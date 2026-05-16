# Init

Create a fresh wiki.

## Steps

1. **Resolve path** per SKILL.md:
   - If user passed `--path <dir>`, use it.
   - Else ask Obsidian for the vault: `obsidian eval code="app.vault.adapter.basePath"` (strip `=> ` prefix), then default to `<vault>/Wiki/`.
   - If the vault can't be resolved, fall back to `./wiki/` and tell the user.
2. **If the path already contains a `schema.md`**, stop — the wiki exists. Tell the user; do not overwrite.
3. **Set the purpose** in `schema.md`:
   - If the user gave a purpose in their message, use it.
   - Else, infer a one-liner from the first source being ingested (if any).
   - Else, write a generic default: *"Personal knowledge wiki. Update this Purpose line once the wiki's focus becomes clear."*
   Don't pause to ask the user for a purpose — `schema.md` is editable; they can refine it later.
4. **Create the tree**:
   ```
   <wiki-root>/
     schema.md      ← copy from assets/schema-template.md; fill Purpose per step 3
     index.md       ← `# Index\n\n_No pages yet._\n`
     pages/         ← empty
     refs/          ← empty citeable provenance records
     raw/           ← empty immutable source files/packages
   ```
5. **Report**: print the wiki root path and tell the user they can now just say "add this paper to my wiki" or "what does the wiki say about X" — no subcommand required.

6. **Commit** if the vault is git-tracked. The wiki uses `git log` as its chronological history; the initial commit is the wiki's birth marker. If the vault isn't git-tracked, mention this once so the user can decide whether to init a repo.

## Don't

- Don't pre-populate page conventions. The schema's "Page conventions" section starts empty; ingest will propose entries once shapes recur in actual use.
- Don't generate placeholder pages.
