# Controlled Dependabot branch-collision lab

This repository is intentionally vulnerable and must only be deployed under
accounts and organizations that have explicitly authorized the test.

The lab reproduces the two important GitHub behaviors from the
`justeattakeaway/pie` finding:

1. A cross-repository pull request can receive a `synchronize` event whose
   `github.actor` is `dependabot[bot]`, even though the PR author is a different
   account.
2. Combining the trusted base `owner/repo` with the fork-controlled
   `pull_request.head.ref` writes to a same-named branch in the base repository.

The workflow refuses to run unless:

- the repository is named exactly `dependabot-branch-collision-lab`; and
- the repository Actions variable `LAB_ACK` equals `controlled-test`.

It writes only to `lab-proof/pr-<number>.txt`.

## Required GitHub setup

- A base account or organization that owns the lab repository.
- A separate attacker account with no write access to the base repository.
- The base repository must be public so the attacker account can fork it.
- GitHub Actions and Dependabot version updates must be enabled.
- GitHub CLI must be authenticated for both controlled accounts, or equivalent
  repository-scoped credentials must be available locally.

Do not put tokens in this repository or paste them into a report/chat.

## Expected result

After the attacker fork's Dependabot branch is rebased, the base
`pull_request_target` workflow records:

```text
event actor: dependabot[bot]
pull request author: <attacker account>
fork repository: <attacker account>/dependabot-branch-collision-lab
base write repository: <base owner>/dependabot-branch-collision-lab
base write branch: <same Dependabot ref used by the fork>
```

The proof file appears as a new commit on the colliding base branch.
