# Default GitHub community files

This public repository supplies **fallback** issue forms, pull request guidance, and security/contribution instructions for repositories owned by [yesvus](https://github.com/yesvus).

GitHub uses these files only when a target repository does not define its own corresponding files. A local `.github/ISSUE_TEMPLATE` configuration or issue form replaces the **entire default set of issue forms**, rather than merging them. Specialized repositories should keep their local forms.

## Included

- Bug, feature, engineering task, and idea/research issue forms, plus the option to open a blank issue
- A concise pull request template
- Contribution guidance and private vulnerability reporting instructions

These are **community defaults**, not executable CI policies, deployment permissions, runner registrations or approval for automated agents. Repo-specific instructions and checks take precedence.

The engineering-standard and rollout proposal is tracked in [yesvus/agents#76](https://github.com/yesvus/agents/issues/76). Labels and branch rules are configured per repository; publishing these files does not create them.

Do not add credentials, private hostnames, internal operational details, or client data here.
