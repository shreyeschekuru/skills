# turnstile-spin (skill)

End-to-end setup skill for Cloudflare Turnstile. Loads when an agent is asked to add Turnstile, set up CAPTCHA, protect a form from bots, migrate from reCAPTCHA or hCaptcha, or fix a siteverify integration.

This is a mirror of the canonical docs page at [`developers.cloudflare.com/turnstile/spin`](https://developers.cloudflare.com/turnstile/spin/). If the two disagree, the docs page wins.

## Layout

| File                              | Purpose                                                                |
| --------------------------------- | ---------------------------------------------------------------------- |
| `SKILL.md`                        | Main wizard instructions for the agent                                 |
| `references/vanilla-html.md`      | Code snippet for static / vanilla HTML projects                        |
| `references/nextjs-app.md`        | Code snippet for Next.js App Router projects                           |
| `references/nextjs-pages.md`      | Code snippet for Next.js Pages Router projects                         |
| `references/astro.md`             | Code snippet for Astro projects                                        |
| `references/sveltekit.md`         | Code snippet for SvelteKit projects                                    |
| `references/hugo.md`              | Code snippet for Hugo projects                                         |
| `scripts/validate.sh`             | End-to-end validation for Worker health, siteverify, widget domains, and frontend markers |
| `templates/worker/README.md`      | Manual deploy and testing notes for the bundled managed Worker template |

## How agents load it

Agents that load skill bundles from `github.com/cloudflare/skills` will pick this up automatically. For agents that load skills out of a local directory, install the whole skill folder so the scripts, references, and Worker template are available:

```sh
# Claude Code local directory install
git clone https://github.com/cloudflare/skills ~/.config/cloudflare-skills
mkdir -p ~/.claude/skills
ln -s ~/.config/cloudflare-skills/skills/turnstile-spin ~/.claude/skills/turnstile-spin
```

For other agents, see Step 12 in [`SKILL.md`](./SKILL.md#conversation-flow).

## Sync with the docs page

The canonical source of truth is `src/content/docs/turnstile/spin/index.mdx` in the `cloudflare-docs` repo. This skill mirrors that content with the JSX stripped out. CI keeps them in sync on each docs release; if you are hand-editing, mirror your change to both places.

## Related

- [Canonical docs page](https://developers.cloudflare.com/turnstile/spin/)
- [`templates/worker/`](./templates/worker/) - the managed Worker template that this skill deploys
- [`cloudflare/skills`](https://github.com/cloudflare/skills) - root index for all Cloudflare agent skills
