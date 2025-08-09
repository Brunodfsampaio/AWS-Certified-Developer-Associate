# Laboratório - Arquitetura Fan-Out com SNS, SQS e Lambda
### Resumo
Observação: A interface do Console de Gerenciamento da AWS pode sofrer
pequenas alterações visuais ao longo do tempo, mas os conceitos e a localização
geral dos serviços permanecem consistentes. As instruções neste resumo
seguem a estrutura geral das funcionalidades.
Este laboratório ensina como implementar uma arquitetura fan-out avançada na
AWS. Você aprenderá a:
1. Usar o Amazon SNS para publicar uma mensagem que será distribuída
para múltiplos assinantes.
2. Configurar filtros de assinatura SNS para que diferentes Lambdas
processem apenas mensagens relevantes.
3. Integrar o SNS com Amazon SQS para desacoplar o processamento e
aumentar a resiliência, com uma das "ramificações" do fan-out.
4. Criar múltiplas AWS Lambda functions que serão acionadas pelos
eventos do SNS (diretamente ou via SQS), cada uma realizando uma tarefa
distinta.
5. Configurar Dead-Letter Queues (DLQs) para tratamento de erros em uma
das filas SQS.
6. Monitorar o fluxo usando o Amazon CloudWatch Logs.
### Cenário:
Imagine um sistema de processamento de pedidos de um e-commerce. Quando
um novo pedido é recebido (simulado pela publicação de uma mensagem no
SNS), várias ações precisam ocorrer em paralelo:
1. Atualizar Inventário: Uma função Lambda (ou serviço) precisa verificar e
atualizar o estoque dos produtos.
2. Processar Pagamento: Outra função Lambda simula o processamento do
pagamento.
3. Notificar o Cliente: Uma terceira função Lambda envia uma notificação
por e-mail (simulada) para o cliente.
4. Análise de Fraude (Via SQS): Uma mensagem é enviada para uma fila SQS
para uma análise de fraude mais demorada, processada por outra Lambda.
Usaremos o SNS para o fan-out inicial. Algumas Lambdas serão inscritas
diretamente no tópico SNS, outras indiretamente via SQS. Usaremos atributos de
mensagem e filtros de assinatura SNS para direcionar eventos específicos.
Pré-requisitos:
- Conta AWS Ativa: Com permissões para criar e gerenciar SNS, SQS,
Lambda e IAM.
- Permissões IAM:
- Para simplificar: AWSLambda_FullAccess, AmazonSNSFullAccess,
AmazonSQSFullAccess, IAMFullAccess (para criar papéis).
- Recomendado (para produção): Criar papéis IAM específicos para
cada Lambda com as permissões mínimas necessárias (ex:
permissão para publicar no CloudWatch Logs, ler da SQS, etc.).
- Navegador Web.
- Conhecimento Básico: Familiaridade com SNS, SQS e Lambda.

# Passo 1
Para este laboratório, assumiremos o uso da VPC padrão e suas sub-redes
públicas para simplificar. As Lambdas, por padrão, rodam dentro da VPC
gerenciada pela AWS.
Vamos criar duas filas: uma para a análise de fraude e uma Dead-Letter Queue
(DLQ) para ela.
1. Console SQS: Acesse o console do SQS.
2. Criar DLQ para Análise de Fraude:
- Clique em Create queue.
- Type: Standard.
- Name: fila-fraude-analise-dlq-seunome
- Deixe as demais configurações padrão.
- Clique em Create queue. Anote o ARN desta DLQ.

<img width="1218" height="483" alt="image" src="https://github.com/user-attachments/assets/06303cb0-a01a-45dc-b498-d484407036c3" />
<img width="1215" height="800" alt="image" src="https://github.com/user-attachments/assets/8402fec1-8300-4d74-843a-4f241d177ac3" />

3. Criar Fila Principal para Análise de Fraude:
- Clique em Create queue.
-  Type: Standard.
- Name: fila-fraude-analise-seunome
- Desça até Dead-letter queue:
- Marque Enabled.
- Amazon Resource Name (ARN): Selecione o ARN da DLQ
(fila-fraude-analise-dlq-seunome) que você criou.
- Maximum receives: 3 (Após 3 falhas de processamento, a
mensagem irá para a DLQ).
- Clique em Create queue. Anote o ARN desta fila.

<img width="1240" height="777" alt="image" src="https://github.com/user-attachments/assets/c6fe884a-9a69-405c-ab43-2e4d507292d9" />

# Passo 2
Este será o tópico central para disparar o fan-out.
1. Console SNS: Acesse o console do SNS.
2. Criar Tópico:
- Topics -> Create topic.
- Type: Standard.
- Name: topico-pedidos-ecommerce-seunome
- Access policy (Avançado):
- Permitiremos que o SQS (a fila de análise de fraude) e as
Lambdas (que serão criadas) se inscrevam, e também que o
próprio SNS publique mensagens.
- Por enquanto, deixe a política padrão. Vamos ajustá-la
depois de criar a fila e as Lambdas, se necessário, ou garantir
que os serviços tenham permissão para se inscrever. Muitas
vezes, ao criar a assinatura via console, as permissões são
ajustadas automaticamente. Para este laboratório, vamos
focar em criar as assinaturas e depois verificar/ajustar as
políticas do SNS e SQS.
3. Create topic. Anote o ARN do tópico.

# Passo 3
Cada Lambda precisará de um papel IAM com permissões para executar e
escrever logs no CloudWatch. A Lambda que consome da SQS precisará de
permissões SQS.
1. Console IAM: Acesse o console do IAM.
2. Criar Papel para Lambdas Genéricas (Inventário, Pagamento,
Notificação):
- Roles -> Create role.
- Trusted entity type: AWS service.
- Use case: Lambda.
- Next.
- Add permissions: Procure e adicione a política
AWSLambdaBasicExecutionRole (permite upload de logs para
CloudWatch).
- Next.
- Role name: LambdaRoleSNSGeneric-seunome
- Create role.
3. Criar Papel para Lambda de Análise de Fraude (SQS):
- Roles -> Create role.
- Trusted entity type: AWS service.
- Use case: Lambda.
- Next.
- Add permissions:
- Procure e adicione AWSLambdaBasicExecutionRole.
- Procure e adicione AmazonSQSFullAccess (para simplificar;
em produção, use permissões mais restritas como
sqs:ReceiveMessage, sqs:DeleteMessage,
sqs:GetQueueAttributes para a fila específica).
- Next.
- Role name: LambdaRoleSQSFraude-seunome
- Create role.

<img width="972" height="263" alt="image" src="https://github.com/user-attachments/assets/8f7b7d90-6aa9-4d00-a943-f5239fad7e83" />
<img width="977" height="296" alt="image" src="https://github.com/user-attachments/assets/bef52185-87fa-4f4f-9c46-9c94a5e65829" />

# Passo 4
Criaremos quatro Lambdas simples (Python como exemplo).
1. Console Lambda: Acesse o console Lambda.
2. Criar Lambda "Atualizar Inventário":
- Create function.
- Selecione Author from scratch.
- Function name: lambda-inventario-seunome
- Runtime: Python 3.12 (ou a mais recente suportada).
- Architecture: x86_64.
- Expanda o menu Change default execution role.
- Selecione: Use an existing role.
- Selecione: LambdaRoleSNSGeneric-seunome.
- Create function.

<img width="1242" height="720" alt="image" src="https://github.com/user-attachments/assets/bc54803d-784a-464a-8525-c23ea4991a80" />
<img width="1207" height="535" alt="image" src="https://github.com/user-attachments/assets/7df0969c-d96e-4564-9d34-9bd726dfe857" />

- Role para baixo, até a aba com o código da sua função. Nome da aba: “Code”.
- Substitua o código no editor (lambda_function.py) pelo Código
clicando aqui.

```
import json

def lambda_handler(event, context):
    print("Recebido evento para ATUALIZAR INVENTÃRIO:")
    print(json.dumps(event))
    # LÃ³gica para atualizar inventÃ¡rio aqui (simulada)
    message_attributes = event['Records'][0]['Sns']['MessageAttributes']
    order_id = message_attributes.get('OrderID', {}).get('Value', 'N/A')
    print(f"InventÃ¡rio atualizado para o pedido: {order_id}")
    return {
        'statusCode': 200,
        'body': json.dumps(f'InventÃ¡rio atualizado para pedido {order_id}')
    }
```

- Depois clique em Deploy.

<img width="1240" height="800" alt="image" src="https://github.com/user-attachments/assets/0cb73754-3748-4375-b69f-fabbc04d8fd6" />

3. Criar Lambda "Processar Pagamento":
4. No menu lateral esquerdo, clique em Functions.
- Create function.
- Selecione Author from scratch.
- Function name: lambda-pagamento-seunome
- Runtime: Python 3.12 (ou a mais recente suportada).
- Architecture: x86_64.
- Expanda o menu Change default execution role.
- Selecione: Use an existing role.
- Existing role: LambdaRoleSNSGeneric-seunome
- Create function.

<img width="1242" height="785" alt="image" src="https://github.com/user-attachments/assets/fd5b23ea-0782-4558-baae-f33f8913f728" />

-  Role para baixo, até a aba com o código da sua função. Nome da aba: “Code”.
- Substitua o código no editor (lambda_function.py) pelo Código
clicando aqui.

```
import json

def lambda_handler(event, context):
    print("Recebido evento para PROCESSAR PAGAMENTO:")
    print(json.dumps(event))
    # LÃ³gica para processar pagamento aqui (simulada)
    message_attributes = event['Records'][0]['Sns']['MessageAttributes']
    order_id = message_attributes.get('OrderID', {}).get('Value', 'N/A')
    payment_type = message_attributes.get('PaymentType', {}).get('Value', 'N/A')
    print(f"Pagamento do tipo '{payment_type}' processado para o pedido: {order_id}")
    return {
        'statusCode': 200,
        'body': json.dumps(f'Pagamento processado para pedido {order_id}')
    }
```
<img width="1232" height="714" alt="image" src="https://github.com/user-attachments/assets/5fbc4f57-05ba-40a3-96fe-d9738ede808f" />
- Depois clique em Deploy.
 
5. Criar Lambda "Notificar Cliente":
6. No menu lateral esquerdo, clique em Functions.
- Create function.
- Selecione Author from scratch.
- Function name: lambda-notificacao-cliente-seunome
- Runtime: Python 3.12 (ou a mais recente suportada).
- Architecture: x86_64.
- Expanda o menu Change default execution role.
- Selecione: Use an existing role.
- Existing role: LambdaRoleSNSGeneric-seunome
- Create function.
- Role para baixo, até a aba com o código da sua função. Nome da aba: “Code”.
- Substitua o código no editor (lambda_function.py) pelo Código
clicando aqui.

```
import json

def lambda_handler(event, context):
    print("Recebido evento para NOTIFICAR CLIENTE:")
    print(json.dumps(event))
    # LÃ³gica para notificar cliente (simulada)
    message_attributes = event['Records'][0]['Sns']['MessageAttributes']
    order_id = message_attributes.get('OrderID', {}).get('Value', 'N/A')
    customer_email = message_attributes.get('CustomerEmail', {}).get('Value', 'N/A')
    print(f"NotificaÃ§Ã£o enviada para {customer_email} sobre o pedido: {order_id}")
    return {
        'statusCode': 200,
        'body': json.dumps(f'NotificaÃ§Ã£o enviada para pedido {order_id}')
    }
```

- Depois clique em Deploy.
<img width="1233" height="752" alt="image" src="https://github.com/user-attachments/assets/4dd6ce66-3476-4189-a271-45dc17d90b7c" />

7. Criar Lambda "Analisar Fraude" (acionada pela SQS):
8. No menu lateral esquerdo, clique em Functions.
- Create function.
- Selecione Author from scratch.
- Function name: lambda-analise-fraude-seunome
- Runtime: Python 3.12 (ou a mais recente suportada).
- Architecture: x86_64.
- Expanda o menu Change default execution role.
- Selecione: Use an existing role.
- Selecione: LambdaRoleSQSFraude-seunome.
- Create function.

<img width="1242" height="803" alt="image" src="https://github.com/user-attachments/assets/34323fef-4c33-46d0-9292-2114468b1fe1" />

- Role para baixo, até a aba com o código da sua função. Nome da aba: “Code”.
- Substitua o código no editor (lambda_function.py) pelo Código
clicando aqui.

```
import json

def lambda_handler(event, context):
    print("Recebido evento da SQS para ANÃLISE DE FRAUDE:")
    for record in event['Records']:
        # A mensagem da SQS contÃ©m a mensagem original do SNS
        sns_message_body = json.loads(record['body'])
        original_sns_message = json.loads(sns_message_body['Message'])
        message_attributes = sns_message_body.get('MessageAttributes', {})

        print(f"Mensagem original do SNS: {json.dumps(original_sns_message)}")
        print(f"Atributos da mensagem: {json.dumps(message_attributes)}")

        order_id = message_attributes.get('OrderID', {}).get('Value', 'N/A')
        transaction_value = float(message_attributes.get('TransactionValue', {}).get('Value', 0))

        print(f"Analisando fraude para o pedido: {order_id} com valor: {transaction_value}")

        # Simular falha para testar DLQ (descomente para testar)
        # if transaction_value > 1000:
        #    raise Exception("Valor da transaÃ§Ã£o muito alto, enviando para DLQ!")

    return {
        'statusCode': 200,
        'body': json.dumps('AnÃ¡lise de fraude concluÃ­da')
    }
```

- Depois clique em Deploy.

<img width="1244" height="821" alt="image" src="https://github.com/user-attachments/assets/0001bfe7-9892-4df2-9dcd-a475f974fa9e" />

- Adicionar Gatilho SQS:
- Na página da função lambda-analise-fraude-seunome,
selecione a aba Configuration.
- Clique em Triggers > Add trigger.
- Em Trigger configuration, selecione a trigger: SQS.
- SQS queue: Selecione sua fila fila-fraude-analise-seunome.
- Batch size: 1 (para processar uma mensagem por vez,
simplificando os logs).
- Deixe as demais opções padrão.
- Clique em Add.

<img width="1236" height="760" alt="image" src="https://github.com/user-attachments/assets/066d9570-e6b2-4190-a8a7-d166c2c60a6b" />

# Passo 5
1. Console SNS: Acesse o console do SNS.
2. Topics -> Clique no seu tópico topico-pedidos-ecommerce-seunome.
3. Role para baixo até Subscriptions e clique em Create subscription.
- Protocol: AWS Lambda.
- Endpoint: Selecione o ARN da Lambda lambda-inventario-seunome.
- Subscription filter policy (Opcional, mas vamos usar):
- Marque Subscription filter policy.
- Cole o seguinte JSON na caixa de texto da política de filtro:

```
{
"EventType": ["OrderPlaced", "InventoryCheckRequired"]
}
```

- Isso significa que esta Lambda só será acionada se a mensagem SNS tiver um atributo EventType com valor OrderPlaced OU InventoryCheckRequired.
- Create subscription.

<img width="1245" height="811" alt="image" src="https://github.com/user-attachments/assets/32639f17-7f0f-40ad-a33d-d2433adec3b2" />

4. Repita o Passo 3 para a Lambda "Processar Pagamento":
- Endpoint: ARN da Lambda lambda-pagamento-seunome.
- Subscription filter policy:

```
{
"EventType": ["OrderPlaced"],
"PaymentType": ["CreditCard", "Boleto"]
}
```

<img width="484" height="149" alt="image" src="https://github.com/user-attachments/assets/476c922e-ad34-4eaf-bec9-634c76bcf9f0" />
- Esta Lambda será acionada se EventType for OrderPlaced E PaymentType for CreditCard OU Boleto.

5. Repita o Passo 3 para a Lambda "Notificar Cliente":
- Endpoint: ARN da Lambda lambda-notificacao-cliente-seunome.
- Subscription filter policy:
<img width="485" height="118" alt="image" src="https://github.com/user-attachments/assets/a4673368-191a-469d-be5b-b9bd2e19c429" />

```
{
"EventType": ["OrderConfirmed", "OrderShipped"]
}
```

6. Repita o Passo 3 para a Fila SQS "Análise de Fraude":
- Protocol: Amazon SQS.
- Endpoint: Selecione o ARN da fila fila-fraude-analise-seunome.
- Subscription filter policy:
<img width="492" height="154" alt="image" src="https://github.com/user-attachments/assets/79e6e62e-f417-47c0-a1f4-3706200a47ab" />

```
{
"EventType": ["OrderPlaced"],
"TransactionValue": [{"numeric": [">", 500]}]
}
```

- Esta fila receberá mensagens se EventType for OrderPlaced E TransactionValue (um atributo numérico) for maior que 500.

7. Verificar/Ajustar Política de Acesso da Fila SQS:
- Vá para o console SQS -> fila-fraude-analise-seunome -> Aba Queue policies -> Access policy -> Edit.
- Verifique se existe uma declaração permitindo que o SNS envie mensagens para esta fila. Se o console não adicionou automaticamente ao criar a assinatura, adicione:
<img width="566" height="556" alt="image" src="https://github.com/user-attachments/assets/a0fcb679-b7b9-4e1f-9853-8e79d045a16b" />
- Substitua os ARNs pelos seus valores corretos.
- CUIDADO COM AS QUEBRAS DE LINHAS. ELAS PODEM CAUSAR ERROS AO SALVAR.
- Salve.
<img width="577" height="335" alt="image" src="https://github.com/user-attachments/assets/9955eed9-f145-4522-b232-52f7fe1defa9" />

# Passo 6

1. Console SNS: Vá para seu tópico Amazon SNS -> Topics -> topico-
pedidos-ecommerce-seunome.
2. Clique em Publish message.
3. Message body:
<img width="424" height="288" alt="image" src="https://github.com/user-attachments/assets/d8860538-5b46-4d2c-bfb5-6ce58de8e7f9" />

```
{
"pedido_id": "PEDIDO-123",
"cliente_id": "CLIENTE-XYZ",
"itens": [
{"produto_id": "PROD-A", "quantidade": 2},
{"produto_id": "PROD-B", "quantidade": 1}
]
}
```

4. Em Message attributes (MUITO IMPORTANTE para os filtros):
- Type: String
- Name: EventType
- Value: OrderPlaced
- Clique em Add attribute.
- Type: String
- Name: OrderID
- Value: PEDIDO-123
- Clique em Add attribute.
- Type: String
- Name: PaymentType
- Value: CreditCard
- Clique em Add attribute.
- Type: String
- Name: CustomerEmail
- Value: cliente@example.com
- Clique em Add attribute.
- Type: Number
- Name: TransactionValue
- Value: 750 (Para testar o filtro da SQS)
5. Clique em Publish message.

<img width="1245" height="784" alt="image" src="https://github.com/user-attachments/assets/188fbc09-b31d-4d1e-8616-50b6d611aed0" />

# Passo 7
1. CloudWatch Logs:
- Acesse o CloudWatch -> Log groups.
- Verifique os logs das Lambdas:
- /aws/lambda/lambda-inventario-seunome: Deve ter sido acionada (devido a EventType: OrderPlaced).
- /aws/lambda/lambda-pagamento-seunome: Deve ter sido acionada (devido a EventType: OrderPlaced e PaymentType: CreditCard).
- /aws/lambda/lambda-notificacao-cliente-seunome: Não deve ter sido acionada.
- /aws/lambda/lambda-analise-fraude-seunome: Deve ter sido acionada (após a SQS entregar a mensagem, devido a EventType: OrderPlaced e TransactionValue > 500).

<img width="1232" height="658" alt="image" src="https://github.com/user-attachments/assets/ecbcf3e8-991f-4a2c-8092-49d3ff93a683" />

# Passo 8
1. Exclua as assinaturas do tópico SNS.
2. Exclua o tópico SNS.
3. Exclua as filas SQS (a principal e a DLQ).
4. Exclua as funções Lambda.
5. Exclua os papéis IAM.

Parabéns por concluir este laboratório avançado de arquitetura fan-out com
SNS, SQS e Lambda!
Você demonstrou um excelente entendimento de como construir sistemas
distribuídos e orientados a eventos na AWS. Ao longo deste laboratório, você
aprendeu e aplicou conceitos cruciais, como:
- Fan-Out com SNS: Utilizar o Amazon SNS como um hub central para distribuir mensagens para múltiplos destinos de forma eficiente.
- Filtragem de Assinaturas SNS: Implementar filtros de atributos para garantir que cada serviço (Lambdas e SQS) processe apenas as mensagens que são relevantes para ele, otimizando o processamento e reduzindo custos.
- Desacoplamento com SQS: Integrar o SNS com o Amazon SQS para criar um buffer resiliente, permitindo que tarefas mais demoradas (como a análise de fraude) sejam processadas de forma assíncrona, sem impactar o fluxo principal.
- Processamento Paralelo com Lambda: Criar múltiplas funções Lambda que respondem a diferentes eventos, permitindo que diversas lógicas de negócio (atualização de inventário, processamento de pagamento, notificação ao cliente, análise de fraude) ocorram em paralelo.
- Tratamento de Erros com DLQs: Configurar Dead-Letter Queues (DLQs) para capturar e isolar mensagens que não puderam ser processadas com sucesso, facilitando a depuração e a reintrodução dessas mensagens no sistema, se necessário.
- Monitoramento com CloudWatch Logs: Utilizar o CloudWatch Logs para inspecionar a execução das suas funções Lambda e entender o fluxo das mensagens através do sistema.
- Gerenciamento de Permissões com IAM: Criar papéis IAM específicos para suas funções Lambda, seguindo o princípio do menor privilégio.

> Escola da Nuvem · Todos os direitos reservados.
> stay focused !!!



