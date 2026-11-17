# Frontend

React + TypeScript + Vite. **Empty until Week 3.** It will be generated with Vite's official scaffolding command. Check the current Vite docs for the exact command at that point.

Planned structure under `src/` (added in Week 3):

```
src/
├── pages/        # Landing, Assessment, Results, SchemeDetail, Browse, Methodology
├── features/
│   ├── assessment/   # question flow, conditional questions, progress
│   └── results/      # result groups, scheme cards, explanations
├── components/   # shared, accessible UI pieces
├── services/     # API client
└── types/        # TypeScript types matching the API schemas
```

Answers live in component state only. Nothing goes in `localStorage` (ADR 0001).
