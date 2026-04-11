# Tech & Res — Victoria 3 Mod

A Victoria 3 mod that extends the technology tree, production methods, and related mechanics into later eras (atomic age, digital age, and beyond).

## Key paths

| What | Path |
|---|---|
| **This mod** | `C:\Users\User\Documents\Paradox Interactive\Victoria 3\mod\Tech&Res\` |
| **Vanilla game files** | `C:\Program Files (x86)\Steam\steamapps\common\Victoria 3\game\` |
| **Reference mod (Workshop)** | `C:\Program Files (x86)\Steam\steamapps\workshop\content\529340\3385002128\` |

When you need to check how vanilla defines something, read from the vanilla path. The Workshop mod is useful as a reference for well-coded custom mechanics.

## Workflow Rules

1. **No Git.** Do not run any git commands. Version control is handled outside this environment.
2. **Use skills.** Before creating or editing scripted content (technologies, production methods, events, buildings, etc.), read the matching skill file in `.claude\skills\` to follow the mod's conventions and templates.
3. **Vanilla as ground truth.** When in doubt about syntax, scopes, or modifier keys, verify against the vanilla game files rather than guessing.
4. **Verify after editing.** After modifying a script file, confirm that referenced keys (goods, techs, modifiers, PMs, PMGs, icons) actually exist in the mod or vanilla.

## Sub-Agents
You can call the following sub-agents to help with specific tasks:
- `workspace-logger`: for logging completed game-related tasks in `LOG.md`.
- `localization-writer`: for writing and improving English localization while preserving script correctness and mod terminology.