# CLAUDE.md

This file provides guidance to AI agents when working with code in this repository.

## Project Overview

Pafiso-ts is a TypeScript library for generating Paging, Filtering, and Sorting query parameters for REST requests. It produces a serialization format compatible with the [Pafiso .NET library](https://github.com/fuji97/pafiso).

## Commands

- **Build:** `npm run build` (uses tsup, outputs CJS + ESM + declarations to `dist/`)
- **Test (watch):** `npm test` (vitest in watch mode)
- **Test (single run):** `npm run test:run`
- **Type check:** `npm run typecheck`
- **Lint:** `npm run lint` (eslint on `src/**/*.ts`)
- **Pre-publish check:** `npm run prepublishOnly` (typecheck + test + build)

There is a single test file: `src/index.test.ts`.

## Architecture

All source code lives in `src/`. The library has four core modules, each following the same pattern: a data class, a fluent builder class, and a factory function.

| Module | Class | Builder | Factory | Purpose |
|--------|-------|---------|---------|---------|
| `filter.ts` | `Filter` | `FilterBuilder` | `filter()` | Filter conditions with field(s), operator, value, case sensitivity |
| `sorting.ts` | `Sorting` | `SortingBuilder` | `sorting()` | Sort specifications with property and order |
| `paging.ts` | `Paging` | `PagingBuilder` | `paging()` | Pagination via skip/take |
| `search-parameters.ts` | `SearchParameters` | `SearchParametersBuilder` | `search()` | Combines filters, sortings, and paging |

- `types.ts` — Shared types: `FilterOperator`, `SortOrder` (const object + type pattern), `Dictionary` (= `Record<string, string>`)
- `index.ts` — Re-exports everything from the modules above

Each data class has `toDictionary()` and `static fromDictionary()` for serialization/deserialization. `SearchParameters` additionally has `toQueryString()`, `toURLSearchParams()`, and corresponding `from*` static methods.

## Serialization Format

Query parameters use bracket notation: `filters[0][fields]`, `filters[0][op]`, `filters[0][val]`, `sortings[0][prop]`, `sortings[0][ord]`, `skip`, `take`. This must stay compatible with the .NET Pafiso library.

## Build Configuration

- TypeScript target: ES2020, strict mode with `noUnusedLocals`, `noUnusedParameters`, `noImplicitReturns`
- tsup bundles to CJS (`dist/index.js`) and ESM (`dist/index.mjs`) with declarations and sourcemaps
- No runtime dependencies
