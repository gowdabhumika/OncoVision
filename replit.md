# CancerScan AI

An AI-powered early cancer detection platform for radiologists and oncologists. Upload MRI scans, receive instant AI analysis with confidence scores, and generate formal diagnostic reports.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the Express API server (port 8080)
- `ML_PORT=5100 python services/flask-ml/app.py` — run the Flask ML service (port 5100)
- `pnpm --filter @workspace/cancer-detection run dev` — run the React frontend (via workflow)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS v4 + shadcn/ui + wouter
- API: Express 5 (gateway + business logic)
- ML: Python Flask + Pillow + NumPy (simulated CNN model for MRI analysis)
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — API contract (source of truth)
- `lib/db/src/schema/` — DB schema: `scans.ts`, `reports.ts`
- `artifacts/api-server/src/routes/` — Express routes: `scans.ts`, `reports.ts`, `dashboard.ts`
- `services/flask-ml/app.py` — Flask ML service with simulated TensorFlow CNN model
- `artifacts/cancer-detection/src/` — React frontend
  - `pages/` — Dashboard, ScansList, UploadScan, ScanDetail, ReportsList, ReportDetail
  - `components/ui/badges.tsx` — PredictionBadge, SeverityBadge, StatusBadge
  - `components/layout/AppLayout.tsx` — Sidebar navigation

## Architecture decisions

- Flask ML service runs on port 5100 (separate from the Express gateway on 8080). Express proxies ML inference requests to Flask via `fetch()` in route handlers. Flask is not exposed through the shared proxy.
- ML model is simulated using image pixel statistics (mean/std from a 64×64 grayscale conversion) to seed a deterministic RNG — results are reproducible for the same image. In production, swap `analyze_image_data()` with a real TF Lite model call.
- The Express API server applies a graceful fallback: if Flask is unreachable, predictions default to "normal" with 0.72 confidence so uploads never hard-fail.
- OpenAPI-first contract: all types generated via Orval. Frontend uses only generated hooks from `@workspace/api-client-react`.

## Product

- **Dashboard** — system overview with total scans, critical case alerts, average AI confidence, detection breakdown
- **Scans Database** — searchable/filterable list of all MRI scans with status, prediction, and severity badges
- **Upload Scan** — drag-and-drop MRI image upload with patient metadata form; triggers AI analysis immediately
- **Scan Detail** — full AI result view: confidence radial gauge, prediction, severity, cancer type, radiologist notes, generate report action
- **Diagnostic Reports** — list and detail view for generated reports, formatted for clinical printing

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Always run `pnpm run typecheck:libs` after modifying `lib/db/src/schema/` before running `pnpm --filter @workspace/api-server run typecheck` — the API server depends on compiled lib declarations.
- The Flask ML service must be running before the API server for ML inference to work. The Express server has a fallback but predictions will be static defaults if Flask is down.
- Do not change `info.title: Api` in `openapi.yaml` — it controls generated filenames.
- CSS custom property values use space-separated HSL (no `hsl()` wrapper).

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
