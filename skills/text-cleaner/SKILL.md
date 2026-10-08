---
name: text-cleaner
description: >-
  Use when the user wants their OWN text or files cleaned of hidden Unicode
  (zero-width spaces, BOM, soft hyphens, stray bidi controls, odd spaces, tag or
  private-use characters) and leftover chatbot citation tokens (citeturn…,
  【n†source】, oaicite, [cite: n], utm_source=chatgpt.com), or, on request, wants
  author fields blanked in their own DOCX/XLSX/PPTX. Keeps Arabic/Persian/Kurdish
  ZWNJ/ZWJ where needed and RTL text intact. Does NOT strip C2PA Content Credentials,
  AI-provenance watermarks, or copyright/visible watermarks.
---
# Text cleaner (hidden characters and chatbot leftovers)

A deterministic hygiene pass for text the user owns or is authorised to edit. It uses a small Python script (standard library only, nothing to install) and does not change words, facts, numbers or citations.

## What it does NOT do (say this plainly when relevant)
- It does **not** remove or weaken C2PA Content Credentials, SynthID-class or Claude text watermarks, or any other AI-provenance mark, and it does not rewrite text to evade watermarks or AI detectors.
- It does **not** remove visible watermarks, logos, stock-photo or copyright marks, and it never edits images, PDFs, audio or video (the script refuses those files).
- Removing hidden characters does not make text "human-written". It does not replace any AI-use disclosure a journal, university, employer or platform requires.
- Decline requests to clean content the user does not own, or to hide authorship or AI use where disclosure is required.

## Steps
1. Confirm the content is the user's own (or they are authorised), and find out which file or text is involved. Work on a copy: the script writes `NAME.cleaned.EXT` and never edits in place.
2. Save the script below to a temporary file, e.g. `/tmp/clean_text.py`.
3. **Inspect first, change nothing:** `python3 /tmp/clean_text.py FILE --check`. For pasted text: `printf '%s' "$TEXT" | python3 /tmp/clean_text.py - --check` (report on stderr).
4. Show the user the counts grouped by type (removed vs kept). Point out anything surprising, e.g. a right-to-left override (U+202E) inside a document, which can hide or reorder text.
5. Clean: `python3 /tmp/clean_text.py FILE` (or `-o OUT`). For stdin: `... | python3 /tmp/clean_text.py - > cleaned.txt`.
6. Office author fields, **only if the user asks**: `python3 /tmp/clean_text.py report.docx --office-meta`. This blanks `dc:creator`, `cp:lastModifiedBy`, `Company` and `Manager`. Every other part of the package (body, images, any C2PA manifest) is copied byte for byte. Comment and tracked-change author names are not touched; say so if that matters to the user.
7. Re-run with `--check` on the output to confirm it is clean, and give the user the before/after counts and the output path.

## Rules the script applies
- **Always removed:** zero-width space (U+200B; between two Arabic-script letters it becomes a normal space so the word break stays visible), word joiner U+2060, BOM/ZWNBSP U+FEFF, soft hyphen U+00AD, invisible math operators U+2061 to U+2064, Mongolian vowel separator, Hangul fillers, combining grapheme joiner, C0/C1 control characters other than tab and newline, Unicode TAG characters outside flag emoji, and private-use characters (`--keep-pua` keeps them).
- **Legacy bidi controls** U+202A to U+202E (LRE, RLE, PDF, LRO, RLO) are removed (`--keep-bidi` keeps them). Modern isolates U+2066 to U+2069 are kept. LRM, RLM and ALM are kept where they separate text of different directions or sit beside punctuation or digits, and removed only when redundant (between two letters of the same direction, or duplicated).
- **ZWNJ/ZWJ** are kept next to Arabic-script letters (Arabic, Persian, Kurdish), Syriac, Indic scripts, and inside emoji sequences, and removed between Latin letters, where they do nothing visible.
- **Spaces:** en/em/thin/hair and other odd spaces (U+2000 to U+200A, U+205F, U+1680, U+3000) and the narrow no-break space U+202F become a normal space (`--keep-nnbsp` keeps U+202F). The ordinary no-break space U+00A0 is kept. U+2028/U+2029 become line breaks.
- **Chatbot leftovers:** ChatGPT private-use cite blocks and `citeturn0search0`-style tokens, `:contentReference[oaicite:n]{index=n}`, `【n†source】`, Gemini `[cite: n]` / `[cite_start]`, and `utm_source=chatgpt.com|openai|copilot.com|perplexity|gemini` URL parameters. `--no-citations` skips this group.
- **Markdown:** fenced code blocks are left untouched.
- Files are read and written as UTF-8, and line endings are preserved.

## Script
```python
#!/usr/bin/env python3
"""clean_text.py - remove hidden Unicode and chatbot citation leftovers from YOUR OWN text.
Standard library only. Never touches images, PDFs, C2PA Content Credentials, or AI-provenance
watermarks, and never removes visible or copyright watermarks.

  python3 clean_text.py FILE [-o OUT] [--check] [--json]     # .txt .md .html .csv .tex ... (UTF-8)
  python3 clean_text.py - < in.txt > out.txt                 # stdin -> stdout, report on stderr
  python3 clean_text.py FILE.docx --office-meta [-o OUT]     # blank author fields only (opt-in)
Options: --keep-bidi (keep U+202A-202E)  --keep-nnbsp  --keep-pua  --no-citations
"""
import argparse, json, re, sys, unicodedata, zipfile, os

NAMES = {0x200B: "ZERO WIDTH SPACE", 0x2060: "WORD JOINER", 0xFEFF: "BOM / ZWNBSP", 0x00AD: "SOFT HYPHEN",
         0x180E: "MONGOLIAN VOWEL SEPARATOR", 0x2061: "INVISIBLE FUNCTION APPLICATION", 0x2062: "INVISIBLE TIMES",
         0x2063: "INVISIBLE SEPARATOR", 0x2064: "INVISIBLE PLUS", 0x115F: "HANGUL CHOSEONG FILLER",
         0x1160: "HANGUL JUNGSEONG FILLER", 0x3164: "HANGUL FILLER", 0xFFA0: "HALFWIDTH HANGUL FILLER",
         0x034F: "COMBINING GRAPHEME JOINER"}
ALWAYS = set(NAMES)                                   # invisible, never needed in prose
BIDI_LEGACY = {0x202A: "LRE", 0x202B: "RLE", 0x202C: "PDF", 0x202D: "LRO", 0x202E: "RLO"}
MARKS = {0x200E: "LRM", 0x200F: "RLM", 0x061C: "ALM"}  # kept unless redundant
JOINERS = {0x200C: "ZWNJ", 0x200D: "ZWJ"}             # kept where a script/emoji needs them
ODD_SPACES = set(range(0x2000, 0x200B)) | {0x205F, 0x1680, 0x3000}   # -> normal space (NBSP U+00A0 is kept)
NNBSP = 0x202F
LINESEP = {0x2028: "\n", 0x2029: "\n\n"}

def joining_script(ch):
    """True for scripts where ZWNJ/ZWJ change shaping (Arabic, Persian, Kurdish, Syriac, N'Ko, Indic...)."""
    if not ch: return False
    o = ord(ch)
    return (0x0600 <= o <= 0x08FF or 0xFB50 <= o <= 0xFDFF or 0xFE70 <= o <= 0xFEFF or 0x0700 <= o <= 0x07FF
            or 0x0900 <= o <= 0x0DFF or 0x1000 <= o <= 0x109F or 0x1780 <= o <= 0x17FF or 0x10A00 <= o <= 0x10AFF)

def is_emoji(ch):
    if not ch: return False
    o = ord(ch)
    return (0x1F000 <= o <= 0x1FAFF or 0x2600 <= o <= 0x27BF or 0x1F3FB <= o <= 0x1F3FF or o in (0xFE0F, 0x20E3)
            or 0x2190 <= o <= 0x21FF or 0x2B00 <= o <= 0x2BFF)

def strong_dir(ch):
    if not ch: return None
    b = unicodedata.bidirectional(ch)
    return "R" if b in ("R", "AL") else "L" if b == "L" else None

CITATION_PATTERNS = [
    ("chatgpt cite block", re.compile(r"\ue200(?:cite|filecite|navlist|image_group|entity)\ue202[^\ue201]*\ue201")),
    ("chatgpt citeturn token", re.compile(r"\b(?:cite|filecite)?turn\d+(?:search|news|view|file|image|fetch|academia)\d+(?:turn\d+\w+?\d+)*\b")),
    ("oaicite contentReference", re.compile(r":?contentReference\[oaicite:\d+\]\{index=\d+\}")),
    ("【n†source】 reference", re.compile(r"【\d+(?::\d+)?†[^】]{0,80}】")),
    ("gemini [cite] marker", re.compile(r"\s?\[cite(?:_start|_end)?(?::\s*[\d,\s]+)?\]")),
]
UTM = re.compile(r"([?&])utm_source=(?:chatgpt\.com|openai|copilot\.com|perplexity(?:\.ai)?|gemini)(&?)", re.I)

def clean(text, opt):
    stats = {}
    def bump(k, n=1):
        if n: stats[k] = stats.get(k, 0) + n
    if not opt.no_citations:
        for name, rx in CITATION_PATTERNS:
            text, n = rx.subn("", text); bump(name, n)
        def utm(m):
            bump("utm_source tracking parameter")
            return m.group(1) if m.group(2) else ""
        text = UTM.sub(utm, text)
    out, chars = [], list(text)
    for i, ch in enumerate(chars):
        o = ord(ch)
        prev = out[-1] if out else ""
        nxt = chars[i + 1] if i + 1 < len(chars) else ""
        if o == 0x200B and joining_script(prev) and joining_script(nxt) and not unicodedata.combining(nxt):
            bump("ZWSP between Arabic-script letters -> space"); out.append(" "); continue   # keeps the visible word break
        if o in ALWAYS:
            if o == 0xFEFF and prev == "" and i == 0: bump("BOM at start")
            else: bump(NAMES[o])
            continue
        if o in BIDI_LEGACY and not opt.keep_bidi:
            bump("bidi embedding/override " + BIDI_LEGACY[o]); continue
        if o in MARKS:
            p, n = strong_dir(prev), strong_dir(nxt)
            if (p and p == n) or (prev and ord(prev) in MARKS):     # between same-direction letters, or duplicated
                bump("redundant " + MARKS[o]); continue
            out.append(ch); continue
        if o in JOINERS:
            needed = joining_script(prev) or joining_script(nxt) or (o == 0x200D and (is_emoji(prev) or is_emoji(nxt)))
            if needed: bump("kept " + JOINERS[o] + " (script/emoji needs it)", 1); out.append(ch)
            else: bump(JOINERS[o] + " outside Arabic/Indic/emoji")
            continue
        if 0xE0000 <= o <= 0xE007F:                         # tag characters: keep only inside flag emoji sequences
            if prev and (ord(prev) == 0x1F3F4 or 0xE0000 <= ord(prev) <= 0xE007F): out.append(ch)
            else: bump("Unicode TAG character")
            continue
        if 0xE000 <= o <= 0xF8FF and not opt.keep_pua:
            bump("private-use character"); continue
        if o in ODD_SPACES:
            bump("odd space U+%04X -> space" % o); out.append(" "); continue
        if o == NNBSP and not opt.keep_nnbsp:
            bump("narrow no-break space -> space"); out.append(" "); continue
        if o in LINESEP:
            bump("Unicode line/paragraph separator"); out.append(LINESEP[o]); continue
        if unicodedata.category(ch) == "Cc" and ch not in "\t\n\r":
            bump("control character U+%04X" % o); continue
        out.append(ch)
    text = "".join(out)
    if stats.get("gemini [cite] marker") or any(k.startswith("chatgpt") or "oaicite" in k or "†" in k for k in stats):
        text = re.sub(r"[ \t]+([.,،؛:;!?؟])", r"\1", text)   # tidy spaces left before punctuation
        text = re.sub(r"[ \t]{2,}", " ", text)
    return text, stats

def protect_md(text, opt):
    """Leave fenced code blocks untouched in Markdown."""
    parts = re.split(r"(^```.*?^```[^\n]*$)", text, flags=re.S | re.M)
    total, res = {}, []
    for k, p in enumerate(parts):
        if k % 2: res.append(p); continue
        c, s = clean(p, opt); res.append(c)
        for a, b in s.items(): total[a] = total.get(a, 0) + b
    return "".join(res), total

OFFICE = {"docProps/core.xml": [r"(<dc:creator>)[^<]*(</dc:creator>)", r"(<cp:lastModifiedBy>)[^<]*(</cp:lastModifiedBy>)"],
          "docProps/app.xml": [r"(<Company>)[^<]*(</Company>)", r"(<Manager>)[^<]*(</Manager>)"]}

def office_meta(src, dst):
    """Blank author/last-editor/company/manager fields. Every other part (incl. any C2PA manifest) is copied byte-for-byte."""
    stats = {}
    with zipfile.ZipFile(src) as zi, zipfile.ZipFile(dst, "w") as zo:
        for info in zi.infolist():
            data = zi.read(info.filename)
            if info.filename in OFFICE:
                s = data.decode("utf-8")
                for rx in OFFICE[info.filename]:
                    s, n = re.subn(rx, r"\1\2", s)
                    if n: stats[rx.split(">")[0].strip("(<:").split(":")[-1]] = n
                data = s.encode("utf-8")
            zo.writestr(info, data)
    return stats

def main():
    ap = argparse.ArgumentParser(description=__doc__, formatter_class=argparse.RawDescriptionHelpFormatter)
    ap.add_argument("path"); ap.add_argument("-o", "--out")
    ap.add_argument("--check", action="store_true", help="report only, write nothing")
    ap.add_argument("--json", action="store_true")
    ap.add_argument("--office-meta", action="store_true", help="DOCX/XLSX/PPTX: blank author fields (opt-in)")
    for f in ("--keep-bidi", "--keep-nnbsp", "--keep-pua", "--no-citations"): ap.add_argument(f, action="store_true")
    opt = ap.parse_args()
    ext = os.path.splitext(opt.path)[1].lower()
    if ext in (".pdf", ".png", ".jpg", ".jpeg", ".webp", ".gif", ".heic", ".avif", ".tif", ".tiff", ".mp4", ".mov"):
        sys.exit("refused: this tool only cleans text and Office author fields; it never edits images, PDFs, video, "
                 "C2PA Content Credentials or watermarks.")
    if ext in (".docx", ".xlsx", ".pptx"):
        if not opt.office_meta: sys.exit("Office file: pass --office-meta to blank author fields (nothing else is changed).")
        if opt.check: sys.exit("--check is not supported for Office files")
        out = opt.out or opt.path[:-len(ext)] + ".cleaned" + ext
        s = office_meta(opt.path, out); print(json.dumps({"file": out, "removed": s}, ensure_ascii=False)); return
    raw = sys.stdin.read() if opt.path == "-" else open(opt.path, encoding="utf-8", newline="").read()
    cleaned, stats = (protect_md if ext in (".md", ".markdown") else clean)(raw, opt)
    removed = {k: v for k, v in stats.items() if not k.startswith("kept")}
    kept = {k: v for k, v in stats.items() if k.startswith("kept")}
    report = {"chars_before": len(raw), "chars_after": len(cleaned), "removed_or_replaced": removed,
              "kept": kept, "total_changes": sum(removed.values())}
    if opt.path == "-":
        if not opt.check: sys.stdout.write(cleaned)
        print(json.dumps(report, ensure_ascii=False, indent=1), file=sys.stderr); return
    if not opt.check:
        out = opt.out or (os.path.splitext(opt.path)[0] + ".cleaned" + ext)
        open(out, "w", encoding="utf-8", newline="").write(cleaned); report["written"] = out
    print(json.dumps(report, ensure_ascii=False, indent=None if opt.json else 1))

if __name__ == "__main__":
    main()
```
