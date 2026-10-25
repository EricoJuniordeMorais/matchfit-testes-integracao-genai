# Matchfit — testes de integração com GenAI

Atividade da Unidade III: uso de prompts para elaborar casos de teste de integração.

## Cenário

Uma API REST de gestão de treinos recebe a solicitação de um professor para vincular um treino a alunos. A API valida a autenticação e as regras de negócio, grava os vínculos e as sessões no banco, e permite que o aluno consulte sua agenda.

**Integração estudada:** cliente HTTP → autenticação → módulo de treinos → banco de dados → consulta da agenda.

O domínio foi inspirado no Matchfit. Este repositório é um exercício didático independente: o contrato abaixo é proposto para a atividade, não uma especificação oficial nem uma cópia da implementação do produto. Todos os identificadores e tokens são fictícios.

## Material da atividade

- [Contrato e dados sintéticos](CONTRATO.md)
- [Prompts, respostas e refinamentos](PROMPTS.md)
- [16 casos de teste de integração](CASOS.md)
- [Texto para o fórum](FORUM.md)

## Experimento com a LLM

Os prompts foram aplicados ao assistente de IA desta sessão (Codex), durante a elaboração desta atividade. As respostas registradas são saídas textuais geradas nesta interação; não são evidências de conversas anteriores. Foram usados dois tipos: geração de cenários funcionais e geração de casos negativos/dados de fronteira, com refinamento por contrato e formato estruturado.

## Como executar os casos em um ambiente de teste

1. Implementar ou adaptar o contrato didático em uma API isolada e usar um banco exclusivo de testes.
2. Preparar os dados de CONTRATO.md antes de cada caso, limpando vínculos e sessões entre execuções.
3. Enviar as requisições descritas em CASOS.md usando um cliente HTTP.
4. Conferir o status, o corpo da resposta, a consulta da agenda e o estado persistido no banco.
5. Para falhas de persistência, aplicar a falha indicada somente no ambiente de testes.
6. Registrar resultado obtido e evidências de cada execução.

**Estado atual:** 16 casos especificados e revisados; nenhum executado contra a API ou o banco do Matchfit. Não há suíte automatizada neste repositório. A validação realizada nesta entrega verifica a integridade dos documentos, os links relativos e a quantidade/identificação dos casos, não o funcionamento da API.

## Reflexão

O prompt funcional favoreceu sequências entre componentes e conferência da persistência, enquanto o prompt negativo ampliou a diversidade com autorização, entradas inválidas e falhas de banco. O refinamento acrescentou contrato, dados sintéticos, códigos HTTP, pré-condições e efeitos esperados no banco. Isso tornou as respostas verificáveis e reduziu suposições sobre endpoints e regras. A revisão humana continua necessária para adequar a proposta à API real.
