# Finding: Historical Secret in Git History

**Tool:** gitleaks (secrets-scan gate)
**Rule:** stripe-access-token
**Location:** `deploy/deployment.yaml:24` (historical commit, not current HEAD)

## What happened
An early commit to this repository included a plaintext Stripe API key
(`sk_live_9f3a2b7c1e4d8...`) directly in the Deployment manifest, matching
the original insecure starter state described in the assessment brief.
This was subsequently fixed by moving the secret to a Sealed Secret and
referencing it via `envFrom.secretRef` — but gitleaks correctly flags that
the plaintext value is still present in git history, even though it's gone
from the current file.

## Why this matters
Removing a secret from the latest commit does NOT remove it from git
history. Anyone with clone access can check out the earlier commit and
recover the plaintext key. The correct incident response here is not
"edit the file" — it's:

1. **Rotate the credential immediately.** Treat it as compromised the
   moment it was committed, regardless of repo visibility or how quickly
   it was fixed.
2. **Purge it from history** (`git filter-repo` or BFG Repo-Cleaner),
   understanding this requires a force-push and coordination with anyone
   else who has cloned the repo.
3. **Add gitleaks as a pre-commit hook**, not just a CI gate, so the
   secret never reaches a pushed commit in the first place — CI catching
   it after the fact is a safety net, not the primary control.

## Status
Documented and accepted as a known historical artifact for this
assessment repo rather than rewriting history, given time constraints.
In a production incident this would be remediated per steps 1–2 above
before the pipeline is considered safe to re-enable.
