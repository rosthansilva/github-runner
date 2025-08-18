# Ansible Runner Role 🚀

Role completa para executar pipelines de comandos ou scripts com múltiplas fases:

- 🔹 **Pre-run**: comandos executados antes da ação principal  
- 🏃 **Run Action**: comando principal ou execução de scripts  
- 🛠️ **Post-run**: limpeza ou tarefas finais  

## Features principais

| Feature | Descrição | Emoji |
|---------|-----------|-------|
| Pre-run | Executa tarefas antes da ação principal | 🔹 |
| Run Action | Tarefas principais do pipeline | 🏃 |
| Post-run | Tarefas finais de cleanup | 🛠️ |
| Confirmação | Permite que usuário confirme execução antes de rodar | ✅ |
| Retry automático | Permite re-executar comandos falhos N vezes | 🔁 |
| Timeout | Limita tempo de execução de cada comando | ⏱️ |
| Execução paralela | Rodar comandos de forma assíncrona/paralela | ⚡ |
| Docker | Suporte a execução de comandos dentro de containers | 🐳 |
| Logs | Captura saída em arquivos e JSON | 📄 |
| Ignore errors | Continua mesmo se o comando falhar | ❌ |

---

## Exemplo de uso (usando todas as features)

```yaml
- hosts: localhost
  gather_facts: false
  roles:
    - role: ansible_runner_role
      pre_run:
        - name: "Check system"
          type: command
          cmd: "echo 'Checking system pre-run...'"
          retries: 2
          ignore_errors: false
          async: 0
          ask_confirmation: false

      run_action:
        - name: "Run local script"
          type: bash
          path: "/tmp/test.sh"
          retries: 1
          async: 0
          ask_confirmation: true
          ignore_errors: false

        - name: "Run docker command"
          type: docker
          image: "ubuntu:22.04"
          cmd: "echo 'Hello Docker Run Action!'"
          retries: 0
          async: 0
          ask_confirmation: false
          ignore_errors: false

      post_run:
        - name: "Cleanup logs"
          type: command
          cmd: "echo 'Cleaning up post-run...'"
          retries: 0
          async: 10        # executa em paralelo
          ask_confirmation: false
          ignore_errors: true
