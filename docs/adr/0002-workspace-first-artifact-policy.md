# Workspace-first artifact policy for NDA repos

Este fork é instalado em repos corporativos sujeitos a NDAs com terceiros. Dois requisitos não-negociáveis guiam o design: (C) nenhuma skill pode chamar `gh`, `glab`, ou qualquer serviço externo; (D) nenhum artifact gerado com auxílio de IA entra no histórico oficial do git. A convenção upstream (`mattpocock/skills`) viola ambos — escreve config em `docs/agents/`, publica issues diretamente no GitHub/GitLab, e escreve `CONTEXT.md` em paths trackeados.

Decidimos que `.workspace/` é a fonte de verdade exclusiva para todos os artifacts gerados pelas skills em repos corporativos. A pasta é git-ignorada por padrão e inicializada por `setup-matt-pocock-skills-safe`. As skills neste fork leem apenas de `.workspace/agents/` — sem fallback para `docs/agents/`. Um colega que clonar o repo sem inicializar o `.workspace/` verá as skills falharem explicitamente, o que é intencional: força a execução do setup antes do uso.

## Considered Options

**Stub em `docs/agents/`** apontando para `.workspace/` foi rejeitado: manteria `docs/agents/` no git, criando um arquivo trackeado que referencia conteúdo sensível git-ignorado — confuso e potencialmente revelador da existência da config.
