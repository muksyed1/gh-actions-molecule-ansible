# gh-actions-molecule-ansible

An Ansible role that installs and configures nginx, tested with
[Molecule](https://ansible.readthedocs.io/projects/molecule/) in a Docker
container. This repo is a hands-on project for practising Ansible testing and
CI/CD with GitHub Actions.

## What the role does

- Updates the apt cache and installs nginx from the Ubuntu repositories.
- Renders a custom index page from a Jinja2 template
  (`roles/nginx/templates/index.html.j2`) to `/var/www/html/index.html`, owned
  by `www-data` with mode `0644`.
- Restarts nginx through a handler whenever the package or the page changes.

## Repository layout

```
.
├── site.yml                    # top-level playbook
└── roles/
    └── nginx/
        ├── defaults/           # role defaults (none defined yet)
        ├── handlers/           # restart nginx
        ├── meta/               # role metadata
        ├── molecule/default/   # Molecule scenario: molecule.yml, converge.yml, verify.yml
        ├── tasks/              # install nginx, write the index page
        └── templates/          # index.html.j2
```

## Requirements

- Docker (running)
- Python 3 and a virtualenv
- Ansible, Molecule with the Docker driver, ansible-lint and yamllint

Developed with ansible-core 2.19 and Molecule 26.x, on Ubuntu 22.04 containers.

## Running the tests

Create and activate a virtualenv, install the tools, then run Molecule from the
role directory:

```bash
pip install ansible-core molecule "molecule-plugins[docker]" ansible-lint yamllint

cd roles/nginx
molecule test
```

`molecule test` runs the full sequence: create a container, apply the role
(converge), run it a second time and fail if anything changes (idempotence),
check that nginx serves the page (verify), and destroy the container.

Useful while developing:

| Command | What it does |
| --- | --- |
| `molecule converge` | Create the container and apply the role, leave it running |
| `molecule verify` | Run the checks against the running container |
| `molecule login` | Open a shell inside the test container |
| `molecule destroy` | Remove the container |

## Linting

```bash
ansible-lint
yamllint .
```

## Platforms

Tested on Ubuntu 22.04 (jammy) using the
`geerlingguy/docker-ubuntu2204-ansible` image.

## CI

A GitHub Actions workflow that runs `molecule test` on every push and pull
request is planned.

## License

MIT
