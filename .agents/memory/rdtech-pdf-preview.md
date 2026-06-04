---
name: RDTech PDF preview mode
description: How to get a blob URL from any PDF generator for in-browser viewing
---

All 4 PDF generators accept `opts: PDFOptions = {}` as last parameter.

`PDFOptions`:
```ts
interface PDFOptions {
  preview?: boolean;
  adminSignatureBase64?: string;
  clientSignatureBase64?: string;
}
```

When `opts.preview = true`, the function returns `doc.output("bloburi") as unknown as string` instead of calling `doc.save()`.

**TypeScript quirk:** `doc.output("bloburi")` returns `URL`, not `string`. Cast as `unknown as string` to avoid TS2352 — direct cast `as string` fails compilation.

**How to apply:** Pass the URL to `viewPDF(url, title)` in historico.tsx which sets `pdfViewerUrl` state and renders it in an `<iframe>` inside a Dialog. Download button uses `<a href={url} download>`.
