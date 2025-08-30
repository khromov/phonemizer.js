# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

- **Build**: `npm run build` - Compiles source using Rollup (generates both CJS and ESM bundles) and TypeScript (generates type declarations)
- **Test**: `npm test` - Runs Vitest test suite
- **Format**: `npm run format` - Formats code using Prettier

The build process:
1. Cleans `dist` and `types` directories
2. Runs Rollup to bundle `src/phonemizer.js` into both CommonJS (`dist/phonemizer.cjs`) and ES modules (`dist/phonemizer.js`)
3. Runs TypeScript compiler to generate type declarations from JSDoc comments

## Architecture

This is a JavaScript library that provides text-to-phoneme conversion using eSpeak NG via WebAssembly.

### Core Components

- **`src/phonemizer.js`**: Main API with two exports:
  - `phonemize(text, language)`: Converts text to phonemes using specified language/voice
  - `list_voices(language?)`: Lists available voices, optionally filtered by language
  
- **`src/espeakng.worker.js`**: Large (~590KB) WebAssembly worker file containing the compiled eSpeak NG engine

- **`data/espeakng.worker.data`**: Binary data file (~890KB) required by the eSpeak NG WebAssembly module

### Key Design Patterns

- **Worker initialization**: Uses a promise-based lazy initialization pattern with `workerPromise` to ensure the WebAssembly module is ready before use
- **Voice caching**: Builds and caches a list of supported voices and language identifiers on first use via `initCache`
- **Language filtering**: Only supports English variants (configurable via `SUPPORTED_LANGUAGES` array)
- **Error handling**: Validates language identifiers against supported voices before processing

### Module System

- **Dual package exports**: Supports both ES modules and CommonJS via package.json exports
- **Type definitions**: Generated automatically from JSDoc comments in source files
- **CDN ready**: Can be imported directly from jsdelivr/unpkg

### Testing

- Uses Vitest for testing
- Tests cover both API functions with expected phoneme outputs for different English variants
- Includes error cases (unsupported languages)
- Test data includes expected phoneme outputs for comparison with eSpeak NG CLI results