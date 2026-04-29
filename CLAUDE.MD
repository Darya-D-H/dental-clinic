## Make sure that all the design features in ../markdown/ are applied after every prompt that I give you

Go through each markdown in that folder and make sure the code is that of the markdowns

## Make sure to update the project_dental.md and CUSTOMIZATION.md after every prompt that I give you



## Claude Skill: 

name: smart-push <br>
description: Automates staging, committing with Conventional Commits, and pushing to GitHub. <br>
disable-model-invocation: true
---

# Smart Push Workflow
Use this skill when the user wants to quickly save and upload their changes to GitHub.

## Process
1. **Check Status**: Use `git status` to see all changed and untracked files.
2. **Stage Changes**: Stage all relevant modified files. Ask the user for confirmation if there are many untracked files.
3. **Generate Message**: Write a commit message following [Conventional Commits](https://conventionalcommits.org) (e.g., `feat: add push skill`).
4. **Commit**: Execute the commit.
5. **Push**: Push the current branch to `origin`.
6. **Optional PR**: If the current branch is not `main`, ask the user if they want to create a Pull Request using the [GitHub CLI (gh)](https://cli.github.com/).

## Guardrails
- Never use `git push --force` unless explicitly told.
- Do not commit sensitive files like `.env` (check `.gitignore`).
