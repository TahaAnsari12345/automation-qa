# Morning Brief: n8n Workflow

**Trigger:** Schedule (every 1 hour) plus a manual trigger for testing.

**APIs used**
1. GitHub Search API: finds the most-starred repos for topic "ai".
   Chosen because it is free, needs no signup beyond a token, and has rich data.
2. GitHub Releases API: fetches the latest release of each top repo.
   Chosen because it enriches each repo with version and date, from the same provider.

**Transformation:** A Code node sorts repos by stars, keeps the top 5,
and reduces each to name, owner, stars, url and description
(empty descriptions get a fallback). A second Code node merges
the release data back with the repo data.

**Conditional branch:** An IF node checks stars > [YOUR NUMBER].
True -> labelled "🔥 Hot repo". False -> "📦 Regular repo".
The branches are merged again and built into one digest message.

**Output:** One Discord message via webhook.

**Error handling:** Both HTTP nodes use "Continue (using error output)".
A failure goes to a Set node that logs the error message and timestamp,
then sends a ⚠️ alert to Discord instead of crashing silently.
I tested this by breaking the URL on purpose.
Known limitation: repos with no release are skipped in the digest.

**Credentials:** GitHub token and Discord webhook are stored in n8n's
Credentials store and referenced by nodes, so no secrets are in the JSON.
