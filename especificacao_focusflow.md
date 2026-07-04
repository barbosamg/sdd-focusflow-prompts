## Especificação do projeto: FocusFlow

### 1. Visão geral

O FocusFlow é uma aplicação web de gerenciamento de tempo baseada na técnica Pomodoro.

O objetivo do projeto é ajudar usuários a organizar sessões de foco, registrar o tempo trabalhado por projeto, classificar o tipo de atividade executada e consultar um histórico de produtividade.

O sistema será construído com:

Backend:
- Node.js
- Express
- TypeScript
- Prisma
- PostgreSQL
- Zod
- Jest

Frontend:
- React
- TypeScript
- Vite
- React Router
- Axios
- React Hook Form
- Zod

---

### 2. Objetivo do produto

Criar uma aplicação simples, funcional e escalável para controle de produtividade pessoal, permitindo que o usuário:

- cadastre projetos
- cadastre ou utilize tipos de atividade
- inicie sessões de foco no estilo Pomodoro
- pause, retome, finalize ou cancele sessões
- registre histórico de tempo trabalhado
- visualize métricas de produtividade no dashboard
- filtre sessões por período, projeto, tipo e status

---

### 3. Público-alvo

O FocusFlow é voltado para:

- desenvolvedores
- freelancers
- estudantes
- criadores de conteúdo
- profissionais de produto
- pessoas que trabalham com múltiplas tarefas ou projetos
- qualquer pessoa que queira controlar melhor o tempo de foco

---

### 4. Funcionalidades principais

#### 4.1 Projetos

O usuário poderá criar, editar, listar e arquivar projetos.

Cada projeto representa uma área, cliente, produto ou frente de trabalho.

Exemplos de projetos genéricos:

- E-commerce
- Gerenciador de Tarefas
- Controle Financeiro
- Diário / Blog Pessoal
- Agenda de Compromissos
- Estudos Técnicos
- Bugs e Manutenção
- Refatorações

Cada projeto deverá conter:

- nome
- descrição opcional
- cor de identificação
- status: ativo ou arquivado
- data de criação
- data de atualização

Regras:

- O nome do projeto é obrigatório.
- A cor é opcional, mas deve ter um valor padrão.
- Projetos arquivados não devem aparecer na lista de seleção ao iniciar uma nova sessão.
- Projetos com sessões vinculadas não devem ser excluídos definitivamente.
- Projetos com histórico devem ser arquivados, não deletados.

---

#### 4.2 Tipos de atividade

O usuário poderá classificar uma sessão de foco por tipo de atividade.

Exemplos:

- Desenvolvimento
- Estudo
- Reunião
- Planejamento
- Revisão de código
- Documentação
- Correção de bugs
- Refatoração
- Testes
- Pesquisa

Cada tipo de atividade deverá conter:

- nome
- descrição opcional
- data de criação
- data de atualização

Regras:

- O nome do tipo de atividade é obrigatório.
- O sistema deve ter alguns tipos iniciais criados via seed.
- O usuário precisa selecionar um tipo de atividade para iniciar uma sessão.

---

#### 4.3 Sessões Pomodoro

O usuário poderá iniciar uma sessão de foco informando:

- projeto
- tipo de atividade
- duração do foco em minutos
- duração da pausa em minutos
- observações opcionais ao finalizar

A sessão poderá ter os seguintes status:

- running
- paused
- completed
- canceled
- interrupted

Ações disponíveis:

- iniciar sessão
- pausar sessão
- retomar sessão
- finalizar sessão
- cancelar sessão

Regras:

- Uma sessão só pode ser iniciada se houver projeto ativo selecionado.
- Uma sessão só pode ser iniciada se houver tipo de atividade selecionado.
- A duração mínima do foco deve ser de 5 minutos.
- A duração máxima do foco deve ser de 120 minutos.
- A duração da pausa deve ser opcional.
- A pausa não conta como tempo produtivo.
- Ao finalizar uma sessão, o sistema deve registrar o histórico automaticamente.
- Sessões canceladas não devem entrar no cálculo do dashboard.
- Sessões interrompidas podem entrar parcialmente no histórico, desde que tenham tempo real registrado.
- Sessões canceladas com menos de 1 minuto de duração real podem ser descartadas do histórico.
- O histórico deve considerar apenas o tempo efetivamente trabalhado.

---

#### 4.4 Histórico

O sistema deverá registrar as sessões finalizadas, interrompidas ou canceladas conforme as regras de negócio.

Cada registro de histórico deverá exibir:

- data
- projeto
- tipo de atividade
- horário de início
- horário de fim
- duração planejada
- duração real
- status
- observações

Filtros obrigatórios:

- período inicial
- período final
- projeto
- tipo de atividade
- status

Regras:

- O histórico deve ser ordenado por data mais recente primeiro.
- O usuário deve conseguir visualizar o total de tempo do período filtrado.
- Sessões canceladas não devem entrar nos totais produtivos.
- Sessões concluídas entram integralmente.
- Sessões interrompidas entram parcialmente, se tiverem tempo real registrado.

---

#### 4.5 Dashboard

O dashboard deverá apresentar um resumo da produtividade do usuário.

Métricas principais:

- tempo focado hoje
- tempo focado na semana
- total de sessões concluídas
- projeto com maior tempo investido
- distribuição de tempo por tipo de atividade
- distribuição de tempo por projeto

Regras:

- O total diário deve considerar sessões concluídas no dia atual.
- O total semanal deve considerar a semana de segunda a domingo.
- Sessões canceladas não entram no dashboard.
- Sessões concluídas entram nos totais.
- Sessões interrompidas entram apenas com o tempo efetivamente trabalhado.
- O dashboard deve retornar valores em segundos ou minutos de forma consistente.

---

### 5. Modelo de dados

#### 5.1 Project

Representa um projeto ou área de foco.

Campos:

- id: string UUID
- name: string obrigatório
- description: string opcional
- color: string opcional
- status: active | archived
- createdAt: DateTime
- updatedAt: DateTime

Relacionamentos:

- Project possui muitas FocusSessions.

---

#### 5.2 ActivityType

Representa o tipo de atividade executada durante uma sessão.

Campos:

- id: string UUID
- name: string obrigatório
- description: string opcional
- createdAt: DateTime
- updatedAt: DateTime

Relacionamentos:

- ActivityType possui muitas FocusSessions.

---

#### 5.3 FocusSession

Representa uma sessão de foco.

Campos:

- id: string UUID
- projectId: string obrigatório
- activityTypeId: string obrigatório
- plannedFocusMinutes: number obrigatório
- plannedBreakMinutes: number opcional
- actualFocusSeconds: number
- startedAt: DateTime
- finishedAt: DateTime opcional
- status: running | paused | completed | canceled | interrupted
- notes: string opcional
- createdAt: DateTime
- updatedAt: DateTime

Relacionamentos:

- FocusSession pertence a Project.
- FocusSession pertence a ActivityType.

---

### 6. Contratos da API

A API deverá seguir padrão REST.

Base URL:

```txt
/api
```

#### 6.1 Projetos

Criar projeto:

```http
POST /api/projects
```

Payload:

```json
{
  "name": "E-commerce",
  "description": "Plataforma de vendas online com catálogo, carrinho e pedidos.",
  "color": "#A3FF12"
}
```

Resposta esperada:

```json
{
  "id": "uuid",
  "name": "E-commerce",
  "description": "Plataforma de vendas online com catálogo, carrinho e pedidos.",
  "color": "#A3FF12",
  "status": "active",
  "createdAt": "2026-07-04T10:00:00.000Z",
  "updatedAt": "2026-07-04T10:00:00.000Z"
}
```

Listar projetos:

```http
GET /api/projects
```

Listar projetos ativos:

```http
GET /api/projects?status=active
```

Editar projeto:

```http
PUT /api/projects/:id
```

Arquivar projeto:

```http
PATCH /api/projects/:id/archive
```

---

#### 6.2 Tipos de atividade

Criar tipo de atividade:

```http
POST /api/activity-types
```

Payload:

```json
{
  "name": "Desenvolvimento",
  "description": "Implementação de funcionalidades."
}
```

Listar tipos de atividade:

```http
GET /api/activity-types
```

---

#### 6.3 Sessões de foco

Iniciar sessão:

```http
POST /api/focus-sessions/start
```

Payload:

```json
{
  "projectId": "uuid",
  "activityTypeId": "uuid",
  "plannedFocusMinutes": 25,
  "plannedBreakMinutes": 5
}
```

Pausar sessão:

```http
PATCH /api/focus-sessions/:id/pause
```

Retomar sessão:

```http
PATCH /api/focus-sessions/:id/resume
```

Finalizar sessão:

```http
PATCH /api/focus-sessions/:id/finish
```

Payload:

```json
{
  "notes": "Implementei endpoints principais e ajustes de validação."
}
```

Cancelar sessão:

```http
PATCH /api/focus-sessions/:id/cancel
```

---

#### 6.4 Histórico

Listar histórico:

```http
GET /api/focus-sessions/history
```

Filtros suportados:

```txt
?projectId=uuid
&activityTypeId=uuid
&startDate=2026-07-01
&endDate=2026-07-31
&status=completed
```

Resposta esperada:

```json
{
  "items": [
    {
      "id": "uuid",
      "project": {
        "id": "uuid",
        "name": "E-commerce"
      },
      "activityType": {
        "id": "uuid",
        "name": "Desenvolvimento"
      },
      "plannedFocusMinutes": 25,
      "actualFocusSeconds": 1500,
      "startedAt": "2026-07-04T10:00:00.000Z",
      "finishedAt": "2026-07-04T10:25:00.000Z",
      "status": "completed",
      "notes": "Implementei a tela inicial."
    }
  ],
  "totalFocusSeconds": 1500
}
```

---

#### 6.5 Dashboard

Resumo do dashboard:

```http
GET /api/dashboard/summary
```

Resposta esperada:

```json
{
  "todayFocusSeconds": 7200,
  "weekFocusSeconds": 28800,
  "completedSessions": 12,
  "topProject": {
    "id": "uuid",
    "name": "E-commerce",
    "totalSeconds": 14400
  },
  "byActivityType": [
    {
      "id": "uuid",
      "name": "Desenvolvimento",
      "totalSeconds": 18000
    },
    {
      "id": "uuid",
      "name": "Documentação",
      "totalSeconds": 7200
    }
  ],
  "byProject": [
    {
      "id": "uuid",
      "name": "E-commerce",
      "totalSeconds": 14400
    }
  ]
}
```

---

### 7. Telas do frontend

#### 7.1 Dashboard

A tela de dashboard deverá exibir:

- card com tempo focado hoje
- card com tempo focado na semana
- card com sessões concluídas
- card com projeto mais trabalhado
- gráfico ou lista de tempo por projeto
- gráfico ou lista de tempo por tipo de atividade
- botão para iniciar nova sessão

Estados obrigatórios:

- carregando
- erro ao buscar dados
- sem dados suficientes

---

#### 7.2 Timer Pomodoro

A tela de timer deverá conter:

- select de projeto ativo
- select de tipo de atividade
- campo de duração do foco
- campo de duração da pausa
- timer central grande
- botão iniciar
- botão pausar
- botão retomar
- botão finalizar
- botão cancelar
- campo de observações ao finalizar

Regras de interface:

- Antes de iniciar, o usuário precisa selecionar projeto e tipo de atividade.
- Durante a sessão, os campos principais devem ficar bloqueados.
- Ao pausar, o botão de retomar deve aparecer.
- Ao finalizar, deve abrir campo ou modal para observações.
- Ao cancelar, deve pedir confirmação.

---

#### 7.3 Projetos

A tela de projetos deverá permitir:

- listar projetos
- criar projeto
- editar projeto
- arquivar projeto
- visualizar status
- visualizar tempo total por projeto, se disponível

Campos do formulário:

- nome
- descrição
- cor

Estados obrigatórios:

- lista vazia
- erro ao carregar
- loading ao salvar
- confirmação ao arquivar

---

#### 7.4 Histórico

A tela de histórico deverá conter:

- filtro por data inicial
- filtro por data final
- filtro por projeto
- filtro por tipo de atividade
- filtro por status
- tabela com sessões
- totalizador do período

Colunas da tabela:

- data
- projeto
- tipo de atividade
- início
- fim
- duração
- status
- observações

Estados obrigatórios:

- carregando
- erro ao buscar histórico
- nenhum registro encontrado

---

### 8. Fluxos principais do usuário

#### Fluxo 1: Criar projeto

1. Usuário acessa a tela de projetos.
2. Clica em novo projeto.
3. Informa nome, descrição e cor.
4. Salva o projeto.
5. O projeto aparece na listagem como ativo.

---

#### Fluxo 2: Iniciar sessão Pomodoro

1. Usuário acessa a tela de timer.
2. Seleciona um projeto ativo.
3. Seleciona um tipo de atividade.
4. Define duração do foco.
5. Define duração da pausa, se quiser.
6. Clica em iniciar.
7. O timer começa a contar.
8. A sessão fica com status running.

---

#### Fluxo 3: Pausar e retomar sessão

1. Usuário inicia uma sessão.
2. Clica em pausar.
3. O timer para.
4. A sessão fica com status paused.
5. Usuário clica em retomar.
6. O timer continua.
7. A sessão volta para running.

---

#### Fluxo 4: Finalizar sessão

1. Usuário está com uma sessão em andamento ou pausada.
2. Clica em finalizar.
3. Informa observações opcionais.
4. O sistema calcula o tempo efetivamente trabalhado.
5. A sessão é marcada como completed.
6. A sessão aparece no histórico.
7. O dashboard passa a considerar essa sessão.

---

#### Fluxo 5: Cancelar sessão

1. Usuário está com uma sessão em andamento ou pausada.
2. Clica em cancelar.
3. O sistema pede confirmação.
4. Se confirmado, a sessão é marcada como canceled.
5. Sessões canceladas não entram no dashboard.
6. Sessões com menos de 1 minuto real podem ser descartadas do histórico.

---

### 9. Validações

#### Projetos

- name é obrigatório.
- name deve ter pelo menos 2 caracteres.
- color deve ser uma cor hexadecimal válida, se informada.

#### Tipos de atividade

- name é obrigatório.
- name deve ter pelo menos 2 caracteres.

#### Sessões

- projectId é obrigatório.
- activityTypeId é obrigatório.
- projectId precisa apontar para um projeto existente e ativo.
- activityTypeId precisa apontar para um tipo existente.
- plannedFocusMinutes precisa ser número.
- plannedFocusMinutes mínimo: 5.
- plannedFocusMinutes máximo: 120.
- plannedBreakMinutes deve ser número positivo, se informado.

---

### 10. Tratamento de erros

A API deverá retornar erros padronizados.

Exemplo:

```json
{
  "error": {
    "message": "Projeto não encontrado.",
    "code": "PROJECT_NOT_FOUND",
    "statusCode": 404
  }
}
```

Erros esperados:

- 400: payload inválido
- 404: recurso não encontrado
- 409: conflito de regra de negócio
- 500: erro interno inesperado

Regras:

- Não expor stack trace em ambiente de produção.
- Mensagens de erro devem ser claras.
- Validações devem ser feitas com Zod.
- Controllers não devem conter regras de negócio.

---

### 11. Estrutura sugerida do backend

```txt
backend/
  src/
    modules/
      projects/
        project.routes.ts
        project.controller.ts
        project.service.ts
        project.schemas.ts
      activityTypes/
        activityType.routes.ts
        activityType.controller.ts
        activityType.service.ts
        activityType.schemas.ts
      focusSessions/
        focusSession.routes.ts
        focusSession.controller.ts
        focusSession.service.ts
        focusSession.schemas.ts
      dashboard/
        dashboard.routes.ts
        dashboard.controller.ts
        dashboard.service.ts
    shared/
      errors/
        AppError.ts
        errorHandler.ts
      prisma/
        client.ts
    app.ts
    server.ts
  prisma/
    schema.prisma
    seed.ts
  tests/
```

Regras:

- Routes apenas definem as rotas.
- Controllers recebem request e response.
- Services concentram regras de negócio.
- Schemas validam entrada de dados.
- Prisma fica isolado em client compartilhado.
- Erros devem usar AppError.

---

### 12. Estrutura sugerida do frontend

```txt
frontend/
  src/
    pages/
      Dashboard/
      Timer/
      Projects/
      History/
    components/
      Layout/
      Button/
      Card/
      Input/
      Select/
      TimerDisplay/
      ProjectForm/
      HistoryTable/
      EmptyState/
      LoadingState/
      ErrorState/
    services/
      api.ts
      projectService.ts
      activityTypeService.ts
      focusSessionService.ts
      dashboardService.ts
    hooks/
      usePomodoroTimer.ts
      useProjects.ts
      useActivityTypes.ts
      useDashboard.ts
      useHistory.ts
    schemas/
      projectSchema.ts
      focusSessionSchema.ts
    types/
      project.ts
      activityType.ts
      focusSession.ts
      dashboard.ts
    routes/
      AppRoutes.tsx
    App.tsx
    main.tsx
```

Regras:

- Services centralizam chamadas HTTP.
- Hooks concentram lógica reutilizável.
- Páginas montam fluxos.
- Componentes devem ser reutilizáveis.
- Formulários devem usar React Hook Form e Zod.
- Axios deve usar uma instância centralizada.

---

### 13. Critérios de aceite

#### Backend

- Deve ser possível criar projeto.
- Deve ser possível listar projetos.
- Deve ser possível listar apenas projetos ativos.
- Deve ser possível editar projeto.
- Deve ser possível arquivar projeto.
- Deve ser possível listar tipos de atividade.
- Deve haver seed com tipos de atividade iniciais.
- Deve ser possível iniciar sessão.
- Deve ser possível pausar sessão.
- Deve ser possível retomar sessão.
- Deve ser possível finalizar sessão.
- Deve ser possível cancelar sessão.
- Sessão finalizada deve aparecer no histórico.
- Dashboard deve calcular totais corretamente.
- Payloads inválidos devem retornar erro 400.
- Entidades inexistentes devem retornar erro 404.
- Regras de negócio devem estar em services.
- Controllers não devem conter lógica de negócio.
- Testes automatizados devem cobrir as principais regras.

#### Frontend

- Usuário consegue criar projeto.
- Usuário consegue visualizar projetos.
- Usuário consegue arquivar projeto.
- Usuário consegue iniciar sessão Pomodoro.
- Timer exibe tempo restante corretamente.
- Usuário consegue pausar e retomar sessão.
- Usuário consegue finalizar sessão com observações.
- Usuário consegue cancelar sessão com confirmação.
- Histórico exibe sessões registradas.
- Histórico permite filtros.
- Dashboard exibe totais.
- Interface trata loading, erro e estado vazio.
- Layout funciona em desktop e mobile.
- Formulários possuem validação.

---

### 14. Testes necessários

#### Backend

Testes de projetos:

- criar projeto com sucesso
- bloquear projeto sem nome
- listar projetos ativos
- arquivar projeto
- impedir uso de projeto arquivado em nova sessão

Testes de tipos de atividade:

- listar tipos criados por seed
- criar tipo de atividade
- bloquear tipo sem nome

Testes de sessões:

- iniciar sessão válida
- impedir sessão sem projeto
- impedir sessão sem tipo de atividade
- impedir sessão com projeto arquivado
- impedir foco menor que 5 minutos
- impedir foco maior que 120 minutos
- pausar sessão
- retomar sessão
- finalizar sessão
- cancelar sessão
- garantir que sessão finalizada gera histórico

Testes de dashboard:

- calcular tempo do dia
- calcular tempo da semana
- ignorar sessões canceladas
- considerar sessões concluídas
- considerar tempo real de sessões interrompidas

#### Frontend

Testes manuais ou automatizados:

- renderizar dashboard
- exibir loading
- exibir erro
- criar projeto
- validar formulário de projeto
- iniciar timer
- pausar timer
- retomar timer
- finalizar sessão
- cancelar sessão
- carregar histórico
- aplicar filtros
- validar responsividade

---

### 15. Segurança

Pontos mínimos:

- Validar todos os payloads com Zod.
- Não confiar em dados enviados pelo frontend.
- Não expor stack trace em produção.
- Padronizar mensagens de erro.
- Sanitizar campos de texto se forem exibidos diretamente.
- Preparar estrutura para autenticação futura, mesmo que a primeira versão não tenha login.
- Evitar regras críticas apenas no frontend.

---

### 16. Performance

Pontos mínimos:

- Paginar histórico se houver muitos registros.
- Indexar campos usados em filtros:
  - projectId
  - activityTypeId
  - startedAt
  - status
- Evitar recalcular métricas pesadas no frontend.
- Dashboard deve ser calculado no backend.
- Frontend deve tratar loading e evitar chamadas duplicadas.
- Usar filtros no backend, não apenas no frontend.

---

### 17. UX

Pontos mínimos:

- Timer precisa ter destaque visual.
- Botões devem deixar claro o estado atual da sessão.
- Ao cancelar sessão, pedir confirmação.
- Ao finalizar sessão, permitir observações.
- Exibir mensagens claras de erro.
- Exibir estados vazios amigáveis.
- Layout responsivo para desktop e mobile.
- Inputs devem ter labels claros.
- O usuário deve entender rapidamente quanto tempo focou hoje e na semana.

---

### 18. Fora do escopo da primeira versão

A primeira versão não precisa ter:

- autenticação
- múltiplos usuários
- cobrança
- integração com calendário
- notificações push
- app mobile
- relatórios avançados em PDF
- gráficos complexos
- times/equipes
- permissões por usuário

Esses itens podem ser considerados em versões futuras.

---

### 19. Resultado esperado

Ao final da implementação, o FocusFlow deve permitir que o usuário controle seu tempo de foco de forma simples e confiável.

O sistema deve ter:

- backend organizado por módulos
- banco de dados relacional com Prisma
- validações com Zod
- API REST clara
- frontend em React com páginas principais
- timer funcional
- histórico filtrável
- dashboard com métricas úteis
- regras de negócio testadas
- código organizado e preparado para evolução