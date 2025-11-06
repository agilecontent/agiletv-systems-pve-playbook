# agiletv-systems-install-playbook

Run Ansible check mode to --limit convoy:
```
rm -rf roles/* && ansible-galaxy install -r requirements.yml --keep-scm-meta --roles-path ./roles && ansible-playbook -i inventory.yml main.yml --vault-password-file ~/.vault_password_file -v --limit "pre_logging_global" --check
```

Run Ansible to --limit convoy NO CHECK, this will effectively apply changes on remote:

```
rm -rf roles/* && ansible-galaxy install -r requirements.yml --keep-scm-meta --roles-path ./roles && ansible-playbook -i
 inventory.yml main.yml --vault-password-file ~/.vault_password_file -v --limit "convoy" -v
```

Para instalar un ELK por primera vez en varios nodos.
rm -rf roles/* && ansible-galaxy install -r requirements.yml --keep-scm-meta --roles-path ./roles && ansible-playbook -i inventory.yml main.yml --vault-password-file ~/.vault_password_file -v --limit "pro_logging_global" --check

El setup_elasticserach 
- crea los certificados, si no existen.
- configura el nodo inicial
- provisiona usuarios, indices e index_templates

Luego los certificados hay q copiarlos a mano a cada nodo.
