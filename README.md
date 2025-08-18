# Ansible Runner Role 🚀

Role completa para executar pipelines de comandos ou scripts com múltiplas fases:

* 🔹 **Pre-run**: comandos executados antes da ação principal
* 🏃 **Run Action**: comandos principais ou execução de scripts
* 🛠️ **Post-run**: limpeza ou tarefas finais

## Features principais

| Feature           | Descrição                                            | Emoji |
| ----------------- | ---------------------------------------------------- | ----- |
| Pre-run           | Executa tarefas antes da ação principal              | 🔹    |
| Run Action        | Tarefas principais do pipeline                       | 🏃    |
| Post-run          | Tarefas finais de cleanup                            | 🛠️   |
| Confirmação       | Permite que usuário confirme execução antes de rodar | ✅     |
| Retry automático  | Permite re-executar comandos falhos N vezes          | 🔁    |
| Timeout           | Limita tempo de execução de cada comando             | ⏱️    |
| Execução paralela | Rodar comandos de forma assíncrona/paralela          | ⚡     |
| Docker            | Suporte a execução de comandos dentro de containers  | 🐳    |
| Logs              | Captura saída em arquivos e JSON                     | 📄    |
| Ignore errors     | Continua mesmo se o comando falhar                   | ❌     |

---

## Estrutura do Playbook

Exemplo `main.yml` para testar a role:

```yaml
- name: Teste da Role runner-v2
  hosts: localhost
  gather_facts: yes

  vars:
    pre_run:
      - type: bash
        path: scripts/check_env.sh
        name: "Verificar ambiente Linux"
        ask_confirmation: false
      - type: powershell
        path: scripts/check_env.psh
        name: "Verificar ambiente Windows"
        ask_confirmation: false

    run_action:
      - type: python3
        path: scripts/deploy.py
        name: "Deploy Python"
        ask_confirmation: true
        confirmation_message: "Deseja executar o deploy Python? (yes/no)"
        retries: 2
        ignore_errors: false
      - type: powershell
        path: scripts/deploy.psh
        name: "Deploy PowerShell"
        ask_confirmation: true
        confirmation_message: "Deseja executar o deploy PowerShell? (yes/no)"
        retries: 2
        ignore_errors: false
      - type: bash
        path: scripts/migrate.sh
        name: "Migração DB"
        ask_confirmation: true
        confirmation_message: "Deseja executar a migração? (yes/no)"
        retries: 1
        ignore_errors: false

    post_run:
      - type: powershell
        path: scripts/cleanup.ps1
        name: "Limpeza Windows"
        ask_confirmation: false
      - type: bash
        path: scripts/migrate.sh
        name: "Limpeza Linux"
        ask_confirmation: false

  roles:
    - runner-v2
```

---

## Configuração de callback para melhor formatação

Para ver os logs em YAML, configure o `ansible.cfg`:

```ini
[defaults]
# Use o plugin de callback YAML
stdout_callback = yaml
# Habilitar callback para ad-hoc
bin_ansible_callbacks = True
```

---

## Como funciona a execução da role

1. **Pre-run**
   Executa scripts de pré-verificação antes do pipeline principal.

2. **Run Action**
   Executa scripts ou comandos principais.

   * Pergunta ao usuário se a execução deve continuar (se `ask_confirmation` = true).
   * Permite retries automáticos (`retries`) se o comando falhar.
   * Pode ignorar erros (`ignore_errors`) sem interromper o pipeline.

3. **Post-run**
   Executa scripts de limpeza ou tarefas finais após o pipeline.

4. **Logs**
   Cada comando gera um arquivo `.log` dentro de `runner_config.tmp_dir` com:

```text
RETURN_CODE: 0
STDOUT:
...
STDERR:
...
```

5. **PowerShell**
   Verifica se o PowerShell (`pwsh`) está instalado e instala automaticamente no Ubuntu via Snap, se necessário.

6. **Docker**
   Comandos do tipo `docker` são executados em containers de forma isolada.


---

## **Roadmap – runner-v2** 🚀

### **Curto Prazo (rápida entrega / impacto imediato)** 🟢

Essas são features de **alto valor e baixa complexidade**, ideais para validar a base do runner:

| Feature                     | Justificativa / Valor                                                           | Emoji  | Complexidade |
| --------------------------- | ------------------------------------------------------------------------------- | ------ | ------------ |
| Execução local offline      | Permite testes rápidos e desenvolvimento sem depender de rede.                  | 🖥️    | 2            |
| Modo Dry-run                | Simula execução sem alterar nada, ideal para validação e segurança.             | 🕵️‍♂️ | 2            |
| Alertas visuais no terminal | Facilita interpretação de logs e melhora produtividade.                         | 🎨     | 1            |
| Pré-checks e validações     | Evita falhas desnecessárias e aumenta confiabilidade.  **exemplo** : verificar existencia de arquivos e diretórios, accesso a urls e etc                         | ✅      | 2            |
| Integração CI/CD            | Permite acionamento do runner em pipelines existentes, chamando a partir de um repositório independente do zuul e garantindo uso imediato. | 🔗     | 2            |

---

### **Médio Prazo (impacto estratégico / complexidade moderada)** 🟡

Features que agregam **segurança, flexibilidade e rastreabilidade**:

| Feature                                | Justificativa / Valor                                                                                | Emoji | Complexidade |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------- | ----- | ------------ |
| Pipeline condicional                   | Fluxos inteligentes baseados em resultados anteriores, reduzindo retrabalho e aumentando eficiência. | 🔀    | 3            |
| Templates dinâmicos                    | Reuso e parametrização de scripts, reduzindo erros e duplicação.                                     | 📝    | 3            |
| Integração com Vault                   | Segurança aprimorada, evitando hardcoding de credenciais.                                            | 🔐    | 3            |
| Auditoria completa                     | Rastreabilidade e compliance garantidas.                                                             | 📊    | 3            |
| Suporte multi-OS                       | Maior compatibilidade e flexibilidade de execução em diferentes sistemas.                            | 🌐    | 3            |
| Logs centralizados                     | Facilita análise e auditoria de execuções.                                                           | 🗃️   | 3            |
| Versionamento de scripts               | Garantia de consistência em diferentes versões de scripts ou roles.                                  | 🗂️   | 3            |
| Parâmetros dinâmicos por pipeline      | Personalização por branch, workflow ou ambiente, aumentando flexibilidade.                           | ⚙️    | 3            |
| Suporte a múltiplos workflows por repo | Organização e manutenção facilitadas em projetos complexos.                                          | 🔄    | 3            |

---

### **Longo Prazo (alto valor / alta complexidade)** 🔴

Features que demandam **integração avançada, automação e paralelismo**, mas trarão grande impacto no desempenho e confiabilidade:

| Feature                           | Justificativa / Valor                                                                                            | Emoji | Complexidade |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ----- | ------------ |
| Execução via Zuul com repo remoto | Atualização centralizada e colaboração entre equipes, permitindo rodar workflows direto de repositórios remotos. | 🔄    | 4            |
| Rollback automático               | Reduz risco em ambientes de produção revertendo alterações automaticamente em falhas.                            | ⏪     | 5            |
| Paralelismo avançado              | Reduz tempo total de execução mantendo dependências entre tarefas.                                               | ⚡     | 4            |
| Suporte a Kubernetes              | Integração com clusters cloud-native, essencial para pipelines modernos.                                         | ☸️    | 4            |
| Checkpoint / Resume               | Retoma execuções parciais após falhas, evitando retrabalho em pipelines longos.                                  | ⏩     | 4            |
| Suporte a múltiplos repositórios  | Combinação de arquivos de diferentes repositórios, essencial para ambientes distribuídos ou microserviços.       | 🔄    | 4            |
| Métricas de performance           | Permite monitoramento e otimização de pipelines com base em consumo de tempo e recursos.                         | ⏱️    | 3            |

## Fluxo do Runner v2

```mermaid
flowchart TD
    %% Estilos de nós
    classDef inicioFim fill:#DDEEFF,stroke:#333,stroke-width:2px;
    classDef processo fill:#E0FFD8,stroke:#333,stroke-width:1.5px;
    classDef decisao fill:#FFF2CC,stroke:#333,stroke-width:2px;
    classDef feature fill:#FFDDE0,stroke:#333,stroke-width:1px;
    classDef log fill:#F0E6FF,stroke:#333,stroke-width:1px;

    %% Fluxo principal
    A[Início da Execução]:::inicioFim --> B[Pré-checks e Validações]:::processo
    B --> C{Clonar Repositório Remoto? 🔄}:::decisao
    C -- Sim --> D[Selecionar workflow do repo: .zuul-runner/workflow.yml]:::processo
    C -- Não --> E[Usar workflow local]:::processo
    D --> F[Injetar parâmetros e variáveis dinâmicas ⚙️📝]:::processo
    E --> F
    F --> G[Executar pipeline principal]:::processo

    %% Pipeline Features agrupadas
    subgraph PIPELINE[Pipeline Features 🚀]
        direction TB
        G1[Pipeline condicional 🔀]:::feature
        G2[Paralelismo avançado ⚡]:::feature
        G3[Suporte Kubernetes ☸️]:::feature
        G4[Integração Vault 🔐]:::feature
        G5[Templates dinâmicos 📝]:::feature
        G6[Modo Dry-run 🕵️‍♂️]:::feature
        G7[Rollback automático ⏪]:::feature
        G8[Checkpoint / Resume ⏩]:::feature
        G9[Suporte multi-OS 🌐]:::feature
        G10[Versionamento de scripts 🗂️]:::feature
    end

    G --> PIPELINE
    PIPELINE --> H[Logs e Auditoria 📊🎨]:::log
    H --> I[Métricas de performance ⏱️]:::log
    I --> J[Conclusão]:::inicioFim
