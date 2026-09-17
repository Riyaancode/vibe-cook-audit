# Dependency reviewer checklist

1. Run the vulnerability audit for the detected package manager; report counts by severity and each high/critical with the dependency chain and whether a fix exists.
2. Outdated packages: list majors behind; flag frameworks pinned to beta/RC/canary; flag mismatched versions within one ecosystem (e.g. three packages from the same vendor pinned to different minors).
3. Lockfile: present? Matches the package manager used in scripts/CI? Multiple lockfiles?
4. Duplicated or overlapping libraries (two date libraries, two HTTP clients, two state managers, two email senders).
5. Heavy dependencies used in one place (spreadsheet parsers, PDF, canvas, ML) — are they lazy-loaded?
6. Deprecated or abandoned packages (last publish > 2 years, deprecation notice on install).
7. License flags: copyleft licenses in a proprietary product.
8. Supply-chain hygiene: `minimumReleaseAge` or equivalent, install scripts allowed, `overrides`/`resolutions` explaining why.
9. Runtime/version floors: Node/Python version specified (`engines`, `.nvmrc`, `runtime.txt`) and matched by CI/hosting?
10. Build tool and framework versions vs latest stable; note any breaking-change migration due soon.
