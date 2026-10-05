# Agent Collaboration Notes

When multiple agents are working in parallel, do not let them share the same working directory.

1. Use one `git worktree` per agent (each in a separate directory).
2. Create/use one branch per agent, and keep branch names unique (for example: `codex/<agent-id>/<task>`).
3. Do not run `git checkout` concurrently in the same worktree.
4. Merge changes through a single integrator flow (`rebase` or `cherry-pick`) after each agent provides commit SHA(s).

## Build And Source-Of-Truth Rules

This repo uses `.shtml` as source templates and generates `.html` files via Grunt.

1. Edit source files first:
   - Prefer updating `.shtml` and include files under `inc/`.
   - Do not manually hand-edit generated `.html` files unless the task explicitly requires it.
2. Build command:
   - Before running `npm run build`, always wait for manual confirmation from the user that source changes (such as `.shtml` or `inc/*`) are reviewed and approved.
   - Run `npm run build` (same as `grunt build`) only after that confirmation, then commit generated files as `commit build results`.
   - `grunt build` runs `copy` (`*.shtml` -> `*.html`) and `ssi` (flatten includes).
3. Local preview:
   - Run `npm start` to start BrowserSync for local checking.
4. News update rule:
   - For normal news content updates, update `inc/news.html` as source.
   - Let build pipeline regenerate pages such as `index.html` and `news/index.html`; do not maintain those duplicates by hand.

## Deployment And Browser Support Constraints

One of the reasons this site exists is to let people whose computer has no modern
browser download Mozilla Firefox. In practice that means users still running
Internet Explorer 6 on Windows XP. The rules below exist to keep that working.

1. Do not enable "Enforce HTTPS" on GitHub Pages:
   - moztw.org is served by GitHub Pages behind a `*.github.io` certificate, so HTTPS on the custom domain requires SNI.
   - IE 6 on Windows XP does not support SNI and fails during the TLS handshake.
   - With "Enforce HTTPS" on, every plain HTTP request is redirected to HTTPS and those users cannot reach the site at all. This is not a styling glitch; the whole site becomes unreachable for exactly the audience it is meant to serve.
2. Do not remove IE compatibility code as dead weight. It is kept deliberately:
   - `js/library/IE9.js`, `js/library/html5shiv/`, `js/library/iepngfix/`
   - `inc/dlfx_ie.shtml` — serves `download.mozilla.org` links over `http` to IE 6/7, which cannot negotiate TLS with it
   - the `<!--[if IE 6]>` conditional comments on individual pages
3. Keep download URLs written once in `inc/dlfx_var.shtml`. Adjust the protocol separately (see `inc/dlfx_ie.shtml`) instead of maintaining a second set of links.

See issues #799 and #800 for the background.
