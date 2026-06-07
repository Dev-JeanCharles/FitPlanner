[![NPM](https://img.shields.io/npm/l/react)](https://github.com/Dev-JeanCharles/FitPlanner/blob/master/LICENSE) 
 # 💪 FitPlanner
  
  Sistema baseado em microsserviços para controle completo de treinos, nutrição, metas e rotina de pessoas que praticam musculação.
  
  ---
  
  ## 📋 Índice
  
  - [Visão Geral](#visão-geral)
  - [Arquitetura](#arquitetura)
  - [Microsserviços](#microsserviços)
  - [Stack Tecnológica](#stack-tecnológica)
  - [Comunicação entre Serviços](#comunicação-entre-serviços)
  - [ADRs — Decisões Arquiteturais](#adrs--decisões-arquiteturais)
  - [Como Executar](#como-executar)
  - [Roadmap](#roadmap)
  
  ---
  
  ## Visão Geral
  
  O FitPlanner oferece:
  
  - **Controle de treinos** — divisão de treinos, exercícios por dia, histórico de alterações
  - **Registro nutricional** — calorias, macros, controle de água
  - **Metas mensais** — cálculo inteligente baseado nos dados nutricionais do usuário
  - **Calendário** — exames nutricionais, consultas, eventos importantes
  - **Lista de compras** — semanal ou mensal
  - **Notificações** — lembretes de metas, eventos e prazos
  - **Onboarding** — plano personalizado criado automaticamente após cadastro
  
  ---
  
  ## Arquitetura
  O diagrama completo de comunicação entre os microsserviços está disponível no arquivo draw.io:
  📎 [`docs/diagrams/fitplanner-communication.drawio`](docs/diagrams/fitplanner-communication.drawio)
  
  > Para visualizar: abra no [draw.io](https://app.diagrams.net) (File → Open from → Device) ou no VS Code com a extensão **Draw.io Integration**.
  
  ![Diagrama de Arquitetura](docs/diagrams/fitplanner-communication.drawio.svg)
  ---
  
  ## Microsserviços
  
  | Serviço | Responsabilidade | Banco | Arquitetura |
  |---------|-----------------|-------|-------------|
  | **user-service** | Cadastro, perfil, imagem, onboarding, autenticação OAuth2, autorização JWT | PostgreSQL | Hexagonal |
  | **workout-service** | Divisão de treinos, exercícios, histórico de alterações | PostgreSQL | Hexagonal |
  | **nutrition-service** | Registro nutricional, gastos calóricos, controle de água | PostgreSQL | Hexagonal |
  | **goal-service** | Metas mensais, cálculo de cenário ideal | PostgreSQL | Hexagonal |
  | **calendar-service** | Eventos, exames, compromissos, recorrência | MongoDB | Clean simplificada |
  | **shopping-list-service** | Listas de compras semanal/mensal | MongoDB | Clean simplificada |
  | **notification-service** | Notificações (e-mail, push, in-app) | MongoDB | Clean simplificada |
  | **api-gateway** | Roteamento, validação JWT, rate limiting | — | — |
  | **bff-service** | Composição de respostas para frontend | — | — |
  
  ---
  
  ## Stack Tecnológica
  
  | Camada | Tecnologia |
  |--------|-----------|
  | Linguagem | Java 21 |
  | Framework | Spring Boot 3.x |
  | Autenticação | Spring Authorization Server + OAuth2 + JWT |
  | Comunicação síncrona | OpenFeign |
  | Mensageria | AWS SQS |
  | Banco relacional | PostgreSQL |
  | Banco não relacional | MongoDB |
  | Gateway | Spring Cloud Gateway |
  | Documentação API | Swagger / SpringDoc OpenAPI |
  | Containerização | Docker + Docker Compose |
  
  ---
  
  ## Comunicação entre Serviços
  
  ### Síncrona (OpenFeign)
  
  | Origem | Destino | Motivo |
  |--------|---------|--------|
  | bff-service | todos | Composição de telas para frontend |
  | goal-service | nutrition-service | Buscar dados nutricionais para cálculo de metas |
  
  ### Assíncrona (AWS SQS)
  
  | Produtor | Evento | Consumidor(es) |
  |----------|--------|----------------|
  | user-service | `onboarding.completed` | workout-service, nutrition-service, goal-service |
  | nutrition-service | `nutrition.updated` | goal-service |
  | calendar-service | `event.created` | notification-service |
  | goal-service | `goal.deadline.approaching` | notification-service |
  
  ---
  ## ADRs — Decisões Arquiteturais
  
  ### ADR-001: Comunicação entre Microsserviços
  
  **Status:** Aceita
  
  **Contexto:** Microsserviços precisam trocar dados. Algumas operações exigem resposta imediata, outras são eventuais.
  
  **Decisão:**
  - Comunicação síncrona via **OpenFeign** para operações que precisam de resposta imediata (ex: BFF compondo tela, goal-service buscando dados nutricionais)
  - Comunicação assíncrona via **AWS SQS** para eventos que não precisam de resposta (ex: onboarding concluído, notificações)
  
  **Consequências:**
  - Desacoplamento entre serviços para operações assíncronas
  - Resiliência: falha em um consumidor não impacta o produtor
  - Consistência eventual para dados propagados via eventos
  - Custo de complexidade para monitorar filas e dead-letter queues
  
  ---
  
  ### ADR-002: Arquitetura Hexagonal Seletiva
  
  **Status:** Aceita
  
  **Contexto:** Nem todos os serviços possuem lógica de domínio complexa. Aplicar hexagonal em serviços CRUD gera over-engineering.
  
  **Decisão:**
  - **Hexagonal (Ports & Adapters):** user-service, workout-service, nutrition-service, goal-service
  - **Clean simplificada (Controller → Service → Repository):** calendar-service, shopping-list-service, notification-service
  
  **Consequências:**
  - Serviços com domínio rico ficam testáveis e isolados de frameworks
  - Serviços simples mantêm agilidade no desenvolvimento
  - Duas convenções de estrutura no projeto (documentadas)
  
  ---
  
  ### ADR-003: Autenticação e Autorização
  
  **Status:** Aceita
  
  **Contexto:** O sistema precisa de autenticação segura e controle de acesso por papéis.
  
  **Decisão:**
  - **Spring Authorization Server** como provedor OAuth2, integrado ao user-service
  - **JWT** como token de acesso (stateless)
  - Validação de JWT no **API Gateway** (todas as requests são validadas antes de chegar aos serviços)
  - Roles iniciais: `USER`, `ADMIN`
  
  **Consequências:**
  - Tokens stateless eliminam necessidade de sessão compartilhada
  - Gateway centraliza validação de segurança
  - Serviços downstream confiam no token já validado (recebem claims via headers)
  
  ---
  
  ### ADR-004: Observabilidade (Futuro)
  
  **Status:** Adiada
  
  **Contexto:** Em produção, será necessário rastrear requests entre microsserviços e centralizar logs.
  
  **Decisão planejada:**
  - Spring Actuator + Micrometer para métricas
  - OpenTelemetry para tracing distribuído
  - Logs centralizados (ELK ou CloudWatch)
  
  **Consequências:**
  - Será implementado em fase posterior para não atrasar o MVP
  
  ---
  
  ### ADR-005: API Gateway e BFF
  
  **Status:** Aceita
  
  **Contexto:** O frontend precisa de endpoints compostos e o sistema precisa de um ponto único de entrada.
  
  **Decisão:**
  - **Spring Cloud Gateway** como API Gateway (roteamento, JWT validation, rate limiting)
  - **BFF Service** separado para composição de respostas voltadas ao frontend
  
  **Consequências:**
  - Gateway leve e focado em infraestrutura
  - BFF absorve a complexidade de compor múltiplas chamadas
  - Frontend faz poucas requests com payloads otimizados
  
  ---
  
  ## Como Executar
  
  ```bash
  # Clonar repositório
  git clone https://github.com/seu-usuario/fitplanner.git
  cd fitplanner
  
  # Subir infraestrutura local
  docker-compose up -d
  
  # Cada serviço pode ser executado individualmente
  cd user-service
  ./mvnw spring-boot:run
  
  Pré-requisitos:
  
  - Java 21
  - Docker + Docker Compose
  - Maven 3.9+
  ```
  ---
  
  ## Roadmap
  
  - [x] Definição da arquitetura e microsserviços
  - [x] Modelagem de dados (MER/DER)
  - [x] Diagramas de comunicação e sequência
  - [x] Estrutura de pastas
  - [x] ADRs documentadas
  - [ ] Implementação do user-service (auth + onboarding)
  - [ ] Implementação do workout-service
  - [ ] Implementação do nutrition-service
  - [ ] Implementação do goal-service
  - [ ] Implementação do calendar-service
  - [ ] Implementação do shopping-list-service
  - [ ] Implementação do notification-service
  - [ ] API Gateway + BFF
  - [ ] Docker Compose completo
  - [ ] Observabilidade (ADR-004)
  - [ ] Frontend
  
