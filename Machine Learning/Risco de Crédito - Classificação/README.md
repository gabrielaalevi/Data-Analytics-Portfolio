## :dart: Descrição do Projeto

Neste projeto, utilizei dados reais do período de 2007 a 2010 do site LendingClub, que conecta investidores e tomadores de crédito. O principal objetivo foi identificar clientes com maior probabilidade de não quitar suas dívidas, usando dados como finalidade do empréstimo, taxa de juros, valor das parcelas, score de crédito, entre outras. Com isto, é possível classificar tais clientes como empréstimos de risco.
O banco de dados contém as seguintes colunas:

| Variável | Tipo | Descrição |
| -------- | ---- | ----------- |
|**Credit Policy** (Política de crédito) | int | Determina se o cliente se adequa às políticas de crédito do site. 1 se o cliente se adequa a tais políticas, 0 se não. |
|**Purpose** (Propósito) | str | Propósito do empréstimo.|
|**Interest Rate** (Taxa de juros) | float | Taxa de juros aplicada ao empréstimo.|
|**Installment** (Parcela)| float | Valor da parcela mensal de pagamento.|
|**Annual Income** (Renda anual)| float | Renda anual do cliente, conforme informado pelo próprio.|
|**DTI** | float | Razão entre o valor da dívida e a renda anual do cliente.|
|**FICO** (Pontos FICO)| int | Número de pontos FICO (score) do cartão de crédito.|
|**Days with CR line** (Dias com linha de crédito) | int | Número de dias desde que o cliente criou sua conta de crédito.|
|**Revol Bal** (Crédito rotativo)| float | Valor do crédito rotativo do cliente (valor que falta pagar após fechamento do ciclo de crédito).|
|**Revol Util** (Crédito utilizado) | float | Razão entre o crédito utilizado e o crédito disponível total. |
|**Inqueries Last 6 Months** (Investigações nos últimos 6 meses) | int | Número de investigações que o cliente sofreu por parte de credores nos últimos 6 meses.|
|**Delinqu. 2 years** (Delinq. nos últimos dois anos) | int | Número de vezes que o cliente atrasou um pagamento por mais de 30 dias nos últimos dois anos.|
|**Public Records** (Registros Públicos) | int | Número de registros públicos relacionados a falência, evasão fiscal, etc que o cliente possui. |
|**Not Fully Paid** (Não Pago Totalmente) | int | Se o cliente pagou sua dívida totalmente, esta variável é 0. Caso contrário, 1. Esta é a variável que gostaríamos de prever.|

Algumas das técnicas que apliquei neste projeto são:

- Train-Test Split para validação adequada dos modelos
- Feature Scaling para padronização dos dados
- SMOTE para tratamento do desbalanceamento das classes
- Matriz de confusão, para determinar o modelo mais apropriado
- Grid Search para otimização de hiperparâmetros

Como próximos passos, o projeto poderia ser aprimorado com a inclusão de novas fontes de dados e técnicas mais avançadas de modelagem. Variáveis relacionadas ao histórico detalhado de pagamentos, tempo de relacionamento com instituições financeiras, utilização do limite de crédito, número de consultas recentes ao histórico de crédito e estabilidade profissional poderiam agregar poder preditivo ao modelo. A realização de validação temporal e análises de estabilidade do modelo ao longo do tempo também contribuiria para garantir maior robustez e aderência a cenários reais de concessão de crédito.

