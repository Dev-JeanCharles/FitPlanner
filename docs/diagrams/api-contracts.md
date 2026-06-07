  # 📡 Contratos de API — FitPlanner
  
  Documentação dos endpoints, payloads e padrões de cada microsserviço.
  
  ---
  
  ## Padrões Transversais
  
  ### Headers obrigatórios
  
  Authorization: Bearer {access_token}
  Content-Type: application/json
  
  ### Resposta de erro padrão
  
  ```json
  {
    "timestamp": "2026-06-07T12:00:00Z",
    "status": 400,
    "error": "Bad Request",
    "message": "Campo 'email' é obrigatório",
    "path": "/api/users/register"
  }
  
  Paginação
  
  GET /api/resource?page=0&size=20&sort=created_at,desc
  
  {
    "content": [],
    "page": 0,
    "size": 20,
    "total_elements": 45,
    "total_pages": 3
  }
  
  HTTP Status
  
  ┌────────┬────────────────────────────────┐
  │ Status │ Uso                            │
  ├────────┼────────────────────────────────┤
  │ 200    │ Sucesso em GET/PUT/PATCH       │
  ├────────┼────────────────────────────────┤
  │ 201    │ Recurso criado (POST)          │
  ├────────┼────────────────────────────────┤
  │ 204    │ Sem conteúdo (DELETE)          │
  ├────────┼────────────────────────────────┤
  │ 400    │ Validação falhou               │
  ├────────┼────────────────────────────────┤
  │ 401    │ Não autenticado                │
  ├────────┼────────────────────────────────┤
  │ 403    │ Sem permissão                  │
  ├────────┼────────────────────────────────┤
  │ 404    │ Recurso não encontrado         │
  ├────────┼────────────────────────────────┤
  │ 409    │ Conflito (email/cpf duplicado) │
  ├────────┼────────────────────────────────┤
  │ 500    │ Erro interno                   │
  └────────┴────────────────────────────────┘
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  1. user-service
  
  Base URL: /api/users
  
  Endpoints
  
  ┌────────┬───────────────────┬────────────────────────────┬──────┐
  │ Método │ Endpoint          │ Descrição                  │ Auth │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ POST   │ /register         │ Cadastro de usuário        │ Não  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ POST   │ /login            │ Autenticação               │ Não  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ POST   │ /refresh-token    │ Renovar token              │ Não  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ GET    │ /me               │ Perfil do usuário logado   │ Sim  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ PUT    │ /me               │ Atualizar dados básicos    │ Sim  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ PATCH  │ /me/profile-image │ Upload de imagem           │ Sim  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ POST   │ /me/onboarding    │ Preencher onboarding       │ Sim  │
  ├────────┼───────────────────┼────────────────────────────┼──────┤
  │ GET    │ /me/profile       │ Dados do perfil/onboarding │ Sim  │
  └────────┴───────────────────┴────────────────────────────┴──────┘
  
  Payloads
  
  POST /register
  
  Request:
  
  {
    "name": "string",
    "email": "string",
    "cpf": "string",
    "password": "string"
  }
  
  Response 201:
  
  {
    "id": "uuid",
    "name": "string",
    "email": "string",
    "cpf": "string",
    "profile_image_url": null,
    "enabled": true,
    "created_at": "2026-06-07T12:00:00Z"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /login
  
  Request:
  
  {
    "email": "string",
    "password": "string"
  }
  
  Response 200:
  
  {
    "access_token": "string",
    "refresh_token": "string",
    "expires_in": 3600,
    "token_type": "Bearer"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /refresh-token
  
  Request:
  
  {
    "refresh_token": "string"
  }
  
  Response 200:
  
  {
    "access_token": "string",
    "refresh_token": "string",
    "expires_in": 3600,
    "token_type": "Bearer"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  GET /me
  
  Response 200:
  
  {
    "id": "uuid",
    "name": "string",
    "email": "string",
    "cpf": "string",
    "profile_image_url": "string",
    "enabled": true,
    "created_at": "2026-06-07T12:00:00Z"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  PUT /me
  
  Request:
  
  {
    "name": "string",
    "email": "string"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  PATCH /me/profile-image
  
  Request: multipart/form-data com campo image
  
  Response 200:
  
  {
    "profile_image_url": "string"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /me/onboarding
  
  Request:
  
  {
    "age": 25,
    "gender": "MALE",
    "height_cm": 175.0,
    "weight_kg": 80.0,
    "activity_level": "MODERATE"
  }
  
  Response 200:
  
  {
    "user_id": "uuid",
    "age": 25,
    "gender": "MALE",
    "height_cm": 175.0,
    "weight_kg": 80.0,
    "activity_level": "MODERATE",
    "onboarding_completed": true
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  GET /me/profile
  
  Response 200:
  
  {
    "user_id": "uuid",
    "age": 25,
    "gender": "MALE",
    "height_cm": 175.0,
    "weight_kg": 80.0,
    "activity_level": "MODERATE",
    "onboarding_completed": true
  }
  
  Enums
  
  - gender: MALE, FEMALE, OTHER
  - activity_level: SEDENTARY, LIGHT, MODERATE, ACTIVE, VERY_ACTIVE
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  2. fit-service
  
  Base URL: /api/fit
  
  Treinos
  
  ┌────────┬─────────────────────────┬────────────────────────────────────┐
  │ Método │ Endpoint                │ Descrição                          │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ POST   │ /splits                 │ Criar divisão de treino            │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ GET    │ /splits                 │ Listar splits do usuário           │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ GET    │ /splits/{id}            │ Detalhe com dias e exercícios      │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ PUT    │ /splits/{id}            │ Atualizar split                    │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ DELETE │ /splits/{id}            │ Desativar split                    │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ POST   │ /splits/{id}/days       │ Adicionar dia                      │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ PUT    │ /days/{id}              │ Atualizar dia                      │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ DELETE │ /days/{id}              │ Remover dia                        │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ POST   │ /days/{id}/exercises    │ Adicionar exercício                │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ PUT    │ /exercises/{id}         │ Alterar exercício (gera histórico) │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ DELETE │ /exercises/{id}         │ Remover exercício                  │
  ├────────┼─────────────────────────┼────────────────────────────────────┤
  │ GET    │ /exercises/{id}/history │ Histórico de alterações            │
  └────────┴─────────────────────────┴────────────────────────────────────┘
  
  POST /splits
  
  Request:
  
  {
    "name": "Treino A/B/C",
    "description": "Divisão push/pull/legs"
  }
  
  Response 201:
  
  {
    "id": "uuid",
    "name": "Treino A/B/C",
    "description": "Divisão push/pull/legs",
    "active": true,
    "created_at": "2026-06-07T12:00:00Z"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /splits/{id}/days
  
  Request:
  
  {
    "day_of_week": "MONDAY",
    "muscle_group": "Peito e Tríceps",
    "order_index": 1
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /days/{id}/exercises
  
  Request:
  
  {
    "name": "Supino Reto",
    "sets": 4,
    "reps": "8-12",
    "rest_seconds": 90,
    "notes": "Foco na fase excêntrica",
    "order_index": 1
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  PUT /exercises/{id}
  
  Request:
  
  {
    "name": "Supino Inclinado",
    "sets": 4,
    "reps": "10-12",
    "rest_seconds": 60,
    "notes": "Substituído por lesão no ombro",
    "change_reason": "Lesão no ombro direito"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  GET /splits/{id}
  
  Response 200:
  
  {
    "id": "uuid",
    "name": "Treino A/B/C",
    "description": "string",
    "active": true,
    "days": [
      {
        "id": "uuid",
        "day_of_week": "MONDAY",
        "muscle_group": "Peito e Tríceps",
        "order_index": 1,
        "exercises": [
          {
            "id": "uuid",
            "name": "Supino Reto",
            "sets": 4,
            "reps": "8-12",
            "rest_seconds": 90,
            "notes": "string",
            "order_index": 1
          }
        ]
      }
    ]
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  GET /exercises/{id}/history
  
  Response 200:
  
  [
    {
      "id": "uuid",
      "previous_name": "Supino Reto",
      "previous_sets": 4,
      "previous_reps": "8-12",
      "changed_at": "2026-06-01T10:00:00Z",
      "change_reason": "Lesão no ombro direito"
    }
  ]
  
  Nutrição
  
  ┌────────┬──────────────────────────────┬───────────────────────────────┐
  │ Método │ Endpoint                     │ Descrição                     │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ POST   │ /nutrition/meals             │ Registrar refeição            │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ GET    │ /nutrition/daily-log         │ Log do dia (?date=2026-06-07) │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ GET    │ /nutrition/daily-log/history │ Histórico (?from=&to=)        │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ DELETE │ /nutrition/meals/{id}        │ Remover refeição              │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ PATCH  │ /nutrition/water             │ Adicionar água                │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ POST   │ /nutrition/targets           │ Definir meta nutricional      │
  ├────────┼──────────────────────────────┼───────────────────────────────┤
  │ GET    │ /nutrition/targets/current   │ Meta vigente                  │
  └────────┴──────────────────────────────┴───────────────────────────────┘
  
  POST /nutrition/meals
  
  Request:
  
  {
    "meal_type": "LUNCH",
    "description": "Arroz, frango grelhado, brócolis",
    "calories": 650.0,
    "protein_g": 45.0,
    "carbs_g": 70.0,
    "fat_g": 15.0
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  PATCH /nutrition/water
  
  Request:
  
  {
    "amount_ml": 300
  }
  
  Response 200:
  
  {
    "water_ml": 2100
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  GET /nutrition/daily-log?date=2026-06-07
  
  Response 200:
  
  {
    "id": "uuid",
    "log_date": "2026-06-07",
    "total_calories": 1850.0,
    "total_protein_g": 130.0,
    "total_carbs_g": 200.0,
    "total_fat_g": 50.0,
    "water_ml": 2100,
    "meals": [
      {
        "id": "uuid",
        "meal_type": "LUNCH",
        "description": "Arroz, frango grelhado, brócolis",
        "calories": 650.0,
        "protein_g": 45.0,
        "carbs_g": 70.0,
        "fat_g": 15.0,
        "eaten_at": "2026-06-07T12:30:00Z"
      }
    ],
    "target": {
      "daily_calories": 2500.0,
      "daily_protein_g": 180.0,
      "daily_carbs_g": 280.0,
      "daily_fat_g": 70.0,
      "daily_water_ml": 3000
    }
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /nutrition/targets
  
  Request:
  
  {
    "daily_calories": 2500.0,
    "daily_protein_g": 180.0,
    "daily_carbs_g": 280.0,
    "daily_fat_g": 70.0,
    "daily_water_ml": 3000,
    "valid_from": "2026-06-01",
    "valid_until": "2026-06-30"
  }
  
  Metas
  
  ┌────────┬──────────────────────┬───────────────────────────────┐
  │ Método │ Endpoint             │ Descrição                     │
  ├────────┼──────────────────────┼───────────────────────────────┤
  │ POST   │ /goals               │ Criar meta                    │
  ├────────┼──────────────────────┼───────────────────────────────┤
  │ GET    │ /goals               │ Listar metas (?status=ACTIVE) │
  ├────────┼──────────────────────┼───────────────────────────────┤
  │ GET    │ /goals/{id}          │ Detalhe com progresso         │
  ├────────┼──────────────────────┼───────────────────────────────┤
  │ PUT    │ /goals/{id}          │ Atualizar meta                │
  ├────────┼──────────────────────┼───────────────────────────────┤
  │ DELETE │ /goals/{id}          │ Cancelar meta                 │
  ├────────┼──────────────────────┼───────────────────────────────┤
  │ POST   │ /goals/{id}/progress │ Registrar progresso           │
  └────────┴──────────────────────┴───────────────────────────────┘
  
  POST /goals
  
  Request:
  
  {
    "title": "Perder 3kg",
    "description": "Reduzir gordura mantendo massa magra",
    "goal_type": "WEIGHT_LOSS",
    "target_value": 3.0,
    "unit": "kg",
    "start_date": "2026-06-01",
    "end_date": "2026-06-30"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  POST /goals/{id}/progress
  
  Request:
  
  {
    "recorded_value": 1.2,
    "recorded_at": "2026-06-07",
    "notes": "Pesagem matinal em jejum"
  }
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  GET /goals/{id}
  
  Response 200:
  
  {
    "id": "uuid",
    "title": "Perder 3kg",
    "description": "string",
    "goal_type": "WEIGHT_LOSS",
    "target_value": 3.0,
    "current_value": 1.2,
    "unit": "kg",
    "start_date": "2026-06-01",
    "end_date": "2026-06-30",
    "status": "ACTIVE",
    "percentage": 40.0,
    "progress": [
      {
        "id": "uuid",
        "recorded_value": 1.2,
        "recorded_at": "2026-06-07",
        "notes": "Pesagem matinal em jejum"
      }
    ]
  }
  
  Enums do fit-service
  
  - meal_type: BREAKFAST, MORNING_SNACK, LUNCH, AFTERNOON_SNACK, DINNER, SUPPER
  - goal_type: WEIGHT_LOSS, WEIGHT_GAIN, WATER_INTAKE, TRAINING_FREQUENCY, CALORIE_TARGET
  - goal_status: ACTIVE, COMPLETED, FAILED, CANCELLED
  - day_of_week: MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  3. calendar-service
  
  Base URL: /api/calendar
  
  ┌────────┬──────────────┬─────────────────────┐
  │ Método │ Endpoint     │ Descrição           │
  ├────────┼──────────────┼─────────────────────┤
  │ POST   │ /events      │ Criar evento        │
  ├────────┼──────────────┼─────────────────────┤
  │ GET    │ /events      │ Listar (?from=&to=) │
  ├────────┼──────────────┼─────────────────────┤
  │ GET    │ /events/{id} │ Detalhe             │
  ├────────┼──────────────┼─────────────────────┤
  │ PUT    │ /events/{id} │ Atualizar           │
  ├────────┼──────────────┼─────────────────────┤
  │ DELETE │ /events/{id} │ Remover             │
  └────────┴──────────────┴─────────────────────┘
  
  POST /events
  
  Request:
  
  {
    "title": "Exame de sangue",
    "description": "Hemograma completo + glicemia",
    "event_type": "EXAM",
    "start_date": "2026-06-15T08:00:00Z",
    "end_date": "2026-06-15T09:00:00Z",
    "all_day": false,
    "recurrence": {
      "type": "NONE"
    },
    "metadata": {
      "lab_name": "Laboratório São Paulo",
      "fasting_required": true
    }
  }
  
  Response 201:
  
  {
    "id": "string",
    "title": "Exame de sangue",
    "description": "Hemograma completo + glicemia",
    "event_type": "EXAM",
    "start_date": "2026-06-15T08:00:00Z",
    "end_date": "2026-06-15T09:00:00Z",
    "all_day": false,
    "recurrence": {
      "type": "NONE",
      "interval": null,
      "until": null
    },
    "metadata": {
      "lab_name": "Laboratório São Paulo",
      "fasting_required": true
    },
    "created_at": "2026-06-07T12:00:00Z"
  }
  
  Enums
  
  - event_type: EXAM, APPOINTMENT, REMINDER, CUSTOM
  - recurrence.type: NONE, DAILY, WEEKLY, MONTHLY
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  4. shopping-list-service
  
  Base URL: /api/shopping-lists
  
  ┌────────┬───────────────────────────┬──────────────────────────────┐
  │ Método │ Endpoint                  │ Descrição                    │
  ├────────┼───────────────────────────┼──────────────────────────────┤
  │ POST   │ /                         │ Criar lista                  │
  ├────────┼───────────────────────────┼──────────────────────────────┤
  │ GET    │ /                         │ Listar (?period_type=WEEKLY) │
  ├────────┼───────────────────────────┼──────────────────────────────┤
  │ GET    │ /{id}                     │ Detalhe                      │
  ├────────┼───────────────────────────┼──────────────────────────────┤
  │ PUT    │ /{id}                     │ Atualizar                    │
  ├────────┼───────────────────────────┼──────────────────────────────┤
  │ DELETE │ /{id}                     │ Remover                      │
  ├────────┼───────────────────────────┼──────────────────────────────┤
  │ PATCH  │ /{id}/items/{index}/check │ Marcar/desmarcar item        │
  └────────┴───────────────────────────┴──────────────────────────────┘
  
  POST /
  
  Request:
  
  {
    "title": "Compras semana 1 - Junho",
    "period_type": "WEEKLY",
    "period_start": "2026-06-01",
    "period_end": "2026-06-07",
    "items": [
      {
        "name": "Peito de frango",
        "quantity": 2,
        "unit": "kg",
        "category": "Proteínas"
      },
      {
        "name": "Arroz integral",
        "quantity": 1,
        "unit": "kg",
        "category": "Carboidratos"
      }
    ]
  }
  
  Response 201:
  
  {
    "id": "string",
    "title": "Compras semana 1 - Junho",
    "period_type": "WEEKLY",
    "period_start": "2026-06-01",
    "period_end": "2026-06-07",
    "items": [
      {
        "name": "Peito de frango",
        "quantity": 2,
        "unit": "kg",
        "category": "Proteínas",
        "checked": false
      }
    ],
    "created_at": "2026-06-07T12:00:00Z"
  }
  
  PATCH /{id}/items/{index}/check
  
  Request:
  
  {
    "checked": true
  }
  
  Enums
  
  - period_type: WEEKLY, MONTHLY
  
  ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  
  5. notification-service
  
  Base URL: /api/notifications
  
  ┌────────┬──────────────┬─────────────────────────────────────────┐
  │ Método │ Endpoint     │ Descrição                               │
  ├────────┼──────────────┼─────────────────────────────────────────┤
  │ GET    │ /            │ Listar (?status=PENDING&page=0&size=20) │
  ├────────┼──────────────┼─────────────────────────────────────────┤
  │ PATCH  │ /{id}/read   │ Marcar como lida                        │
  ├────────┼──────────────┼─────────────────────────────────────────┤
  │ GET    │ /preferences │ Preferências de notificação             │
  ├────────┼──────────────┼─────────────────────────────────────────┤
  │ PUT    │ /preferences │ Atualizar preferências                  │
  └────────┴──────────────┴─────────────────────────────────────────┘
  
  GET /
  
  Response 200:
  
  {
    "content": [
      {
        "id": "string",
        "title": "Sua meta vence em 3 dias",
        "body": "A meta 'Perder 3kg' termina em 10/06.",
        "channel": "IN_APP",
        "status": "PENDING",
        "source_service": "fit-service",
        "source_event": "goal.deadline.approaching",
        "created_at": "2026-06-07T12:00:00Z"
      }
    ],
    "page": 0,
    "size": 20,
    "total_elements": 5,
    "total_pages": 1
  }
  
  PUT /preferences
  
  Request:
  
  {
    "email_enabled": true,
    "push_enabled": false,
    "quiet_hours_start": "22:00",
    "quiet_hours_end": "07:00"
  }
  
  Response 200:
  
  {
    "user_id": "uuid",
    "email_enabled": true,
    "push_enabled": false,
    "quiet_hours_start": "22:00",
    "quiet_hours_end": "07:00"
  }
  
  Enums
  
  - channel: EMAIL, PUSH, IN_APP
  - status: PENDING, SENT, FAILED, READ
  
  
  ---
