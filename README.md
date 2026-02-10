# AML-FT Case

## Contexto

A lavagem de dinheiro constitui um dos principais riscos sistêmicos enfrentados pelo setor financeiro, com impactos diretos sobre a integridade do sistema econômico, a reputação das instituições e o cumprimento das exigências regulatórias. O crescimento dos meios de pagamento digitais, aliado ao alto volume e à velocidade das transações, amplia a complexidade da detecção de atividades ilícitas e exige mecanismos de prevenção cada vez mais robustos e baseados em dados. Nesse contexto, a Prevenção à Lavagem de Dinheiro (AML) desempenha um papel central ao permitir a identificação, o monitoramento e a priorização de operações suspeitas, assegurando não apenas a conformidade regulatória, mas também a eficiência operacional das equipes de investigação frente a recursos limitados e cenários de risco cada vez mais dinâmicos

## 1. Objetivo

Realizar uma triagem AML baseada em dados para identificar padrões de risco e priorizar casos para investigação.

[Clique aqui para conferir a análise completa](https://github.com/gabriel-cds/aml_case/blob/main/analise.ipynb)

## 2. Base de Dados

A base principal `transactions` é composta por dados sintéticos de operações financeiras, incluindo informações de pagamento, geolocalização, dispositivos, sinais de risco, entre outras. Os dados abrangem o período de 3 meses aproximadamente (01/07/2025 até 04/10/2025).

Além dessa base, existem três dimensões:
- `KYC_Profiles` contém os perfis cadastrais e regulatórios de clientes (PEP, sanções, classificação de risco).
- `Merchants` contém os cadastros de estabelecimentos comerciais com métricas de risco e vínculos proprietários.
- `GeoBehavior` contém padrões comportamentais agregados por remetente.
 
[Clique aqui para conferir o dicionário de dados e análise de qualidade das bases](https://github.com/gabriel-cds/aml_case/blob/main/reconhecimento.ipynb)

## 3. Metodologia Aplicada
- Enriquecimento dos dados
- Análise de anomalias e tipologias
- Definição de alertas
- Machine Learning

## 4. Análise de Anomalias e Tipologias AML

Nesta etapa, foi realizada uma analise exploratória com o objetivo de identificar anomalias nas transações por meio de desvios do comportamento padrão e de tipologias clássicas de AML (Self Merchant, PEP de alto risco). Como resultado, foram identificados e exemplificados comportamentos anômalos na base de dados e com isso foram criadas as regras para o sistema de alertas.

Tradução de anomalia/tipologia - lógica objetiva

| # | Métrica | Definição Técnica | Threshold (Limiar) | Exemplo na base| Justificativa de Risco (Red Flag) |
|---|---------|-------------------|-------------------|---------|-----------------------------------|
| 2 | Income Incompatible Tx | Razão entre o valor da transação e a renda mensal declarada acima do padrão esperado. | amount_brl / monthly_income > 9.52 | TSFBYSDK4OHYP / C100054 | Incompatibilidade patrimonial severa. Transações que excedem múltiplas vezes a capacidade financeira do titular sugerem uso de conta-laranja, credenciais roubadas, ou fraude de identidade sintética. Padrão comum em contas recém-abertas utilizadas para lavagem de dinheiro. |


## 5. Sistema de Alertas

- Consolidação de todas as regras definidas a partir do tópico anterior e criação de features, cobrindo diversos fatores como comportamento, geografia e valor.
- Criação de features determinísticas `transactions_with_features`, com flags booleanas (f_is_*) que representam o disparo de cada regra de risco por transação.
- Sistema de alertas operacional com peso baseado em severidade.

## 6. Casos Suspeitos + SAR

Definição de casos suspeitos a partir do sistema de alertas, utilizando como parâmetros o weighted_risk_score, severidade máxima, quantidade de alertas simultâneos e materialidade financeira. 

**Exemplo**

**Caso 1 — Cliente C100472** 
**Transaction ID:** T5L6TABCH2AYE  
**Valor:** R$ 42.420,78  
**Score de Risco:** 18 (máximo da amostra)  
**Severidade Máxima:** 5  
**Qtd. de Alertas:** 6  

**Principais Red Flags:**
- Uso de IP anonimizado (VPN).
- MCC de alto risco no recebedor (Atividades de jogos de azar).
- Renda declarada incompatível com o valor transacionado (income utilization ≈ 22x).
- Transação realizada em horário atípico (05h).
- Sanctions hit no sender, elevando significativamente o risco regulatório.

## 7. Machine Learning

Modelo desenvolvido com foco exclusivo em priorização de risco, e não em classificação de fraude.

- Devido a ausência de rótulos, o problema foi tratado como um cenário de aprendizado não supervisionado (detecção de anomalias).
- Isolation Forest: É um modelo não supervisionado, adequado à ausência de rótulos confiáveis; é robusto a distribuições assimétricas e outliers; transações anômalas são isoladas com menos divisões na árvore.
- Os alertas determinísticos da etapa anterior foram utilizados como rótulos fracos para validar se o modelo concentra maiores scores em transações já reconhecidas como sensíveis. A validação foi conduzida de maneira operacional, avaliando a concentração de alertas determinísticos nos percentis superiores.

Limitações: A ausência de rótulos supervisionados faz com que o score represente apenas priorização de risco, e não probabilidade de ilícito. Além disso, por se tratar de detecção de anomalias, o modelo é sensível a mudanças de comportamento (drift), exigindo monitoramento e recalibragem contínuos.

## 8. Agente de AML

Como complemento à metodologia aplicada, foi desenvolvido um agente de IA com foco em automação, padronização e apoio à decisão:

- Agent 1: Ingestão e qualidade dos dados
- Agent 2: Feature Engineering
- Agent 3: ML Scoring
- Agent 4: SAR Report

## Conclusão e Próximos Passos

O projeto apresenta um framework AML-FT ponta a ponta, integrando dados, regras determinísticas, Machine Learning, inteligência artificial e investigação humana de forma explicável e alinhada à realidade de fintechs de pagamentos. 

As etapas são complementares e interdependentes: tipologias fundamentam regras auditáveis, alertas estruturam sinais operacionais, o Machine Learning refina a priorização combinando múltiplos fatores, e o SAR consolida a decisão em narrativa regulatória consistente.

Como próximos passos, destacam-se a incorporação de feedback humano, o monitoramento de drift e recalibragem de thresholds, e a evolução do uso de IA para ampliar escala, consistência e eficiência do processo de AML.

## Contato
<div> 
   <a href="https://www.linkedin.com/in/gabriel-candido-dos-santos-3793a6163" target="_blank"><img src="https://img.shields.io/badge/-LinkedIn-%230077B5?style=for-the-badge&logo=linkedin&logoColor=white" target="_blank"></a> 
   <a href = "mailto:gabrielcandidosts@gmail.com"><img src="https://img.shields.io/badge/-Gmail-%23333?style=for-the-badge&logo=gmail&logoColor=white" target="_blank"></a>
</div>

