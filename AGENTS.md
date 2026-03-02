# AGENTS.md

## Project Overview
vite-plugin-monkey@7.x + vue3 (making 外挂式辅助页面 for mitmweb(react spa) 网站)
功能: 大模型请求/响应的可视化
1. openai/gemini/claude 三种api格式
2. 响应包括普通/SSE

## Build/Lint/Test Commands

### Build Commands
```bash
# Build the project (TypeScript check + Vite build)
npm run build

# Preview the build
npm run preview
```

### Development Commands
```bash
# Start dev server (WARNING: Do NOT run this - it will hang)
npm run dev
```

### Type Checking
```bash
# Run TypeScript type checking (via vue-tsc)
npx vue-tsc --noEmit
```

### Testing
**Note:** This project does not have a test framework configured. There are no test scripts in package.json.

However, there are type test files in `src/types/**/*.test.ts` that can be checked via TypeScript:
- `src/types/openai/openai-chat.test.ts`
- `src/types/openai-response/openai-response-request.test.ts`
- `src/types/gemini/gemini-types.test.ts`
- `src/types/claude/cladue-types.test.ts`

To run type checks on these files:
```bash
npx vue-tsc --noEmit
```

## Code Style Guidelines

### Imports
- Use absolute imports with `@/` alias for project files
- Import types with `import type { TypeName } from '@/path/to/types'`
- Import Vue components with `.vue` extension
- Group imports: external packages first, then internal modules, then types

```typescript
// Good
import { computed } from 'vue';
import { hashId } from '@/utils/id/hashId';
import type { OpenaiChatRequest } from '@/types/openai/chat-request';
import SmartViewer from '@/components/content/SmartViewer.vue';
```

### Formatting
- Use 2 spaces for indentation
- Single quotes for strings
- Trailing commas in multi-line objects/arrays
- No semicolons at end of lines (TypeScript/ESLint default)

### Types
- Use TypeScript strict mode (enabled in tsconfig)
- Define types in `src/types/` directory by API standard (openai, gemini, claude, openai-response)
- Use `type` for type aliases, `interface` for object shapes
- Use `as const` for literal types
- Use type guards for discriminated unions
- Prefer `unknown` over `any` for untyped data

### Naming Conventions
- Components: PascalCase (e.g., `OpenaiRequestView.vue`)
- Composables: useCamelCase (e.g., `useCounter`)
- Utilities: camelCase (e.g., `hashId`)
- Types: PascalCase (e.g., `OpenaiChatRequest`)
- Constants: UPPER_SNAKE_CASE (e.g., `STORAGE_KEY_PREFIX`)
- Props: camelCase in template, PascalCase in type definition

### Vue Components
- Use `<script setup>` syntax
- Define props with `defineProps<Props>()` interface
- Define emits with `defineEmits<{}>()`
- Use `computed` for derived state
- Use `ref` for reactive primitive values
- Use `withDefaults` for optional props with defaults

### Error Handling
- Use try-catch for JSON parsing and async operations
- Return error objects with `success: false` pattern in transfer services
- Display user-friendly error messages in UI
- Log errors with `logger` from `@logtape/logtape`

### Component Structure
- Single Responsibility Principle: Each component handles one logical concern
- Props: Pass data via props, not global state
- Storage: Use localStorage for generic state, sessionStorage for detailed state
- IDs: Use `hashId()` utility for generating stable IDs from data
- Collapsible sections: Use `CollapsibleSection` component with storage keys

### File Organization
```
src/
├── components/
│   ├── llm/           # LLM-specific components
│   │   ├── openai/    # OpenAI components
│   │   ├── gemini/    # Gemini components
│   │   ├── claude/    # Claude components
│   │   └── openai-response/  # OpenAI Responses API components
│   ├── container/     # Layout containers (CollapsibleSection, Tabs)
│   ├── content/       # Content viewers (JsonViewer, SmartViewer)
│   └── common/        # Reusable UI components
├── types/             # TypeScript type definitions
├── lib/               # Core logic (pipeline, transfer services)
├── store/             # Pinia stores
├── utils/             # Utility functions
└── pages/             # Route pages
```

### Important Notes
- **DO NOT run `npm run dev`** - it will hang indefinitely
- **DO NOT modify files in `./src/old/`** - they are for reference only
- **DO add comments** when editing or adding code (short and reasonable)
- **Reasoning = thinking** - different names for the same concept in different standards

### Transfer Service Pattern
When creating new API standard handlers:
1. Define types in `src/types/{standard}/`
2. Create transfer service in `src/lib/transfer/{standard}-transfer-service.ts`
3. Register in `src/lib/transfer/unified.ts`
4. Create view components in `src/components/llm/{standard}/`
5. Register in `src/components/llm/ViewDashboardProxy.vue`

### Component Props Pattern
```typescript
interface Props {
  data: SpecificType;  // Type-specific, not generic
  path?: string;       // Optional path info
}

const props = defineProps<Props>();
```

### Storage Keys
- Use descriptive prefixes: `openai-msg-`, `openai-response-input-`, etc.
- Use `storage-key` prop on `CollapsibleSection` for state persistence
- localStorage for generic UI state (e.g., expanded sections)
- sessionStorage for detailed state (e.g., specific item expansion)
