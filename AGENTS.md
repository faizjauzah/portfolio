# AGENTS.md

Personal portfolio site: React 19 + Vite 8 + Tailwind v4, deployed to GitHub Pages. Single page, no router, no tests.

## Commands

- `npm install` — npm is the package manager (`package-lock.json`; CI uses `npm ci` on Node 20).
- `npm run dev` — dev server at `http://localhost:5173/personal-portofolio/`; the root URL redirects, so use the base path.
- `npm run lint` — ESLint flat config; lints only `**/*.{ts,tsx}` and ignores `dist/`.
- `npm run build` — `tsc -b && vite build`; this is also the typecheck (strict, `noUnusedLocals`). The >500 kB chunk warning from three.js is expected, not an error.
- No test runner or formatter is configured. Don't invent `npm test` or Prettier commands.

## Deployment and base path

- Pushing to `main` runs `.github/workflows/deploy.yml`, which builds and publishes `dist/` to GitHub Pages.
- `base: "/personal-portofolio/"` in `vite.config.ts` is required for deployed asset URLs; note the spelling. Don't change it without updating the Pages setup.
- Public assets must be referenced with `import.meta.env.BASE_URL` (see `src/components/3d/HandModel.tsx`), never as root-absolute `/file` paths.
- `dist/` is generated and gitignored; CI rebuilds it on deploy. Never commit or hand-edit it.

## Architecture

- Entry flow: `src/main.tsx` → `src/App.tsx`, which stacks `src/components/sections/*` (Navbar, Hero, Experience, Projects, Skills, Footer).
- Page copy and data (projects, experience, skills, contact links) are hardcoded inside each section component. Content edits happen there, not in a data layer or CMS.
- Reusable UI primitives live in `src/components/ui/` (`Card`, `Accordion`, `Button`); combine Tailwind classes with `cn()` from `src/lib/utils.ts`.
- Hero 3D is `src/components/3d/Hero3DView.tsx` (react-three-fiber `Canvas`) plus `HandModel.tsx`, which loads `public/hand.obj` via three's `OBJLoader` and replaces its material. Keep it inside `Suspense`.
- In-page links target section ids `experience`, `projects`, `skills`, `contact` (Footer). Use `scrollToSection()` from `src/lib/scroll.ts` (it applies a viewport offset) and keep ids in sync when adding nav links.

## Styling

- Bauhaus design system: `#F0F0F0` background, `#121212` foreground, red `#D02020`, blue `#1040C0`, yellow `#F0C020`; uppercase Outfit type, heavy black borders, hard offset shadows (`bauhaus-shadow-*` or `shadow-[Npx_Npx_0_0_black]`), and square corners by default (pill shapes are explicit exceptions).
- Tokens are declared in `src/index.css` under `@theme`, but components consistently use inline arbitrary hex classes (`bg-[#D02020]`). Follow the existing inline-hex style.
- Tailwind v4 has no `tailwind.config.js`; theme and custom utilities belong in `src/index.css`.
- TypeScript uses `verbatimModuleSyntax`: import types with `import type`.

## Don't trust

- `README.md` is untouched Vite template boilerplate; real page metadata is in `index.html`.
