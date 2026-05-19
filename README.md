# prompt-template

Module #2 — Prompt templating cho LLM applications.

Chuyển đổi template có `{{variable}}` thành `Message[]` tương thích trực tiếp với [llm-client](https://github.com/longiq/llm-client).

## Cài đặt

```bash
npm install
```

## Sử dụng

```typescript
import { PromptTemplate } from "prompt-template";
import { createClient } from "llm-client";

const template = new PromptTemplate({
  system: "Bạn là trợ lý {{role}}.",
  user: "{{question}}",
  examples: [
    { user: "2+2 bằng mấy?", assistant: "4" },
  ],
});

// Kiểm tra biến cần thiết
console.log(template.variables); // ["role", "question"]

// Render thành Message[]
const messages = template.render({
  role: "hữu ích",
  question: "TypeScript là gì?",
});

// Dùng với llm-client
const client = createClient("openai", { apiKey: process.env.OPENAI_API_KEY });
const result = await client.complete({ model: "gpt-4o-mini", messages });
console.log(result.content);
```

## API

### `new PromptTemplate(config)`

| Field | Type | Bắt buộc | Mô tả |
|---|---|---|---|
| `user` | `string` | Có | User prompt, có thể chứa `{{variable}}` |
| `system` | `string` | Không | System prompt, có thể chứa `{{variable}}` |
| `examples` | `FewShotExample[]` | Không | Các cặp ví dụ tĩnh (không interpolate) |

### `template.render(vars?)`

Trả về `Message[]` sẵn sàng cho `llm-client`. Throw `TemplateRenderError` nếu thiếu variable.

### `template.withExamples(examples)`

Trả về `PromptTemplate` mới với examples bổ sung. Không thay đổi template gốc.

### `template.variables`

`ReadonlyArray<string>` — danh sách tất cả biến cần thiết, tính tại constructor.

## Error handling

```typescript
import { TemplateRenderError } from "prompt-template";

try {
  template.render({ role: "helpful" }); // thiếu "question"
} catch (e) {
  if (e instanceof TemplateRenderError) {
    console.log(e.missingVariables); // ["question"]
  }
}
```

## Commands

```bash
npm test           # chạy test
npm run typecheck  # type-check
npm run build      # compile sang dist/
```
