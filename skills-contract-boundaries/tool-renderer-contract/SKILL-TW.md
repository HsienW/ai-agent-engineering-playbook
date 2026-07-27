---
name: tool-renderer-contract
description: 設計與審查 structured tool-result rendering。用於 rendering tool calls、tool messages、structured tool outputs、status cards、fallback JSON/text displays、parser boundaries、display status mappings，或 unknown tool results。
---

# Tool Renderer Contract

## Skill 介面

- 名稱：tool-renderer-contract。
- 描述：為 tool calls、tool messages、structured tool outputs、status cards、fallback JSON 或 text displays、parser boundaries、display status mappings 與 unknown tool results 設計與審查 structured tool-result rendering。
- 參數：Tool result schema、raw message content、parser boundary、display status mapping、known specialized renderers、fallback rendering behavior、sensitive field policy，以及 verification cases。
- 執行指令：當 UI 要 render tool output 時使用此 skill。將 raw output 視為 untrusted data，驗證最低限度的 structured fields，透過 closed display union map status，為 unknown tools 提供 safe fallbacks，並驗證 malformed、future-version 與 sensitive-data cases。

Tool rendering 應由 schema 驅動，並能承受 unknown tools。UI 不得信任 raw tool output 是 display-ready data。

## Layers

1. Message container：負責 message grouping，以及 tool call metadata 與 tool output 的關聯。
2. Generic tool renderer：parse common metadata、display status，並在可用時選擇 specialized renderer。
3. Specialized renderer：render known tool result schema。
4. Fallback renderer：安全呈現 unknown JSON、text 或 parse failures。

## Structured Result Shape

使用可被 specialized 的 generic shape：

```ts
type ToolResult<TStatus extends string, TData = unknown> = {
  schemaVersion: string;
  tool: string;
  status: TStatus;
  data?: TData;
  summary?: string;
  errorCode?: string;
};
```

Frontend parser 應接受 `unknown` 或 raw message content，驗證 minimum fields，並回傳 typed result 或 `undefined`。

## Status Mapping

將 tool status map 到 closed display status union：

```ts
type ToolDisplayStatus =
  | 'running'
  | 'success'
  | 'needs_input'
  | 'not_found'
  | 'denied'
  | 'error'
  | 'timeout'
  | 'cancelled'
  | 'unknown';
```

針對 labels、icons、colors 與 ARIA text 使用單一 `Record<ToolDisplayStatus, DisplayConfig>`。不要把 status rendering 分散在 component branches。

## Safety Rules

- 將 tool output 視為 untrusted。
- 除非經 trusted policy sanitize，否則絕不 render raw HTML from tool output。
- 絕不暴露 stack traces、credentials、provider secrets 或 raw internal payloads。
- 不要從 user-visible text 推論 status。
- 不要 hardcode 單一 tool result 作為唯一 rendering path。
- Unknown tools 必須有 safe fallback。

## Parser Rules

- 驗證 `schemaVersion`、`tool` 與 `status`。
- 只在安全時為 diagnostics 保留 unknown fields。
- 對 unknown minor schema versions 保持 forward compatible。
- 提供 unknown-status fallback。
- 將 type assertions 放在 runtime checks 後方。

## Verification

測試 known structured results、unknown tool results、malformed JSON、plain text output、unknown status、future schema versions、error envelope rendering，以及 sensitive field redaction。
