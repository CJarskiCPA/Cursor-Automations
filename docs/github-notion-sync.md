# Consolidated GitHub → Notion sync

One Cursor Automation refreshes every GitHub-backed Notion catalog. One-way only: **GitHub → Notion**.

This replaces the old separate automations:

- Sync Cursor Automations to Notion
- Sync Coding Agent Skills Library to Notion

## Triggers

Push to `main` on any of:

| Repo | Notion destination |
|------|--------------------|
| `CJarskiCPA/coding-agent-skills-library` | Coding Agent Skills Library database |
| `CJarskiCPA/Cursor-Automations` | Cursor Automations parent page + library database |
| `CJarskiCPA/ai-employee-toolkit` | Shared skills database (inline on AI Employee Toolkit; filter by Department) |

## Shared skills database (ai-employee-toolkit)

Lives **inline** on the AI Employee Toolkit page (Notion title still **Marketing**). Properties include **Department**: Operations, Finance, Sales, People, HR, Marketing. Do not create separate department databases.

## Create / replace the automation

| Setting | Value |
|---------|--------|
| Name | Sync GitHub repos to Notion |
| Trigger | GitHub push → the three repos above, branch `main` |
| Tools | Notion MCP |
| Instructions | Paste from [`automations/github-notion-sync.instructions.md`](../automations/github-notion-sync.instructions.md) |
| Repo write / open PR | Off |

After saving, delete or disable the two old sync automations so only this one remains.

## Edit workflow

1. Change files in the relevant GitHub repo (and its `catalog.json` when needed).
2. Push to `main`.
3. This automation updates only that repo’s Notion targets.
