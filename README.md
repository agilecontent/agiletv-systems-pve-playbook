# AgileTV Systems - PVE Playbook

Ansible playbook for managing Proxmox Virtual Environment (PVE) nodes across the AgileTV infrastructure.

## Overview

This playbook configures and maintains all Proxmox VE nodes in the AgileTV environment. It manages user accounts, SSH access, and system configurations consistently across all PVE hosts.

**IMPORTANT:** This playbook must be run to ALL nodes to ensure consistent configuration across the infrastructure.

## Prerequisites

- Ansible installed on the control machine
- SSH access to target PVE nodes
- Deploy key configured at `~/.ssh/deploy_key`
- Vault password file at `~/.vault_password_file` (if using encrypted variables)
- Access to the private role repository: `git@github.com:agilecontent/agiletv-systems-role-users.git`

## Infrastructure

### Managed Nodes

The playbook currently manages the following PVE nodes:

- `eqmd2-pve-05.agile.int` (192.168.107.44)
- `eqmd2-pve-20.agile.int` (192.168.107.59)
- `eqmd2-pve-21.agile.int` (192.168.107.60)

All nodes are grouped under the `pve` inventory group.

## Playbook Structure

```
.
├── README.md                    # This file
├── main.yml                     # Main playbook
├── inventory.yml                # Inventory with PVE hosts
├── requirements.yml             # Ansible Galaxy role requirements
└── group_vars/
    └── all.yml                  # User definitions and global variables
```

## Usage

### Standard Execution (Apply to All Nodes)

This playbook should always be run against all nodes to maintain consistency:

```bash
rm -rf roles/* && \
ansible-galaxy install -r requirements.yml --keep-scm-meta --roles-path ./roles && \
ansible-playbook -i inventory.yml main.yml --vault-password-file ~/.vault_password_file -v
```

### Check Mode (Dry Run)

To preview changes before applying them:

```bash
rm -rf roles/* && \
ansible-galaxy install -r requirements.yml --keep-scm-meta --roles-path ./roles && \
ansible-playbook -i inventory.yml main.yml --vault-password-file ~/.vault_password_file -v --check
```

### Limiting to Specific Hosts

If you need to target a specific node (use sparingly, prefer running on all nodes):

```bash
rm -rf roles/* && \
ansible-galaxy install -r requirements.yml --keep-scm-meta --roles-path ./roles && \
ansible-playbook -i inventory.yml main.yml --vault-password-file ~/.vault_password_file -v --limit "eqmd2-pve-05.agile.int"
```

## What This Playbook Does

### User Management

The playbook manages the following user accounts across all PVE nodes:

- **System Users:**
  - `agile` (UID: 700) - Member of docker group
  - `deployer` (UID: 21001) - Deployment user, member of docker group
  - `jenkins` (UID: 2007) - CI/CD automation user with sudo access

- **Administrative Users with sudo access:**
  - `ivan.villalba` (UID: 3001)
  - `enrique.ferrero` (UID: 3002)
  - `pablo.aguinagalde` (UID: 3003)
  - `matias.bonke` (UID: 3004)
  - `javier.deposada` (UID: 3005)
  - `oscar.alba` (UID: 3006)
  - `alberto.ordax` (UID: 3007)
  - `alexander.vantilburg` (UID: 3008)

Each user is configured with their SSH public key for secure access.

## Roles

### agiletv-systems-role-users

Manages user accounts, SSH keys, and group memberships.

- **Source:** `git@github.com:agilecontent/agiletv-systems-role-users.git`
- **Version:** main
- **Tags:** `role-user`

## Connection Details

- **SSH Port:** 22
- **SSH User:** ansible
- **Private Key:** `~/.ssh/deploy_key`
- **Diff Mode:** Enabled (shows changes)

## Best Practices

1. **Always run on all nodes** - Consistency is key. Running this playbook on all nodes ensures uniform configuration.

2. **Use check mode first** - Before applying changes, run with `--check` to preview what will be modified.

3. **Keep roles updated** - The playbook clears and reinstalls roles on each run to ensure the latest versions are used.

4. **Review user changes** - When adding or removing users, update `group_vars/all.yml` and run the playbook on all nodes.

5. **Maintain SSH keys** - Keep SSH public keys up to date in the user definitions.

## Troubleshooting

### Connection Issues

If you encounter SSH connection problems:
- Verify the deploy key exists at `~/.ssh/deploy_key`
- Check that the ansible user has appropriate access on target nodes
- Verify network connectivity to the management IP addresses

### Role Installation Failures

If role installation fails:
- Ensure you have access to the GitHub repository
- Check your SSH key is added to GitHub
- Verify the repository URL in [requirements.yml](requirements.yml)

### Permission Issues

If you encounter permission errors:
- Verify the ansible user has sudo privileges on target nodes
- Check that the vault password file is correct

## Maintenance

### Adding a New PVE Node

1. Add the node to [inventory.yml](inventory.yml) under the `pve` group
2. Run the playbook against all nodes including the new one
3. Verify connectivity and configuration

### Adding a New User

1. Add user definition to [group_vars/all.yml](group_vars/all.yml)
2. Include UID, groups, and SSH public key
3. Run the playbook on all nodes to apply changes

### Removing a User

1. Remove user definition from [group_vars/all.yml](group_vars/all.yml)
2. Run the playbook on all nodes
3. Manually verify user removal if required by security policy

## Security Notes

- SSH keys are the only authentication method
- Sudo access is granted to administrative users only
- UIDs are standardized across all nodes
- The vault password file should be kept secure and never committed to version control

## Support

For issues or questions regarding this playbook, contact the AgileTV Systems team.
