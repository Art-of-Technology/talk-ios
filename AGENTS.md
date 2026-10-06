<!--
  - SPDX-FileCopyrightText: 2026 Nextcloud GmbH and Nextcloud contributors
  - SPDX-License-Identifier: GPL-3.0-or-later
-->
# Agent guidance

Read `README.md` for development and contribution instructions. Inspect the current Xcode project, `Podfile`, and `.github/workflows/` for build and test requirements.

## Fork upstream review

At session start, inspect the review ledger using [talk-upstream-review](.agents/skills/talk-upstream-review/SKILL.md). Run that review when the last completed review is missing or more than seven days old, before a release, and when a relevant upstream security fix becomes known. An incomplete review does not reset the interval.

Reviews produce evidence and recommendations only: no automatic adoption, merge, deployment, or scheduler. Read the skill and its [iOS context](.agents/skills/talk-upstream-review/repo-context.md) before reviewing. This fork tracks `nextcloud/talk-ios` (`main`) as its source; this is not proof of the fork's integration branch. Before any future pull request, verify the current integration branch from live fork evidence and target only `Art-of-Technology/talk-ios`, never upstream.

Keep committed customizations brand-neutral; deployment-specific names, domains, signing credentials, and branding belong in ignored local configuration.
