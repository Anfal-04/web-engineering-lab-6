# Web Engineering Lab: Lab 06

Rendering Strategies and the React Mental Model.

## Setup (one command)

Install Node.js LTS first, then in the project root run:

```
npm run setup
```

## Part A: rendering demo

```
node demo/build-static.mjs
node demo/server.mjs
```

Open http://localhost:3000 and visit `/csr`, `/ssr`, `/ssg`, `/isr` and `/stream`. Stop with Ctrl+C.
(Shortcut: `npm run demo` does both commands.)

## Part B: React app

```
cd app
npm run dev          # development server
npm run build        # type-check and build
npm run preview      # serve the production build
```

## Files

- `RENDERING-NOTES.md`: test results and strategy choices
- `AI-USAGE.md`: AI use declaration
- `evidence/`: screenshot and Lighthouse report
