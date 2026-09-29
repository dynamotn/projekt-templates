# Projekt templates and boilerplates

`t` (also `projekt template`) renders a template from a store of them, and `b`
(`projekt boilerplate`) creates a whole project from a recipe that names one.
This repository holds both stores: `templates/` and `boilerplates/` at the
root, reached through `PROJEKT_TEMPLATE_DIR` and `PROJEKT_BOILERPLATE_DIR`,
which `home/private_dot_config/fish/config.fish.tmpl` sets.

They live outside `home/` on purpose. A projekt template is a file ending in
`.tmpl`, and so is a chezmoi template — inside `home/` chezmoi would render
every one of them on apply, and each `{{ }}` the template is made of would have
to be escaped. The recipes sit beside the templates they name.

The templates cover the languages the Neovim configuration is set up for, in
`home/private_dot_config/nvim/lua/config/languages.lua`. A language whose
projects are made by its own generator — Rails, Vue, Angular — has no template
here; `rails new`, `npm create vue@latest` and `ng new` do it better and stay
current.

## Using them

```bash
t list                          # what the store holds
t new go-cli ./myapp --name myapp -i   # scaffold a project, asking for values
t new license LICENSE --set license=MIT
t show adr                      # read a template's source
t path go-cli                   # where it is, for $EDITOR
t check                         # every template still renders
```

Every template renders with nothing but `--name`, so `t new rust-crate ./x
--name x` works and `-i` is for changing the answers rather than supplying
them. The values a template asks for are in its `.vars.yaml`; shell completion
reads the same file.

## Project scaffolds

| Template | What it writes |
|---|---|
| `ansible-role` | role with `tasks/`, `defaults/`, `handlers/`, `meta/`, a molecule scenario |
| `arduino-sketch` | sketch with `config.h`, `sketch.yaml`, `arduino-cli` commands |
| `bash-tool` | `bin/`, `lib/` namespaced `area::`, bats tests, shellcheck and shfmt hooks |
| `beancount-ledger` | `main.beancount`, opening balances, a year of transactions |
| `cpp-cmake` | CMake project, library plus optional executable, CTest, clang-tidy |
| `csharp-console` | `src/` and `tests/` projects, xUnit, nullable and warnings-as-errors |
| `fish-plugin` | `functions/`, `completions/`, `conf.d/`, fisher-installable |
| `go-cli` | `cmd/`, `internal/cli` with tests, Makefile, golangci-lint, CI |
| `helm-chart` | `Chart.yaml`, values, deployment, service, helpers |
| `java-maven` | Maven project, JUnit 5, package asked one path segment at a time |
| `latex-doc` | `main.tex`, a section, `latexmkrc` |
| `nix-flake` | flake with a package, a dev shell and a formatter |
| `nvim-plugin` | `lua/<name>/`, `plugin/`, help file, busted spec, stylua config |
| `php-composer` | PSR-4 library, PHPUnit, phpcs and php-cs-fixer |
| `proto-module` | buf module, a service, lint and breaking-change config |
| `python-app` | `src/` layout, ruff and pytest in `pyproject.toml`, optional console script |
| `rego-policy` | policy with `allow`/`deny`, tests, regal config |
| `ruby-gem` | gemspec, `lib/`, RSpec, RuboCop, optional executable |
| `rust-crate` | library with tests and errors, optional binary |
| `solidity-foundry` | contract, Foundry test with `vm.prank` and `expectRevert` |
| `terraform-module` | `main`/`variables`/`outputs`/`versions`, an example, tflint |
| `terragrunt-unit` | unit plus the `root.hcl` it includes |
| `typescript-lib` | ESM library, Biome, Vitest, build and dev tsconfigs |
| `typst-doc` | `main.typ` and a `template.typ` that holds the look |
| `zig-exe` | `build.zig`, `build.zig.zon`, a source file with its own tests |

## Single files

| Template | What it writes |
|---|---|
| `adr` | an architecture decision record |
| `awk-script` | an awk filter with `BEGIN`/`END` |
| `bash-lib` | a `<area>.sh` library, documented in the repository's comment style |
| `bash-script` | a standalone script, optionally on dybatpho |
| `bats-test` | a bats file for a library |
| `bicep-module` | a Bicep module with parameters, outputs and TLS defaults |
| `cucumber-feature` | a feature with a background, a scenario and an outline |
| `d2-diagram` | a d2 diagram of an actor, a service and a store |
| `dbml-schema` | two tables, an index and a reference |
| `docker-compose` | one service with a healthcheck |
| `dockerfile` | a Dockerfile that runs as a non-root user |
| `editorconfig` | the shared `.editorconfig` |
| `fish-function` | a fish function with `argparse` and `--help` |
| `gh-workflow` | a GitHub Actions workflow (delimiters moved to `<% %>`) |
| `gitignore` | the shared ignore list, plus what a project adds |
| `gitlab-ci` | a two-stage `.gitlab-ci.yml` |
| `jenkinsfile` | a declarative pipeline |
| `license` | MIT, Apache-2.0, BSD-2-Clause, GPL-3.0, Unlicense, WTFPL or CC-BY-NC-SA-4.0 |
| `lua-module` | a Lua module with a typed options table |
| `makefile` | a self-documenting Makefile |
| `nginx-site` | an https server block with a proxy upstream |
| `pre-commit-config` | a prek config, with the hooks each language needs |
| `readme` | a README skeleton |
| `sql-migration` | an up/down migration |
| `systemd-unit` | a hardened service unit |

## Boilerplates

Where `t` writes files into a folder, `b` creates the project: it renders the
template, puts it in the workspace it belongs to, points `origin` at the right
server, and leaves it reachable by name.

```bash
b list                     # the recipes
b show go-cli              # one of them, as written
b new go-cli myapp         # ~/Working/personal/tools/myapp, pj tool-myapp
b new go-cli myapp -i      # ask for the values instead of taking the defaults
b new go-cli myapp --dry-run   # the whole plan, nothing written
b check --strict           # every recipe still usable
```

There is one recipe per project scaffold, 25 of them, and the name is the
template's name. What a recipe adds is **where the project goes**:

| Recipes | Lands in | Remote |
|---|---|---|
| `go-cli`, `bash-tool`, `python-app`, `rust-crate`, `typescript-lib` | `~/Working/personal/tools` (`tool-`) | `gl:dynamo-tools` |
| `fish-plugin` | `~/Working/personal/fish` (`fish-`) | `gl:dynamo-config/fish-packages` |
| `nvim-plugin`, `proto-module` | `~/Working/personal/misc` (`misc-`) | `gh:dynamotn` |
| `terraform-module`, `terragrunt-unit`, `ansible-role`, `helm-chart`, `rego-policy`, `nix-flake` | `~/Working/personal/infra` (`infra-`) | `gl:dynamo-infra` |
| `beancount-ledger` | `~/Working/personal/finance` (`finance-`) | `gl:dynamo-finance` |
| `cpp-cmake`, `csharp-console`, `java-maven`, `php-composer`, `ruby-gem`, `zig-exe`, `solidity-foundry`, `arduino-sketch`, `latex-doc`, `typst-doc` | `~/Temp` (`tmp-`) | none |

Most recipes carry no `vars` of their own: left out, `b` asks whatever the
template asks, so there is one place to change a question. `go-cli` and
`bash-tool` are the exceptions, because the workspace changes what the right
default is — a module path under `dynamo-tools` rather than under the user
name.

Recipes create no repository on the server. `register.remote` runs `git init`
and sets `origin`; the repository itself is one `gh repo create` or `glab repo
create` away, and it is left out of the `after:` hooks on purpose — a hook that
reaches a server every time `b new` runs is not something to discover
afterwards.

### Two things a recipe cannot do

- **It cannot pin a value.** A recipe has `description`, `source`, `vars`,
  `register` and `after`, and a `vars` default only applies under
  `--interactive` — exactly like a template manifest. A value a plain `b new`
  must get right belongs in the template's `.data.yaml`. That is why `go-cli`
  carries `ci: gitlab` there rather than in the recipe.
- **It should not land outside a workspace.** When the project lands inside a
  configured workspace, nothing is written to the projekt configuration — the
  workspace already reaches it. Given a path outside one, `b` adds an entry to
  `~/.config/projekt/config.yaml`, which **chezmoi manages**: the entry is lost
  on the next `chezmoi apply`. Every recipe here names a configured workspace,
  so `b new <recipe> <name>` is safe. For a one-off elsewhere, pass
  `--no-register`, and put the folder in
  `home/private_dot_config/projekt/config.yaml.tmpl` if it is to last.

### Creating from a recipe again

```bash
b apply                        # every recipe this project records
b apply go-cli --set ci=github # one recipe, one answer changed
b apply --dry-run
```

`b new` records the recipe and its answers in the project's
`.projekt/template.yaml`, so applying needs no flags. A file edited by hand is
kept unless `--force`, a file the template no longer writes is kept unless
`--prune`, and the `after` commands do not run again unless `--hooks`.

## Editing the store

```bash
$EDITOR (t path go-cli)      # fish
t check --strict             # before committing
t add ./someproject --name new-template --replace someproject=Name
```

Four names describe a template rather than belong to it: `.vars.yaml` (what to
ask), `.data.yaml` (what is already known), `.ignore` (what not to write) and
`.templates/` (fragments several templates share). The store's own
`.templates/` holds the licences, the shared `.editorconfig`, the common
gitignore and the base prek hooks.

Two things to keep in mind when adding one:

- **A `.vars.yaml` default only applies under `--interactive`.** Every value a
  template reads needs an inline `| default` as well, or a plain `t new` writes
  `<no value>` — and a path segment that renders to nothing fails outright.
- **A nested `{{ }}` inside a string literal is not an action.** Write
  `default (printf "/var/lib/%s" .Name)`, not `default "/var/lib/{{ .Name }}"`.

## Applying a template again

```bash
t diff                                     # what has drifted here
t apply                                    # bring it up to date
projekt folder exec -t tool -- t diff      # across every project
```

A recipe is checked the same way, and `b check` additionally reads the template
it names, so a broken template fails both.
