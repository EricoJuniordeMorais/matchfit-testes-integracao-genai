Olá, pessoal!

Para esta atividade, usei um cenário didático inspirado no Matchfit: a integração entre uma API REST de treinos e o banco de dados. O fluxo envolve o professor vincular um treino a alunos, a persistência dos vínculos e sessões e a consulta da agenda pelo aluno.

Repositório público: https://github.com/EricoJuniordeMorais/matchfit-testes-integracao-genai

Experimentei dois tipos de prompt com o Codex:

1. **Geração de cenários funcionais:** solicitei casos para vinculação individual e em lote, consulta da agenda e isolamento entre alunos. Esse prompt ajudou a organizar o fluxo entre componentes e a verificar tanto a resposta HTTP quanto os dados persistidos.
2. **Geração de casos negativos e dados de fronteira:** solicitei situações como falta de autenticação, acesso sem permissão, IDs inexistentes, listas fora dos limites, horário inválido, duplicidade e falha de persistência. Esse segundo tipo ampliou a diversidade dos casos, especialmente quanto à integridade dos dados e ao rollback da transação.

Os prompts iniciais produziram sugestões genéricas. Para obter resultados mais úteis, acrescentei um contrato didático com endpoints, regras, códigos HTTP, dados sintéticos e limites de entrada. Também pedi que cada caso apresentasse pré-condições, requisição, resultado esperado e efeito no banco. Um ajuste importante foi exigir que falhas em lote não deixassem gravações parciais.

Registrei no repositório os prompts, as respostas iniciais, os refinamentos e 16 casos de teste gerados. Os casos estão documentados e ainda não foram executados contra a API real. A experiência mostrou como a GenAI pode apoiar a elaboração dos testes, enquanto a revisão humana continua necessária para conferir as regras e validar os resultados em execução.
