# Spec — API de Tarefas

## Visão geral

Este documento especifica o comportamento esperado da API de gerenciamento de tarefas.
O serviço deve expor endpoints REST para criar, listar, atualizar e remover tarefas.

> [!NOTE]
> Todas as respostas da API devem usar o formato JSON, com `Content-Type: application/json`.

## UC1 — Criar tarefa

**Endpoint:** `POST /tarefas`

**Request body:**

| Campo       | Tipo    | Obrigatório | Descrição                        |
|-------------|---------|-------------|-----------------------------------|
| titulo      | string  | sim         | Título da tarefa (1–120 chars)   |
| descricao   | string  | não         | Detalhes da tarefa                |
| prioridade  | enum    | sim         | `baixa`, `media` ou `alta`       |

**Resposta esperada (201 Created):**

```json
{
  "id": "uuid",
  "titulo": "Comprar leite",
  "status": "pendente"
}
```

> [!WARNING]
> Se `titulo` vier vazio ou só com espaços, a API deve retornar `400 Bad Request` — não deve aceitar e salvar uma tarefa sem título válido[^1].

## Casos de borda

| Cenário                                  | Resultado esperado           |
|-------------------------------------------|-------------------------------|
| `titulo` com 121 caracteres               | `400 Bad Request`             |
| `prioridade` fora do enum                 | `400 Bad Request`             |
| Requisição sem `Content-Type` correto     | `415 Unsupported Media Type`  |
| Campo `descricao` ausente                 | Aceita normalmente (é opcional) |

## Regras de negócio

- Toda tarefa criada começa com `status = "pendente"`.
- Tarefas não podem ser editadas após marcadas como `concluida`[^2].

[^1]: Essa validação evita que o banco acumule registros "vazios", o que quebraria relatórios
