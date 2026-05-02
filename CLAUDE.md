# nemaks — project instructions

## Repo overview

Literate Emacs config using org-babel. Single source of truth: `init.org` (~3600 lines),
tangled to `init.el`. Do not edit `init.el` directly.

**Stack:** elpaca · general.el (leader `M-m`) · vertico/consult/orderless · corfu/cape ·
lsp-mode · treemacs · magit+forge · doom-themes · nerd-icons · org-modern

## Commit conventions

Follow [Conventional Commits v1.0](https://www.conventionalcommits.org/en/v1.0.0/).

### Format

```
<type>(<scope>): <short imperative summary>

<body — explain WHY, not what the diff shows>
```

### Rules

- **English only** — no French in commit messages
- **Subject line:** imperative mood, no period, ≤72 chars
- **Body:** explain the *need* behind the change — a reader must understand
  context and consequences without looking at the code
- **Types:** `feat` `fix` `refactor` `chore` `docs` `style` `test` `remove` `misc`
- **Scope:** area of config affected — e.g. `early-init`, `lsp`, `completion`, `ui`,
  `icons`, `org`, `keybindings`, `ide`, `docs`

### Body structure for non-trivial changes

```
OUT:
- what was removed and why it was wrong / obsolete

IN:
- what was added and what problem it solves

note: any gotcha, workaround, or follow-up to keep in mind
```

### Examples

```
fix(icons): remove :after nerd-icons from nerd-icons-dired

nerd-icons-dired was declared before nerd-icons in the file.
Elpaca queued nerd-icons as a transitive dep then found it again
as a standalone declaration — Wrong type argument: listp at startup.

OUT: :after nerd-icons constraint
IN:  :ensure (:wait t) so elpaca blocks until the package is ready
```

```
feat(completion): replace ivy/company stack with vertico/consult/corfu

ivy is in maintenance mode; company-box adds overhead on top of a
backend (company-capf) that corfu covers natively via completion-at-point.
The new stack integrates with nerd-icons out of the box.

OUT: ivy, ivy-prescient, ivy-rich, swiper, counsel, smex, company, company-box
IN:  vertico, orderless, consult, corfu, cape, nerd-icons-completion, nerd-icons-corfu
```

## Elpaca gotcha — :after ordering

`:after X` on a package declared *before* `X` in the file causes:
`Wrong type argument: listp, Warning (emacs): X previously queued as dependency of: (pkg)`

Fix: remove the `:after X` constraint, or move the declaration after `X`.
