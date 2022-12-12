# One Creation SDKs

One Creation SDK's:
- [Java SDK](java.md)
- [Python SDK](python.md)
- [C++ SDK](cpp.md)

All of One Creation's functionality is available via REST API, so why would we need an SDK? The short answer is that nobody likes having to write their own API client; SDKs are much easier to get started with, and have the following additional benefits:

Allows for sane defaults (e.g: application/json)
Error handling built-in
Validate required fields
Only known methods/content
Ability to easily expand on functionality based on client needs
Enforce best practices and patterns (e.g: methods are self-documenting)
Safe and optimized with good defaults and limits
Fast and simple adoption (one-line access to API)
Lean on language features (throwing exceptions, strong typing, optionals)
Easy to consume knowledge without in-depth knowledge
Adjust to use-cases (e.g: combining multiple calls into a single method)
Like we said before, nobody likes implementing an HTTP client every time