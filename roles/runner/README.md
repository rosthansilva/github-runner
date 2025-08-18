# Role Runner

Role Ansible para executar **scripts** e **comandos** organizados em fases:

- **Pré-run** → preparação (ex: validações, setup)
- **Run Action** → execução principal (ex: build, deploy, migração)
- **Post-run** → pós-execução (ex: limpeza, notificações)

## Estrutura

- `type`: tipo do item (`bash`, `sh`, `python`, `powershell`, `command`)
- `path`: caminho do script (para `bash`, `sh`, `python`, `powershell`)
- `cmd`: comando a ser executado (para `command`)

## Exemplo de uso

```yaml
- hosts: all
  roles:
    - role: runner
      vars:
        pre_run:
          - type: bash
            path: /opt/scripts/check_env.sh
          - type: command
            cmd: "echo Preparando ambiente..."

        run_action:
          - type: python
            path: /opt/scripts/deploy.py
          - type: sh
            path: /opt/scripts/migrate.sh

        post_run:
          - type: powershell
            path: C:\cleanup.ps1
          - type: command
            cmd: "echo Finalizado!"mkdir -p roles/runner/{defaults,meta,tasks} && \

cat <<'EOF' > roles/runner/defaults/main.yml
# Definição das fases com scripts ou comandos
pre_run: []
run_action: []
post_run: []

# Exemplo de uso:
# pre_run:
#   - type: bash
#     path: /opt/scripts/check_env.sh
#   - type: command
#     cmd: "echo Preparando ambiente..."
#
# run_action:
#   - type: python
#     path: /opt/scripts/deploy.py
#
# post_run:
#   - type: powershell
#     path: C:\cleanup.ps1
#   - type: command
#     cmd: "echo Finalizado!"
