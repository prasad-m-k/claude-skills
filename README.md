# claude-skills

A collection of open-source skills for [Claude](https://claude.ai) — installable instruction files that teach Claude how to handle specific tasks correctly, on the first try.

> Built by [prasad-m-k](https://github.com/prasad-m-k). Contributions welcome.

---

## What are Claude skills?

A skill is a small Markdown file you install into Claude via **Settings > Skills**. It tells Claude exactly how to handle a specific type of task: which libraries to use, in what order, and what pitfalls to avoid.

Skills use **progressive disclosure** — only a short description (~60 tokens) lives in Claude's context permanently. The full instructions load only when triggered. This means faster responses, fewer failed attempts, and lower token usage.

---

## Skills

| Skill | What it does | Install |
|---|---|---|
| [heic](./heic) | Work natively with iPhone HEIC/HEIF photos — convert, resize, crop, batch process | [heic.skill](./heic/heic.skill) |

---

## How to install a skill

1. Click the skill link in the table above
2. Download the `.skill` file
3. In Claude, go to **Settings > Skills**
4. Click **Upload Skill** and select the file

Claude will now automatically use the skill whenever it's relevant — no special prompting needed.

---

## How to contribute

Found a file format or workflow Claude handles poorly? That's a skill waiting to be written.

1. Fork this repo
2. Create a folder: `your-skill-name/`
3. Add a `SKILL.md` following the structure in any existing skill
4. Package it: `zip -r your-skill-name.skill your-skill-name/`
5. Place the `.skill` file inside the folder alongside `SKILL.md`
6. Open a pull request with a one-line description of what the skill fixes

---

## Repo structure

```
claude-skills/
├── heic/
│   ├── SKILL.md          # Skill instructions (human-readable)
│   └── heic.skill        # Packaged skill file (upload this to Claude)
└── README.md
```

Each skill folder contains both the raw `SKILL.md` (readable on GitHub) and the `.skill` file (ready to install).

---

## License

MIT
