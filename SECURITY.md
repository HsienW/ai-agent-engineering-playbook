# 安全政策（Security Policy）

請私下向 repository maintainers 回報 security issues。

## 敏感資訊（Sensitive Information）

請勿提交：

- API keys、tokens、passwords 或 credentials。
- Private repository paths 或 internal hostnames。
- 會識別 private system 的 customer data、user data、logs、traces 或 screenshots。
- 含有 confidential business logic 的 prompts 或 tool outputs。

## 安全範例（Safe Examples）

請使用 placeholders，例如：

```text
EXAMPLE_API_KEY
https://api.example.com
src/features/example.ts
```

請勿在 examples 中使用逼真的 secret prefixes 或 values。
