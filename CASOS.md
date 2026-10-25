# Casos de teste de integração gerados pela LLM

**Status de todos os casos: NÃO EXECUTADO.** A tabela registra resultados esperados, não observados.

CT01–CT06 derivam do prompt funcional refinado; CT07–CT16, do prompt negativo refinado. O contrato completo está em [CONTRATO.md](CONTRATO.md).

Convenções: POST usa `/workouts/{id}/assign-students`, `TOKEN_P1`, `recurrence=ONE_TIME` e `schedules=[{"execAt":"2030-06-10T12:00:00Z"}]`, exceto quando indicado. GET usa a consulta da agenda e o token do aluno indicado. T1=301; T2=302. Cada caso é independente e restaura os dados iniciais. “Contrato de erro” significa JSON com `error.code` e `error.message` não vazios, sem SQL, stack trace ou credenciais. Cada sucesso POST também valida `success=true` e os tipos de campos definidos no contrato.

| ID | Objetivo | Pré-condições | Requisição / ação | Resultado HTTP esperado | Estado esperado no banco |
| --- | --- | --- | --- | --- | --- |
| CT01 | Funcional: fluxo completo | Dados iniciais. | POST T1 para [201], horário padrão; depois GET da agenda de A1. | POST 201, success=true, count=1; GET 200, uma sessão de A1/T1 no horário padrão, SCHEDULED. | 1 vínculo e 1 sessão; IDs da resposta correspondem aos registros. |
| CT02 | Funcional: vários alunos | Dados iniciais. | POST T1 para [201,202]; GET das agendas de A1 e A2. | 201, count=2, dois assignments; cada GET 200 retorna uma sessão para seu aluno. | 2 vínculos e 2 sessões, sem cruzamento de alunos. |
| CT03 | Funcional: agenda vazia | Dados iniciais, sem POST anterior. | GET da agenda de A1. | 200, sessions=[]. | 0 vínculos e 0 sessões; consulta não grava. |
| CT04 | Funcional: isolamento | Vincular T1 somente a A1 com sucesso. | GET da agenda autenticado como A2. | 200, sessions=[]; nenhum dado de A1. | Continua com 1 vínculo e 1 sessão de A1. |
| CT05 | Fronteira: mínimo permitido | Dados iniciais. | POST T1 com userIds=[201]. | 201; count=1; assignments é array de tamanho 1, id inteiro positivo, userId=201 e workoutId=301. | Exatamente 1 vínculo e 1 sessão, associados ao mesmo aluno/treino. |
| CT06 | Fronteira: máximo permitido | Cadastrar 50 alunos sintéticos ids 1001 a 1050 vinculados a P1. | POST T1 com esses 50 IDs e horário padrão. | 201; count=50 e 50 assignments com IDs distintos e os alunos solicitados. | 50 vínculos e 50 sessões, uma por aluno; nenhum aluno omitido. |
| CT07 | Erro: autenticação | Dados iniciais. | POST válido para A1, omitindo Authorization. | 401 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT08 | Erro: papel sem permissão | Dados iniciais. | POST válido para A1 usando TOKEN_A1. | 403 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT09 | Erro: treino de outro professor | Dados iniciais. | POST T2 para [201] usando TOKEN_P1. | 403 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT10 | Erro: treino inexistente | Dados iniciais. | POST treino 999 para [201]. | 404 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT11 | Erro: lote com aluno inexistente | Dados iniciais. | POST T1 para [201,999]. | 404 e contrato de erro; a requisição inteira falha. | 0 vínculos e 0 sessões, inclusive para 201. |
| CT12 | Fronteira: abaixo do mínimo | Dados iniciais. | POST T1 com userIds=[]. | 400 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT13 | Fronteira: acima do máximo | Cadastrar 51 alunos sintéticos ids 1001 a 1051 vinculados a P1. | POST T1 com esses 51 IDs. | 400 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT14 | Erro: horário inválido | Dados iniciais. | POST T1 para [201] com execAt="data-invalida". | 400 e contrato de erro. | 0 vínculos e 0 sessões. |
| CT15 | Erro: duplicidade | Primeiro POST T1 para [201] concluído com 201. | Repetir exatamente a primeira requisição. | 409 e contrato de erro; GET de A1 mantém uma sessão. | Continua com 1 vínculo e 1 sessão; IDs originais preservados. |
| CT16 | Erro: rollback após gravação | Dados iniciais; configurar no banco de testes uma falha controlada na inserção de sessão, após inserir vínculo na transação. | POST T1 para [201,202]; remover a falha ao finalizar o caso. | 500 e contrato de erro, sem detalhes internos. | Rollback integral: 0 vínculos e 0 sessões. Conferir por outra conexão após o fim da requisição. |
