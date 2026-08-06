# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2 you built Claude Code hooks that block a dangerous action *before* it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). In this assignment you will discover that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

You will build both halves of a real "PR Ready" workflow:

1. A **Git hook that follows fixed rules** — scans staged changes for hardcoded secrets and oversized files and refuses the commit. No AI involved, no guessing, just a rule that gives the same answer every time.
2. A **restricted Claude Code skill** (`/pr-ready`) that reads your staged diff and drafts a Pull Request title, description, and a short list of things worth a second look — the kind of judgment a fixed rule can't make (mixed changes, missing context, unclear intent). The skill never commits, pushes, or opens the PR. You do that yourself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only you act.

---

# Task 0 — Confirm Your Fork and Create a Feature Branch

## Goal

Confirm you are working in your own fork, then create a dedicated branch for this assignment.

### Evidence

#### Screenshot 1 — Output of git remote -v and git branch showing the new branch

![alt text](screenshots/1.png)

---

### Notes

**1. Why create a dedicated branch instead of doing this work on main?**

A dedicated branch keeps new work separate from the `main` branch, preventing unfinished or experimental changes from affecting the stable codebase. It also makes it easier to review, test, and manage changes through a Pull Request before merging. This helps maintain a clean project history and allows multiple developers to work on different features without interfering with each other's work.

---

# Task 1 — Stage a Change With Realistic Risk

## Goal

On your own fork of this repository (the one you've been submitting your DMI work in since onboarding), create a new branch and stage a change that a real reviewer should catch: a hardcoded-looking secret and a leftover debug statement.

### Evidence

#### Screenshot 1 — Output of  `git status` showing the staged file on feature/ai-pr-ready

![alt text](screenshots/2.png)

---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

This assignment uses an obviously fake AWS key to safely demonstrate how secret detection works without exposing any real credentials. Using a real key could create a serious security risk if it were accidentally committed or shared. The fake key allows the pre-commit hook and AI review to detect a credential-like pattern in a realistic but safe way.

---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

### Evidence

#### Screenshot 2 — `hooks/pre-commit` open in VS Code showing the full script

![alt text](screenshots/3.png)

---

#### Screenshot 3 — Output of `git config core.hooksPath` confirming it points to `hooks`

![alt text](screenshots/4.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**

`hooks/pre-commit` is tracked in the repository so every team member can use the same pre-commit hook by configuring `core.hooksPath`. This keeps the safety checks consistent across the project and allows the hook to be version-controlled alongside the code. In contrast, files in `.git/hooks/` are local to a single repository clone, are not tracked by Git, and are not automatically shared with other developers.


---

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

The `hooks/pre-commit` hook intercepts every commit before Git creates it, checking the staged changes for issues such as secret-like patterns or oversized files. `PreToolUse` from Week 2 intercepted AI tool requests before Claude was allowed to execute them, enforcing safety rules on AI actions. They both act as preventive safety gates that stop risky actions before they happen, but they operate at different stages—`pre-commit` protects the Git workflow, while `PreToolUse` controls what the AI is allowed to do.


---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and show the hook rejecting it.

### Evidence

#### Screenshot 4 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

![alt text](screenshots/5.png)

---

### Notes

**1. Which line in `hooks/pre-commit` matched your fake key, and why did it match?**

It matched because the fake AWS access key (`AKIAABCDEFGHIJKLMNOP`) starts with `AKIA` followed by 16 uppercase letters, which matches the regular expression `AKIA[0-9A-Z]{16}`. The `grep` command searched the staged changes and detected the key pattern, causing the pre-commit hook to block the commit.

---

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

No. This hook would not necessarily catch a poorly named variable storing a secret if the value did not match the specific patterns in its regular expression, such as the `AKIA` prefix. This shows the limitation of a fixed-rule check—it only detects patterns it has been explicitly programmed to recognize. It cannot understand context or identify every possible type of secret, which is why additional layers such as AI-assisted review and human judgment are needed.


---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads your staged changes and produces a PR-readiness report and a draft PR description — without writing, committing, or pushing anything itself.

### Evidence

#### Screenshot 5 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

![alt text](screenshots/6.png)

---

#### Screenshot 6 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

![alt text](screenshots/7.png)

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

`/pr-ready` has `Bash` and `Read` because it needs to run safe commands like `git diff --cached` and `git status`, and read the staged changes to analyze them. It does not have `Write` permission because its role is only to review and provide recommendations, not to modify files or perform Git operations. This ensures the developer remains in control of all changes, commits, pushes, and Pull Request creation, reducing the risk of unintended actions.

---

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

They both identified the hardcoded AWS key in the staged changes. However, the `/pr-ready` skill also flagged the leftover debug `echo` statement and highlighted it as something that should be removed before opening a Pull Request. The pre-commit hook focused only on enforcing fixed rules, such as detecting secret-like patterns and oversized files, while `/pr-ready` provided a broader review by identifying issues that might require human attention and suggesting improvements to the PR description.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

### Evidence

#### Screenshot 7 — `git commit` succeeding after the fix (no BLOCKED message)

![alt text](screenshots/8.png)

---

#### Screenshot 8 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

![alt text](screenshots/9a.png)

---

### Notes

**1. What exactly did you change to satisfy the pre-commit hook?**

I removed the hardcoded fake AWS access key (`AWS_ACCESS_KEY_ID=AKIAABCDEFGHIJKLMNOP`) and deleted the debug `echo` statement that displayed the key. After making these changes, I staged the updated file again with `git add scripts/notify.sh`. Since the staged file no longer contained a secret-like pattern, the pre-commit hook allowed the commit to proceed successfully.

---

# Task 6 — Push and Open a Pull Request Using the AI Draft

## Goal

Push your branch and open a real Pull Request, using `/pr-ready`'s drafted title and description as your starting point — read it critically and edit before you use it.

**Important:** Open this Pull Request with base repository set to **your own fork** — not the shared upstream `pravinmishraaws/devops-micro-internship-pravinmishra` repository. This assignment's hook and skill files are your own practice work, not a change meant for the shared class repo.

### Evidence

#### Screenshot 9 — Your Pull Request showing the base repository is your own fork, plus the title and description, with the `/pr-ready` draft visible for comparison (paste it in the PR conversation or your notes below)

![alt text](screenshots/10.png)

---

#### PR Link

https://github.com/MiDevopsEngineer/devops-micro-internship-interviews/pull/1

---

### Notes

**1. What, if anything, did you edit in the AI's drafted PR description before using it? Why?**

I reviewed the AI-generated PR description and made a few edits to improve its accuracy and clarity. I updated the wording to clearly explain that I created a Git pre-commit hook, removed the fake AWS key and debug statement after testing, and verified the changes using the `/pr-ready` skill. I also corrected any details that were incomplete so the PR accurately reflected the work I had actually completed.

---

**2. If you had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

 If I had copied the AI's draft without reviewing it, the Pull Request could have contained inaccurate, incomplete, or misleading information about my changes. This could confuse reviewers, slow down the review process, or cause important details to be overlooked. AI-generated content should always be verified by a human before it is used, especially in collaborative software development.

---

**3. Why does this PR need to target your own fork instead of the shared upstream repository?**

This Pull Request needs to target my own fork because the assignment is completed in my personal copy of the repository, not the shared upstream repository. Opening the PR against my fork prevents assignment files from being submitted to the original project and avoids affecting other contributors' work. It also allows my work to be reviewed independently while keeping the upstream repository clean.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

**Gather** is represented by the steps where information is collected automatically. The pre-commit hook gathers the staged changes using `git diff --cached`, while the `/pr-ready` skill gathers information by running `git diff --cached` and `git status` to inspect the staged files before providing its analysis.

---

**2. Which step(s) represent Analyze?**

**Analyze** is represented by both the pre-commit hook and the `/pr-ready` skill examining the gathered information. The pre-commit hook analyzes the staged changes against fixed rules to detect secret-like patterns and oversized files, while the `/pr-ready` skill analyzes the same changes to identify potential risks such as debug statements, mixed changes, or missing context, and drafts a PR title and description for human review.

---

**3. Which step is Human Act, and why must a human — not Claude — run `git commit`, `git push`, and open the PR?**

**Human Act** is the step where I reviewed the AI's suggestions, fixed the issues in the code, ran `git commit`, pushed the branch with `git push`, and opened the Pull Request. A human must perform these actions because they directly affect the repository and require judgment and accountability. Keeping these actions under human control helps prevent accidental or incorrect changes from being committed, pushed, or merged.

---

**4. Which step is Verify?**

**Verify** is the final step where I confirmed that both safety checks passed after making the fixes. I successfully committed the changes without the pre-commit hook blocking the commit and re-ran the `/pr-ready` skill to confirm it reported a clean risk assessment and generated an updated PR draft. This verified that the issues had been resolved before opening the Pull Request.

---

**5. In one or two sentences: why do you need *both* the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

Both are needed because they serve different purposes. The pre-commit hook enforces fixed, non-negotiable rules such as blocking secret-like patterns and oversized files, while the AI skill provides contextual analysis, drafts the PR description, and identifies issues that fixed rules may miss. Together, they provide stronger protection than either one alone.

---

# Task 8 — LinkedIn Post

## Goal

Publish a LinkedIn post summarizing what you built and what you learned about combining fixed-rule safety checks with AI-assisted review.

### Evidence

#### LinkedIn Post URL

https://www.linkedin.com/posts/millicent-anadi-b7b93a175_dmibypravinmishra-devops-git-ugcPost-7486731464613494784-vPTO/?utm_source=share&utm_medium=member_desktop&rcm=ACoAACmbeQ8Bk7IWiCzNrTecawWZMBxbCmmmG5E

---

## Key Learnings

Add 3-5 bullet points on what you learned this week.

- Learned how to build and configure a shareable Git pre-commit hook using core.hooksPath to enforce consistent safety checks across a team.
- Gained experience creating a Claude Code skill (/pr-ready) with read-only permissions to review staged changes and draft Pull Request content.
- Understood the difference between fixed-rule automation and AI-assisted code review, and why both are valuable in a DevOps workflow.
- Practiced the Agentic Loop (Gather → Analyze → Human Act → Verify) by combining automated checks, AI recommendations, and human decision-making.
- Reinforced Git collaboration best practices by working in a feature branch, pushing to my own fork, and opening a Pull Request with a reviewed AI-generated draft.

---

# Submission Instructions

- Ensure `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` are committed to your GitHub repository
- Add all required screenshots to your submission
- All written answers must be in your own words
- Do not use a real secret or credential anywhere in your submission — the fake key in Task 1 is intentional and must stay clearly fake
- Open your Pull Request against your own fork, not the shared upstream repository
- Push your final changes to your forked repository
- Include your PR link and LinkedIn post URL

---

## GitHub Repository URL

Paste your forked repository URL here:

https://github.com/MiDevopsEngineer/devops-micro-internship-interviews.git

---

# Completion Checklist

- [x] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [x] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [x] `core.hooksPath` configured to point at `hooks/`
- [x] Pre-commit hook shown blocking the risky commit
- [x] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [x] `/pr-ready` run against the risky diff and shown flagging issues
- [x] Risky file fixed; `git commit` succeeds cleanly
- [x] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [x] Pull Request opened using the AI draft as a starting point, with your own fork as the base repository (not upstream), PR link included
- [x] Agentic Loop mapping (Task 7) completed in your own words
- [x] LinkedIn post published and URL submitted
- [x] All required screenshots added
- [x] GitHub repository URL provided

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
