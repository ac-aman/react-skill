# Contributing to react-skill

Thank you for your interest in contributing to `react-skill`! We welcome contributions from the community to help make AI coding assistants smarter, safer, and more effective at building production-ready React applications across all frameworks.

---

## How You Can Contribute

You can contribute in several ways:

1. **Improve Existing Skill Guidelines**: Enhance instructions, architecture rules, or security practices in [`skills/react-skill/SKILL.md`](./skills/react-skill/SKILL.md).
2. **Add Modular References**: Add technical guides for frameworks (Next.js, Vite, Remix, React Native) or state management (Redux Toolkit, Zustand, TanStack Query) in [`skills/react-skill/references/`](./skills/react-skill/references/).
3. **Add Boilerplate Blueprints**: Add starter templates in [`skills/react-skill/templates/`](./skills/react-skill/templates/).
4. **Add New Bundled Skills**: Add new complementary agent skills inside the `skills/` directory.

---

## Development Workflow

### 1. Fork & Clone
Fork this repository to your GitHub account and clone it locally:

```bash
git clone https://github.com/YOUR-USERNAME/react-skill.git
cd react-skill
```

### 2. Create a Feature Branch
Create a descriptive branch for your changes:

```bash
git checkout -b feature/add-rtk-query-reference
```

### 3. Make Your Changes
- Ensure all `SKILL.md` files include valid **YAML frontmatter**:
  ```yaml
  ---
  name: react-skill
  description: Execution guide for building and architecting React applications.
  license: MIT
  metadata:
    author: "your-username"
    version: "1.0.0"
    tags: ["react", "frontend"]
  ---
  ```
- Follow clean Markdown formatting and standard React best practices.

### 4. Validate Package Payload
Test your changes to ensure all files will be included when published:

```bash
npm pack --dry-run
```

---

## Pull Request Guidelines

1. Push your branch to your forked repository:
   ```bash
   git push origin feature/add-rtk-query-reference
   ```
2. Open a **Pull Request** against the `main` branch of `ac-aman/react-skill`.
3. Provide a clear description of the changes or new skill guidelines added.

---

## Code of Conduct & License

By contributing to `react-skill`, you agree that your contributions will be licensed under the project's [MIT License](./LICENSE).
