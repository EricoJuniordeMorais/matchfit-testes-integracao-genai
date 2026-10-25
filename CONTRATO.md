# Contrato didático e dados sintéticos

Este contrato é uma proposta para o exercício. Não foi confirmado como comportamento da API de produção.

## Dados iniciais, recriados antes de cada caso

- Professor P1: id 101; professor P2: id 102.
- Alunos A1 e A2: ids 201 e 202, vinculados a P1.
- Aluno A3: id 203, vinculado a P2.
- Treino T1: id 301, pertence a P1; treino T2: id 302, pertence a P2.
- Treino 999 e aluno 999 não existem.
- Banco sem vínculos e sem sessões inicialmente.
- `TOKEN_P1`, `TOKEN_A1`, `TOKEN_A2`: marcadores para credenciais de teste emitidas no ambiente isolado. Não são tokens utilizáveis.
- Relógio do ambiente fixado em 2030-01-01T00:00:00Z para evitar dependência da data real.

## Vincular treino

`POST /workouts/{workoutId}/assign-students`, com `Authorization: Bearer TOKEN_P1`.

```json
{
  "userIds": [201, 202],
  "recurrence": "ONE_TIME",
  "schedules": [{"execAt": "2030-06-10T12:00:00Z"}]
}
```

Regras propostas:

- Apenas professor pode vincular um treino; só pode usar seus próprios treinos e alunos.
- `userIds` contém de 1 a 50 inteiros únicos; todos os alunos devem existir.
- O exercício aceita somente `ONE_TIME` e exatamente um horário ISO 8601 válido, com fuso explícito e no futuro.
- Sucesso: HTTP 201, JSON com `success: true`, `count` igual à quantidade de alunos e `assignments`, um objeto `{id, userId, workoutId}` por aluno. IDs gerados são inteiros positivos.
- Cada aluno recebe um vínculo e uma sessão persistidos na mesma transação.
- Credencial ausente/inválida: 401; papel ou propriedade sem permissão: 403; treino ou aluno inexistente: 404; entrada inválida: 400.
- Repetir treino/aluno/horário já vinculado: 409, sem duplicar registros.
- Falha no banco: 500 e rollback integral, inclusive quando a falha ocorre após a primeira gravação.
- Todas as respostas de erro contêm `error.code` e `error.message` como strings não vazias; sem SQL, stack trace ou credenciais. O exercício não fixa o texto da mensagem.
- Ordem de validação: autenticação, papel, estrutura da entrada, existência e propriedade, duplicidade, persistência.
- Qualquer erro deixa o estado persistido igual ao estado anterior à requisição.

## Consultar agenda

`GET /student-workout-sessions?startDate=2030-06-10&endDate=2030-06-10`, com token do aluno.

HTTP 200 com `sessions`, array de objetos `{id, userId, workoutId, execAt, status}`. `status` inicial é `SCHEDULED`. A consulta retorna somente sessões do aluno autenticado, no intervalo inclusivo de datas UTC. Uma consulta autenticada não altera dados. IDs são inteiros positivos; `execAt` preserva o instante agendado.
