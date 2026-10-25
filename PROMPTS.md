# Registro do experimento com prompts

LLM utilizada: assistente Codex desta sessão. As respostas abaixo foram geradas para esta atividade e revisadas em relação ao contrato didático. Não houve execução da API nem medição estatística da qualidade.

## Tipo 1 — geração de cenários funcionais

### Prompt inicial aplicado

> Gere cenários de teste de integração para uma API de treinos em que um professor vincula treinos a alunos e o aluno consulta a agenda.

### Resposta inicial gerada

1. Vincular um treino a um aluno e verificar se aparece na agenda.
2. Vincular um treino a vários alunos e consultar suas agendas.
3. Consultar a agenda sem treinos cadastrados.
4. Conferir se um aluno consegue visualizar somente seus próprios treinos.

### Avaliação

A resposta inicial cobre o fluxo principal, mas não define dados, códigos HTTP, formato do retorno nem quantidades persistidas. Sem essas informações, dois executores poderiam interpretar o mesmo caso de formas diferentes.

### Prompt refinado aplicado

> Use integralmente o contrato e os dados sintéticos de CONTRATO.md como contexto. Gere seis casos de integração funcionais para POST /workouts/{workoutId}/assign-students e GET /student-workout-sessions. Inclua um aluno, múltiplos alunos, agenda vazia, isolamento entre alunos e os limites de 1 e 50 alunos. Para 50 alunos, explicite os dados adicionais necessários. Para cada caso forneça ID, pré-condições, requisição/ação, status e corpo esperado, e efeito no banco. Use HTTP e banco reais em ambiente isolado; não substitua o módulo de persistência por mock nos fluxos normais. Não invente recursos fora do contrato. Identifique o que ainda depende de execução.

### Resposta refinada gerada

Casos **CT01 a CT06**, registrados integralmente em [CASOS.md](CASOS.md). A saída passou a incluir códigos, contagens de registros e consulta posterior, permitindo avaliar a comunicação entre componentes. CT01 e CT05 compartilham o mesmo tamanho de entrada, mas têm objetivos distintos: fluxo completo e checagem explícita do contrato no limite mínimo.

## Tipo 2 — casos negativos e dados de fronteira

### Prompt inicial aplicado

> Gere testes de erro para a integração da API de treinos com o banco de dados.

### Resposta inicial gerada

1. Enviar uma requisição sem autenticação.
2. Enviar dados de aluno inválidos.
3. Tentar vincular um treino inexistente.
4. Simular indisponibilidade do banco de dados.

### Avaliação

A saída identificou riscos úteis, mas “dados inválidos” e “indisponibilidade” eram vagos. Faltaram a distinção entre autenticação e autorização, entradas de fronteira e a verificação de gravações parciais.

### Prompt refinado aplicado

> Use integralmente CONTRATO.md. Gere dez casos negativos de integração: token ausente; aluno tentando vincular treino; treino de outro professor; treino inexistente; aluno inexistente em lote misto; lista vazia; 51 alunos; horário inválido; repetição de uma vinculação; falha de banco após a primeira gravação. Use IDs CT07 a CT16. Em cada caso, declare estado inicial, requisição, código HTTP, contrato de erro e estado final do banco. No lote misto e na falha de banco, exija ausência de persistência parcial. Para a repetição, preserve o primeiro vínculo. Não sugira que os casos foram executados e não invente resultados observados.

### Resposta refinada gerada

Casos **CT07 a CT16**, registrados integralmente em [CASOS.md](CASOS.md). O refinamento gerou verificações de autorização, fronteiras, duplicidade e atomicidade; as falhas passaram a ter condições reproduzíveis e efeitos esperados explícitos.

## Comparação e limites

- O prompt funcional priorizou o fluxo ponta a ponta entre HTTP, regras e persistência, incluindo a leitura posterior pela agenda.
- O prompt negativo acrescentou diversidade de entradas e falhas entre componentes que não apareceram no primeiro grupo.
- O contrato explícito reduziu ambiguidades; a estrutura por caso facilitou revisão e reprodução.
- A avaliação é qualitativa sobre o texto gerado. A utilidade prática e a detecção de defeitos dependem de implementação e execução em ambiente de testes.
- Próximas extensões possíveis: concorrência, token expirado e limites de data. Elas não estão cobertas pelos 16 casos desta entrega.
