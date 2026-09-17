# Structure reviewer checklist

Cite file:line or a count with the command used.

1. Describe the folder layout and the naming conventions actually in use (server-only files, API layer, feature folders). Are they applied consistently? List exceptions.
2. List every source file over 700 lines with its line count and what it does. Suggest a split for the three largest.
3. Find dead code: modules never imported (grep each file's basename across `src`), exported functions with zero importers (sample the largest lib files). Report counts and the notable ones.
4. Find duplicated logic (two implementations of the same thing: two email senders, two slot builders, two config sources for prices).
5. Legacy or old-brand naming: previous product names, old domains in URLs/emails, placeholder site URLs. Count files affected.
6. Circular imports (use `npx madge --circular src` if available; otherwise note "not run").
7. Two or more data-access styles coexisting without a documented rule (direct client DB calls plus server functions)? Describe.
8. Tests: count test files and tests, what they cover, whether a `test` script exists, whether CI config exists.
9. Docs: is the README boilerplate? Is there a setup guide, `.env.example`, architecture notes, plan/decision records? Can a new developer run it from the docs alone?
10. Environment variable inventory: every `process.env.X` / `import.meta.env.X` / `os.environ` name, where it is read, and whether it is documented anywhere.
11. Repo hygiene: committed `.env`, logs, screenshots, build output; `.gitignore` gaps; lockfile vs package manager mismatch.
12. Assets or services that tie the project to a specific host (CDN paths, platform-specific auth brokers, managed services with no export path). Name them as portability risks.
