# **Aura API Client: A Modern, Cross-Platform API Toolkit**

---

Aura is a modern API client built from the ground up with Dart and Flutter to provide a native, performant, and aesthetically pleasing experience across all platforms. In a market where tools have become increasingly complex and resource-intensive, Aura fills the "Developer Experience Gap" by delivering a tool that is finely tuned to the modern developer's workflow: fast, intuitive, and focused.1  
Whether you're on Windows, macOS, Linux, in a web browser, on your phone, or in the terminal, Aura provides a consistent and powerful toolkit for all your API development needs.

## **The Aura Advantage: Unifying the Testing Spectrum**

The modern API development workflow is fragmented. Developers use interactive GUI clients like Postman or Insomnia for building, debugging, and functional testing, but must then switch context to a completely different, command-line-driven tool like Artillery.io for performance and load testing.1 This context switch requires manually translating functional tests into scripted performance scenarios, creating friction and slowing down the development cycle.  
Aura's core mission is to eliminate this friction by **unifying the API testing spectrum**.  
With Aura, you can craft a request, add functional assertions, and then seamlessly "promote" that functional test into a full-scale performance test scenario using our integrated, **Artillery.io-compatible load testing engine**. This creates a powerful, cohesive workflow that does not exist in any other mainstream tool, representing a significant strategic advantage and a fundamental rethinking of the API development process.1

## **Core Features**

Aura is packed with the features you need to work efficiently with any API.

* **Comprehensive Protocol Support**:
    * REST (HTTP/HTTPS)
    * GraphQL (including schema introspection)
    * gRPC (with dynamic .proto file loading)
    * WebSockets
    * Server-Sent Events (SSE)
    * Socket.IO
    * MQTT 1
* **Powerful Organizational Tools**:
    * **Workspaces**: Isolate different projects or teams.1
    * **Collections**: Group related requests with support for nested folders.1
    * **Environments**: Manage sets of variables for different deployments (e.g., dev, staging, prod) with support for global, workspace, and collection-level scoping.1
* **Intuitive Request Building**:
    * A clean, tabbed interface for managing URL parameters, headers, and authorization.1
    * Advanced body editor with support for JSON, XML, Form-Data, x-www-form-urlencoded, and raw text, complete with syntax highlighting.1
* **Robust Authentication**:
    * Extensive support for common and advanced authentication methods, including OAuth 1.0/2.0, AWS Signature v4, API Keys, Bearer Token, Basic Auth, and more.1
    * Secure, on-device storage for all sensitive credentials using platform-native secure enclaves.1
* **Integrated Testing & Scripting**:
    * Write pre-request and post-request scripts using **Dart**, providing a single, powerful language across the entire application stack.1
    * A rich assertion library based on Dart's built-in matcher package for writing expressive and type-safe tests.1
* **Seamless Interoperability**:
    * Easily import your existing work from Postman, Insomnia, OpenAPI/Swagger (v2/v3), and cURL commands to get started in seconds.1

## **Platform Support**

Aura is designed to be a truly ubiquitous tool, available wherever you work.

| Platform | Status |
| :---- | :---- |
| Desktop (Windows/macOS/Linux) | 🏗️ In Development |
| Web | 🏗️ In Development |
| Mobile (iOS/Android) | 🏗️ In Development |
| CLI (Command-Line Interface) | 🏗️ In Development |

## **Getting Started**

To get started with Aura, download the latest release for your platform from the **([https://github.com/your-repo/aura-client/releases](https://github.com/your-repo/aura-client/releases))**.  
For detailed information on all features and advanced usage, please refer to our official documentation (coming soon).

## **Contributing & Community**

Aura is an open-source project built on the belief that developer tools are better when they are built by the community they serve. We welcome contributions of all kinds, from bug reports and feature suggestions to code contributions.

* **Architectural Guide**: Before contributing code, please read our CODE.md file to understand our architecture and coding standards.
* **Project Roadmap**: To see what we're working on and where you can help, check out our PLAN.md file.
* **Bug Reports & Feature Requests**: Please submit these through our [GitHub Issues](https://github.com/your-repo/aura-client/issues).
* **Pull Requests**: We welcome PRs\! Please ensure your code adheres to the project standards and includes relevant tests.

Join our community to ask questions, share ideas, and connect with other developers:

* ([https://github.com/your-repo/aura-client/discussions](https://github.com/your-repo/aura-client/discussions))
* Discord (Link coming soon)
