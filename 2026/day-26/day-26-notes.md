# Day 26 – GitHub CLI: Manage GitHub from Your Terminal

## Task 1: Installation & Authentication Notes

### Verification Output
- **Installed Version:** `gh version`
- **Logged-in User:** Run `gh auth status` to view active account and scopes.

---

### Question: What authentication methods does `gh` support?

`gh` supports three primary authentication methods:

1. **Interactive Web Browser Flow (OAuth Device Flow):**
   - Initiated via `gh auth login` -> *Login with a web browser*.
   - Generates a one-time 8-character verification code to enter in your browser, granting authorized OAuth credentials to the local credential helper or keyring.

2. **Personal Access Token (PAT):**
   - Can be passed interactively via `gh auth login` -> *Paste an authentication token*.
   - Can also be passed non-interactively via standard input:
     ```bash
     gh auth login --with-token < mytoken.txt
     ```
   - Ideal for headless environments and basic scripts where standard scopes (`repo`, `read:org`, `gist`) are required.

3. **Environment Variables (`GH_TOKEN` / `GITHUB_TOKEN`):**
   - Authenticates by reading a token directly from the environment:
     ```bash
     export GH_TOKEN="ghp_xxxxxxxxxxxx"
     ```
   - Best practice for CI/CD pipelines (such as GitHub Actions runners), Docker containers, and automated DevOps provisioning scripts without needing a persisted config file.

## Task 3: Issue Management with `gh`

### Key Commands Used
- `gh issue create --title "..." --body "..." --label "..."`
- `gh issue list`
- `gh issue view <issue-number>`
- `gh issue close <issue-number> --comment "..."`

---

### Question: How could you use `gh issue` in a script or automation?

`gh issue` is a powerful building block in CI/CD pipelines, cron jobs, and DevOps automation workflows:

1. **Automated CI/CD Failure Triage:**
   - When a deployment or scheduled build fails, a post-failure script can automatically file an issue with error logs, commit SHA, and assigned team members:
     ```bash
     gh issue create \
       --title "Build Failure: $(git rev-parse --short HEAD)" \
       --body "CI Pipeline failed on branch \`$BRANCH_NAME\`. Check workflow run logs: $RUN_URL" \
       --label "ci/cd,bug" \
       --assignee "@devops-oncall"
     ```

2. **Automated Vulnerability & Dependency Alerts:**
   - In security scanning scripts (e.g., Trivy, Snyk), newly discovered CVEs above a severity threshold can automatically create or update tracking issues.

3. **Scheduled Maintenance Reminders:**
   - Using cron jobs to create recurrent sprint planning, certificate renewal, or quarterly rotation tickets programmatically.

4. **Parsing Issue Data via JSON Output (`--json` & `--jq`):**
   - Querying open issues to generate automated release changelogs or operational status reports:
     ```bash
     gh issue list --state open --label "release-blocker" --json number,title --jq '.[].title'
     ``
## Task 4: Pull Request Lifecycle with `gh`

### Key Commands Used
- `gh pr create --title "..." --body "..." --base main`
- `gh pr list`
- `gh pr view <number>`
- `gh pr checks <number>`
- `gh pr merge <number> --squash --delete-branch`

---

### Question 1: What merge methods does `gh pr merge` support?

`gh pr merge` supports the standard three Git merge strategies configured on GitHub:

1. **`--merge` (Create a merge commit):**
   - Merges all commits from the head branch into the base branch with an explicit merge commit preserving full history (`git merge --no-ff`).
2. **`--squash` (Squash and merge):**
   - Combines all commits from the PR into a single commit on the base branch. Keeps main branch history linear and clean.
3. **`--rebase` (Rebase and merge):**
   - Applies all commits from the PR individually onto the base branch without a merge commit.

*Additional automation flags:*
- `--auto`: Enables auto-merge once all required branch protection checks and reviews pass.
- `--delete-branch` (`-d`): Automatically deletes both local and remote feature branches after merging.

---

### Question 2: How would you review someone else's PR using `gh`?

You can inspect, check out, and submit reviews entirely through the terminal:

1. **Check out the PR locally to test code:**
   ```bash
   gh pr checkout <pr-number>`

## Task 5: GitHub Actions & Workflows (Preview)

### Key Commands Used
- `gh run list -R <owner/repo>`
- `gh run view <run-id> -R <owner/repo>`
- `gh run view <run-id> --log-failed`
- `gh run watch <run-id>`

---

### Question: How could `gh run` and `gh workflow` be useful in a CI/CD pipeline?

1. **Triggering Downstream Deployments (`gh workflow run`):**
   - A pipeline running in one repository can trigger a deployment or end-to-end testing workflow in another repository without setting up custom webhook infrastructure:
     ```bash
     gh workflow run deploy-prod.yml -R org/infrastructure-repo -f environment=production -f version=v2.1.0
     ```

2. **Gating and Polling Run Status (`gh run watch` / `gh run view --exit-status`):**
   - Scripts can trigger asynchronous jobs and wait for their completion, exiting with non-zero error codes if the run fails:
     ```bash
     gh run view <run-id> --exit-status
     ```

3. **Automated Triage & Log Extraction (`gh run view --log-failed`):**
   - When builds fail, scripts can fetch raw failure logs and pipe them into incident channels (Slack/Discord/Teams) or feed them into analysis tools without navigating browser pages.

4. **Artifact Management (`gh run download`):**
   - CI/CD build outputs, binaries, test reports, or container images generated by a previous workflow run can be retrieved programmatically:
     ```bash
     gh run download <run-id> -n build-artifacts -D ./dist
     ```

5. **Automated Workflow Maintenance (`gh workflow disable` / `enable`):**
   - DevOps engineers can disable scheduled staging smoke-tests or noisy cron workflows during maintenance windows and re-enable them afterwards via bash scripts.
