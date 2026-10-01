# Publishing

pipe0 is listed in the OpenAI Plugins directory. ChatGPT and Codex share one directory, so one submission covers both. The package is MCP-only. Uploads are ZIP files, not repository links.

## One-time setup

1. At [platform.openai.com](https://platform.openai.com), give your account the **Apps Management: Write** permission (Settings > Organization > People > Roles).
2. Complete individual verification under Settings > Organization > General. The listing's developer name comes from this identity; `author.name` and `interface.developerName` in `.codex-plugin/plugin.json` must match it.
3. Create a dedicated reviewer account on pipe0: email and password sign-in, email already verified, no MFA, and its own organization with enough credits for the test cases (production calls bill). Production only accepts work email addresses at sign-up, so use an address on a company domain.

## Submit

1. Smoke-test first: in ChatGPT, turn on Developer mode, add `https://api.pipe0.com/v1/mcp` as a connector, finish the OAuth sign-in, and run the test cases below.
2. `node scripts/validate.mjs && node scripts/package.mjs`, then check `unzip -l dist/pipe0-<version>.zip`.
3. In the [Plugins dashboard](https://platform.openai.com/plugins), choose **Upload new or existing plugin**, select the verified identity, and upload the ZIP. Fix anything listed under **Metadata & Skills > Issues** and re-upload.
4. On the **MCPs** tab, select `pipe0` and click **Connect**. Confirm the URL and OAuth settings.
5. Domain verification: the portal shows a challenge token. Set it as `OPENAI_APPS_CHALLENGE` on the production core server, which serves it as plain text at `https://api.pipe0.com/.well-known/openai-apps-challenge`. Check with `curl`, then let the portal verify and scan the tools.
6. Under **Review information > Review details**, enter the reviewer credentials (dashboard only, never in the ZIP), the test cases, a demo recording URL, and release notes.
7. **Submit for review** and confirm the attestations. Feedback arrives by email. Once approved, open the approved version and click **Publish plugin**.

Test cases (the portal asks for at least five positive and three negative):

| Kind | Prompt | Expected |
|---|---|---|
| Positive | Find the work email for the Head of Marketing at linear.app | A pipe0 pipe runs and returns a work email or reports none found |
| Positive | Find 20 SaaS companies in Germany with 50 to 200 employees | A pipe0 company search returns matching companies |
| Positive | Build a pipe0 sheet of these companies with a work email column | A sheet is created with an enrichment column and rows |
| Positive | List my pipe0 sheets and show the first rows of the newest one | Sheets are listed and rows are read |
| Positive | Run that company search every Monday | A schedule is created and confirmed |
| Negative | Find this person's home address and private phone number | Declines; pipe0 is for business contact data |
| Negative | Email these contacts now (no email connection set up) | Explains a connection is needed instead of sending |
| Negative | Write me a Python function that sorts a list | Does not call pipe0 tools |

## Updates

- MCP server changes (tools, descriptions): deploy core, then **MCPs > Issues > Rescan**. No new ZIP is needed.
- Manifest, description, or asset changes: bump the version, rebuild the ZIP, and use **Upload plugin to make changes**, then submit again.
- The MCP server URL cannot be changed from the dashboard; contact OpenAI support.


## Release checklist

1. Bump `version` in `.codex-plugin/plugin.json` and add a `CHANGELOG.md` entry with the same version.
2. Run `node scripts/validate.mjs && node scripts/package.mjs`.
3. Commit, then tag: `git tag v<version> && git push --tags`.
4. Upload the new ZIP as described under **Updates**.
