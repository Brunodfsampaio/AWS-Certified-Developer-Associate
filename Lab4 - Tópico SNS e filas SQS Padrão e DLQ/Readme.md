# Laboratório - Tópico SNS e filas SQS Padrão e DLQ
### Resumo do laboratório
##### Observação: 
A interface do Console de Gerenciamento da AWS pode sofrer pequenas alterações visuais ao longo do tempo, mas os conceitos e a localização geral dos serviços permanecem consistentes. As instruções abaixo seguem a estrutura atual
(final de 2023 / início de 2024).
Este laboratório fornece uma base sólida para entender a interação entre SNS e SQS, e
a importância das Dead-Letter Queues para a resiliência de sistemas baseados em
mensagens.

### Objetivos
Este laboratório ensina como:
- Criar uma fila SQS para servir como Dead-Letter Queue (DLQ).
- Criar uma fila SQS Padrão principal.
- Configurar a fila principal para usar a DLQ (Política de Redirecionamento/Redrive Policy).
- Criar um tópico SNS Padrão.
- Inscrever a fila SQS principal no tópico SNS.
- Testar o envio de mensagens via SNS e o recebimento na fila SQS.
- Compreender e simular o envio de mensagens para a DLQ.


##### Cenário: 
Você está desenvolvendo uma aplicação baseada em microsserviços. Um
serviço (publicador) precisa notificar outros serviços (assinantes) sobre eventos
importantes (ex: novo pedido criado, usuário cadastrado). Para desacoplar os serviços
e garantir a entrega de mensagens, você usará o SNS como um barramento de
mensagens e o SQS como um buffer durável para os assinantes. Para lidar com
mensagens que falham repetidamente no processamento, você implementará uma
Dead-Letter Queue (DLQ) para análise posterior, evitando que mensagens
problemáticas bloqueiem o processamento das demais.

---

# Parte 1: Criação das Filas SQS
1. Acessar Console SQS: Faça login no Console AWS e navegue até o serviço SQS
(Simple Queue Service).

2. Criar Fila Dead-Letter (DLQ):
- Clique em Criar fila.
- Tipo: Selecione Padrão (Standard).
- Nome: Insira um nome único, ex: minha-dlq-lab-seunome-aaaammdd.
- Configurações: Mantenha as configurações padrão por enquanto (ex: Período de retenção da mensagem, Tempo limite de visibilidade). Não configure uma DLQ para a própria DLQ neste passo.
- Clique em Criar fila.
- Após a criação, selecione a fila recém-criada na lista.
- Na aba Detalhes, localize e Copie o ARN da DLQ. Guarde este ARN, você precisará dele em breve.

<img width="1218" height="324" alt="image" src="https://github.com/user-attachments/assets/196d7a56-f4d6-4330-bb4c-c2c726acaad4" />
<img width="1213" height="779" alt="image" src="https://github.com/user-attachments/assets/0d4b4439-2f8d-4672-8dce-440020056771" />
<img width="1253" height="392" alt="image" src="https://github.com/user-attachments/assets/191058a0-ca5d-4a5a-b784-655363a300c8" />


3. Criar Fila Principal:
- Volte para a página principal do SQS (clicando em "Filas" no menu esquerdo) e clique em Criar fila.
- Tipo: Selecione Padrão (Standard).
- Nome: Insira um nome único, ex: minha-fila-principal-lab-seunome-aaaammdd.
- Configurações: Role para baixo até a seção Fila de mensagens mortas (Dead-letter queue).
-   Marque a opção Habilitado.
-   Escolher fila de mensagens mortas: Cole o ARN da DLQ que você copiou no passo anterior.
-   Número máximo de recebimentos: Defina um valor baixo para facilitar o teste, por exemplo, 3. Isso significa que se uma mensagem for recebida (polled) 3 vezes sem ser excluída com sucesso, ela será movida para a DLQ.
- Mantenha as demais configurações padrão ou ajuste conforme necessário.
- Clique em Criar fila.
- Após a criação, selecione a fila principal na lista.
- Na aba Detalhes, localize e Copie o ARN da fila principal. Guarde este ARN, você precisará futuramente.

<img width="1261" height="787" alt="image" src="https://github.com/user-attachments/assets/53e6b52b-6ef1-45b7-b47c-af71034168dc" />
<img width="1261" height="787" alt="image" src="https://github.com/user-attachments/assets/3a8574ae-fbaf-4942-931d-001d06422d45" />

# Parte 2: Criação do Tópico SNS
1. Acessar Console SNS: Navegue até o serviço SNS (Simple Notification Service).
2. Criar Tópico:
- No menu esquerdo, clique em Tópicos.
- Clique em Criar tópico.
- Tipo: Selecione Padrão (Standard).
- Nome: Insira um nome único, ex: meu-topico-lab-seunome-aaaammdd.
- Política de acesso: Deixe como Básico por enquanto. A permissão para SQS será adicionada na inscrição.
- Clique em Criar tópico.
- Após a criação, na página de detalhes do tópico, Copie o ARN do tópico.
- Guarde este ARN.

<img width="1265" height="493" alt="image" src="https://github.com/user-attachments/assets/c72d0404-a1b4-4487-80a0-71b6199dbe41" />
<img width="1240" height="737" alt="image" src="https://github.com/user-attachments/assets/5eea4e44-0b01-48c2-8d86-e2e0f0bd987a" />


# Parte 3: Conectando SNS e SQS
### 1. Inscrever Fila SQS no Tópico SNS: 
- Ainda no console SNS, com seu tópico selecionado, vá para a aba Assinaturas.
- Clique em Criar assinatura.
- ARN do Tópico: Deve estar preenchido com o ARN do seu tópico.
- Protocolo: Selecione Amazon SQS.
- Endpoint: Cole o ARN da sua Fila Principal (criada no passo 3).
- (Opcional, recomendado para simplificar) Habilitar entrega de mensagens brutas (Enable raw message delivery): Marque esta caixa se desejar que a mensagem chegue na fila SQS exatamente como foi publicada no SNS, sem o metadado extra do SNS. Para este lab, pode deixar desmarcado para ver a estrutura completa.
- Clique em Criar assinatura.
- Nota: A criação da assinatura geralmente configura automaticamente a política de acesso da fila SQS para permitir que o tópico SNS envie mensagens para ela. Vamos verificar isso (opcional, mas bom aprendizado).

<img width="1269" height="805" alt="image" src="https://github.com/user-attachments/assets/f8c88bb0-da89-4803-b151-f8243f7c6610" />
<img width="1261" height="840" alt="image" src="https://github.com/user-attachments/assets/a5b1c0b6-6e6b-41b3-8b0d-ec5f2aa745b5" />


### 2. Configure Política de Acesso da Fila SQS:
Agora, a parte crucial com os ARNs e a política do SQS.
- Volte para o console SQS.
- Selecione a sua fila principal.
- Vá para a aba Política de filas.
- Clique em Editar.
- Exclua a politica atual.
- Baixe o arquivo “Política SQS”, na atividade 5.
- Abra o arquivo em um bloco de notas e faça as alterações devidas:
```
{
  "Version": "2012-10-17",
  "Id": "__default_policy_ID",
  "Statement": [
    {
      "Sid": "__owner_statement",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<COLE AQUI SEU ID CONTA DA AWS (12 DÍGITOS)>:root"
      },
      "Action": "SQS:*",
      "Resource": "<COLE AQUI O ARN DA SUA FILA PRINCIPAL>"
    },
    {
      "Sid": "Allow-SNS-SendMessage",
      "Effect": "Allow",
      "Principal": {
        "Service": "sns.amazonaws.com"
      },
      "Action": "sqs:SendMessage",
      "Resource": "<COLE AQUI O ARN DA SUA FILA PRINCIPAL>",
      "Condition": {
        "ArnEquals": {
          "aws:SourceArn": "< COLE AQUI O ARN DO SEU TÓPICO SNS >"
        }
      }
    }
  ]
}
```
- Onde Colar os ARNs (e o ID da Conta): o "AWS": Cole o seu ID de conta da AWS (12 dígitos). Você encontra o ID no canto superior direito do console.
- o "Resource": " <COLE AQUI O ARN DA SUA FILA PRINCIPA> ", cole o ARN completo da sua FILA PRINCIPAL.
- "aws:SourceArn": " <COLE AQUI O ARN DO SEU TÓPICO SNS> ": Cole o ARN do seu TÓPICO SNS.
- Edite com cuidado.
- Copie e cole a sua política devidamente editada no SQS.
- Salve.

<img width="1260" height="830" alt="image" src="https://github.com/user-attachments/assets/b5829b35-c95f-4518-8302-8c00d06c7308" />
<img width="1273" height="807" alt="image" src="https://github.com/user-attachments/assets/e83c922c-6750-4e16-93c8-7ea96db5b234" />
<img width="1240" height="719" alt="image" src="https://github.com/user-attachments/assets/2a3aa557-1078-4566-b21f-1caa34f95ead" />



# Parte 4: Testes
### 1. Testar Publicação e Recebimento Normal:
- Volte ao console SNS e selecione seu tópico.
- Clique no botão Publicar mensagem.
- Assunto (Opcional): Teste de Mensagem Normal
- Corpo da mensagem:
```
{
"evento": "pedido_criado",
"pedido_id": "12345",
"cliente_id": "9876",
"timestamp": "2024-03-15T10:00:00Z"
}
```
- Não mexa nos atributos da mensagem por enquanto.
- Clique em Publicar mensagem.
- Vá para o console SQS, selecione sua fila principal.
- Clique em Enviar e receber mensagens.
- Na seção Receber mensagens, clique em Sondar mensagens.
- Você deve ver 1 mensagem disponível. Clique nela para ver o corpo (que incluirá a estrutura do SNS se você não marcou "entrega bruta").
- IMPORTANTE: Não exclua a mensagem ainda se quiser testar a DLQ no próximo passo. Se quiser apenas confirmar o recebimento, pode excluí-la agora.

<img width="1245" height="715" alt="image" src="https://github.com/user-attachments/assets/3755170c-4060-4ef0-90f9-38b45c74921f" />
<img width="1265" height="811" alt="image" src="https://github.com/user-attachments/assets/c0f21fb4-8c77-4050-b899-036156fefc9f" />
<img width="1262" height="829" alt="image" src="https://github.com/user-attachments/assets/a3898ba7-ad53-4398-8f27-2c52c0416c05" />


### 2. Testar Envio para a DLQ (Simulação de Falha):
- Antes de seguirmos, valide as configurações de sondagem de 10 para 3.
- Acesse a sua FILA PRINCIPAL.
- Desça até “Receber mensagens”.
- Clique em “Editar configurações de sondagem”.
- Verifique se você configurou corretamente ao criar a fila. Caso esteja 3, pode sair sem salvar.
- Caso esteja 10, altere o “Número máximo de mensagens” para 3.
- Salvar.

<img width="1259" height="606" alt="image" src="https://github.com/user-attachments/assets/c8502b6b-240e-4b1e-b94b-9e45a8ab3efc" />
<img width="1254" height="757" alt="image" src="https://github.com/user-attachments/assets/cb4cb480-2ff7-46f2-a2e8-5d9f3802d604" />


### Agora vamos para os testes:
- Certifique-se de que a mensagem do passo anterior ainda está na fila principal (se a excluiu, publique outra).
- Na fila principal, clique em Enviar e receber mensagens.
- Clique em Pesquisar mensagens. A mensagem aparecerá. Observe o Contagem de recebimentos (Receive count) na aba Detalhes da mensagem (deve ser 1).
- Não faça nada com a mensagem. Aguarde o "Tempo limite de visibilidade" da fila expirar (o padrão é 30 segundos). A mensagem voltará a ficar visível na fila.
- Clique em Pesquisar mensagens novamente. A mesma mensagem aparecerá. A Contagem de recebimentos agora será 2.
- Repita: aguarde o tempo limite de visibilidade expirar.
- Clique em Pesquisar mensagens novamente. A Contagem de recebimentos será 3.
- Aguarde o tempo limite de visibilidade expirar mais uma vez.
- Clique em Sondar mensagens novamente. A mensagem não deve mais aparecer na fila principal, pois atingiu o "Número máximo de recebimentos" (3) que configuramos.
- Agora, vá para a fila DLQ (minha-dlq-lab-...).
- Clique em Enviar e receber mensagens.
- Clique em Pesquisar mensagens.
- A mensagem original que falhou em ser processada na fila principal deve agora estar na DLQ! Você pode inspecioná-la.

<img width="1230" height="727" alt="image" src="https://github.com/user-attachments/assets/6be3c901-86b5-4336-a4c4-b68235e22083" />
<img width="1260" height="828" alt="image" src="https://github.com/user-attachments/assets/b856ad7c-e30e-4e5e-bbc5-8bc8ff7209f4" />
<img width="1251" height="633" alt="image" src="https://github.com/user-attachments/assets/ddeb9aaa-8b73-4217-9eaa-b394f43918d8" />
<img width="1259" height="836" alt="image" src="https://github.com/user-attachments/assets/b08c966e-0928-4622-90a4-4d43d3eaa8c7" />
<img width="1267" height="798" alt="image" src="https://github.com/user-attachments/assets/2642b2d9-e621-4471-9e97-08b9764930fa" />

# Parte 5: Limpeza
1. Limpeza de Recursos (IMPORTANTE para evitar custos):
- Excluir Assinaturas SNS: No console SNS, selecione seu tópico, vá em "Assinaturas", selecione a assinatura SQS (e qualquer outra que tenha criado) e clique em "Excluir".

 <img width="1252" height="721" alt="image" src="https://github.com/user-attachments/assets/d933fcd5-78db-4e19-891a-c4d594a0c2de" />

- Excluir Tópico SNS: No console SNS, vá em "Tópicos", selecione seu tópico e clique em "Excluir". Confirme a exclusão.

<img width="1265" height="662" alt="image" src="https://github.com/user-attachments/assets/219a0ae3-5f02-43f7-a6e1-defd2344cfe4" />

- Excluir Filas SQS: No console SQS, selecione a fila principal e clique em "Excluir". Confirme a exclusão. Faça o mesmo para a fila DLQ. Nota: Às vezes é necessário aguardar um pouco após excluir a assinatura para conseguir excluir a fila.

<img width="1252" height="653" alt="image" src="https://github.com/user-attachments/assets/09443adf-92f6-4fa9-95ae-c72912bd8609" />
<img width="1265" height="657" alt="image" src="https://github.com/user-attachments/assets/7ee61f3c-1aaa-4f21-8ddf-7a1bbec80403" />

# ✅ Resumo do Laboratório - SNS + SQS + DLQ
### 🔹 Objetivo Geral
Integrar SNS e SQS para um sistema de mensagens desacoplado e resiliente.

Implementar uma Dead-Letter Queue (DLQ) para lidar com mensagens não processadas.

### 1. Criação das Filas SQS
Criada uma fila DLQ (Dead-Letter Queue) do tipo Padrão.

Criada uma fila principal, também do tipo Padrão.

Configurada para usar a DLQ.

Definido Número máximo de recebimentos (ex: 3).

### 2. Criação do Tópico SNS
Criado um tópico SNS Padrão.

Copiado o ARN do tópico para uso posterior.

### 3. Conectando SNS com SQS
Fila principal inscrita no tópico SNS como assinante.

Política de acesso da fila SQS editada manualmente:

Permissão ao serviço SNS para enviar mensagens para a fila.

Inserção do ARN da fila e do tópico na política.

Inclusão do ID da conta AWS.

### 4. Testes
🔸 Publicação normal:
Publicada mensagem JSON via SNS.

Mensagem recebida na fila principal com estrutura SNS (sem entrega bruta).

🔸 Simulação de falha e envio à DLQ:
Mensagem não excluída propositalmente.

Após atingir o limite de recebimentos (3), mensagem redirecionada para a DLQ.

Verificação da mensagem na DLQ.

###  5. Limpeza de Recursos
Excluída assinatura SNS da fila.

Excluído o tópico SNS.

Excluídas filas SQS principal e DLQ.

> stay focused!




