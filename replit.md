# RDTech Soluções em Informática — Sistema de Orçamentos

Sistema web de gerenciamento de orçamentos, faturas e documentos para RDTech Soluções em Informática.

## Run & Operate

- `pnpm --filter @workspace/rdtech run dev` — run the web app (artifact: rdtech, port via $PORT)
- `pnpm --filter @workspace/rdtech run typecheck` — typecheck the app
- Login: `redkz@admin` / `adminkzy`

## Stack

- React + Vite, TypeScript, Tailwind CSS
- shadcn/ui components, framer-motion, wouter routing
- jsPDF (^4.2.1) for PDF generation
- recharts for charts/reports
- localStorage only (no backend DB)

## Where things live

- `artifacts/rdtech/src/pages/home.tsx` — new OS form
- `artifacts/rdtech/src/pages/historico.tsx` — history, status flow, PDF viewer, ZIP, approval links
- `artifacts/rdtech/src/pages/admin.tsx` — catalog + empresa + signature + security (credentials)
- `artifacts/rdtech/src/pages/relatorio.tsx` — monthly reports with charts
- `artifacts/rdtech/src/pages/inadimplencia.tsx` — overdue AGUARDANDO_PAGAMENTO dashboard
- `artifacts/rdtech/src/pages/busca.tsx` — OS search page (by client name, OS number, serial number)
- `artifacts/rdtech/src/pages/client-view.tsx` — public page (no auth) for client approval/warranty signature
- `artifacts/rdtech/src/pages/login.tsx` — clean login (no prefill) + "Esqueci minhas credenciais" modal
- `artifacts/rdtech/src/lib/pdf.ts` — QuoteData/PDFOptions interfaces + 4 PDF generators (preview mode)
- `artifacts/rdtech/src/lib/storage.ts` — localStorage CRUD + cloud sync via API server
- `artifacts/rdtech/src/lib/settings.ts` — company info (recoveryEmail, defaultAdminSignatureBase64, warrantyDefaultLocation)
- `artifacts/rdtech/src/lib/catalog.ts` — services catalog
- `artifacts/rdtech/src/lib/auth.ts` — login + configurable credentials (rdtech_credentials key)
- `artifacts/api-server/src/routes/rdtech.ts` — cloud sync + client approval endpoints

## Architecture decisions

- localStorage-only storage (no backend) — keeps it zero-dependency deployable
- QuoteData defined in pdf.ts, HistoryEntry extends it in storage.ts
- PDF generators call `getCompanyInfo()` dynamically so admin changes reflect immediately
- 3-step status flow: PENDENTE → AGUARDANDO_PAGAMENTO → APROVADO
- Termo de Garantia uses optional canvas signature (passed as base64 PNG to jsPDF)

## Product

**3-step OS lifecycle:**
1. **Gerar Orçamento** → status PENDENTE (Orçamento PDF)
2. **Gerar Fatura** (from Histórico) → status AGUARDANDO_PAGAMENTO (Fatura + Nota de Serviço PDFs)
3. **Pago ✓** (from Histórico) → status APROVADO (Certificado + Termo de Garantia PDFs, with optional digital signature)

**4 PDF types:** Orçamento, Fatura, Nota de Serviço, Certificado de Garantia, Termo de Garantia (5 actually)

**Admin panel tabs:** Serviços, Combos, Empresa (edits company info used in all PDFs)

**Payment methods:** À VISTA, PIX, CARTÃO (+ link), COMBINADO (+ link + data)

## User preferences

- Login: `redkz@admin` / `adminkzy`
- Company defaults: RDTech Soluções em Informática, responsável Felipe Henrique de Paulo, WhatsApp +55 21 97220-9780

## Approval link one-time password

- Each OS gets `clientApprovalToken` (UUID, route key) + `clientApprovalPassword` (8-char alphanumeric, e.g. `A3KMN7PX`)
- Link format: `/aprovacao/:token?pwd=PASSWORD&type=quote`
- Separate used flags: `clientQuoteApprovalUsed` and `clientWarrantyApprovalUsed` (independent per type)
- Server validates: token exists + password matches + type-specific used flag is false
- When client approves quote (type=quote): server also sets `status = "APROVADO"` automatically
- `/aprovacao/:token` is a public route (no auth required) — handled in App.tsx before auth check

## Gotchas

- `pnpm --filter @workspace/rdtech run build` needs PORT env var — use `typecheck` instead
- localStorage key: `rdtech_history` (entries), `rdtech_settings` (company), `rdtech_auth`, `rdtech_os_counter`, `rdtech_catalog_*`, `rdtech_draft`
- Changing company info in admin takes effect immediately in next PDF generation (no page reload needed)

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
