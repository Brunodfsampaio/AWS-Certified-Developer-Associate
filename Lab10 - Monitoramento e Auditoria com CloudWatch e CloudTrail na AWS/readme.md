# Laboratório - Monitoramento e Auditoria com CloudWatch e CloudTrail na AWS

### Resumo
Este laboratório prático orienta a criação de um ambiente de monitoramento e auditoria na
AWS. Os participantes irão configurar alarmes para monitorar a utilização de CPU em uma
instância EC2, habilitar o rastreamento de atividades da conta com o AWS CloudTrail e
visualizar logs armazenados em um bucket S3. O exercício também inclui o envio de
notificações por e-mail usando o Amazon SNS.

### Objetivos do Laboratório
Este laboratório ensina como configurar:
- Alarme CloudWatch: Monitorar a CPU de uma instância EC2 e enviar notificações por e-mail (SNS) quando um limite for atingido.
- CloudTrail: Habilitar a auditoria (rastreamento de eventos) na sua conta AWS e armazenar os logs em um bucket S3.
- Visualização de Logs: Acessar os logs do CloudTrail via S3 ou CloudWatch Logs.

### Cenário
Você precisa monitorar o desempenho de uma instância EC2 e ser alertado sobre alta
utilização de CPU. Também é necessário manter um registro de auditoria de todas as
atividades na sua conta AWS para segurança e conformidade.
Pré-requisitos
- Conta AWS ativa com permissões para EC2, CloudWatch, CloudTrail, SNS e S3.
- Permissões IAM necessárias:
- CloudWatchFullAccess
- AWSCloudTrail_FullAccess
- AmazonSNSFullAccess
- AmazonS3FullAccess
- AmazonEC2FullAccess
- Navegador Web
- Par de chaves para acesso via SSH à instância EC2

## Passo 1:
1. Console EC2: Acesse o console do EC2.
- Launch Instance:
- Name: Instancia-Teste-CloudWatch
- AMI: Amazon Linux 2 AMI (HVM) - x86_64.
- Instance type: t2.micro
- Key pair: Selecione/crie. (Você vai precisar acessar a instância, então saiba onde está salva o seu par de chaves)
- Network settings:
- VPC: Sua VPC padrão.
- Subnet: Qualquer sub-rede pública.
- Auto-assign public IP: Enable.
- Firewall:
- Create security group:
- Name: SG-Teste-CloudWatch-SeuNome
- Inbound: SSH, Source: My IP
- Configure storage: Padrão.
- Advanced details: Nada.
- Launch Instance.
- Anote o Instance ID.

<img width="1020" height="300" alt="image" src="https://github.com/user-attachments/assets/9c63bf17-33da-4637-917c-b570a4646d75" />

## Passo 2:
- Console CloudWatch: Acesse o console do CloudWatch.
1. Criar Alarme:
- Alarms -> Create alarm.
- Select metric:

<img width="1257" height="412" alt="image" src="https://github.com/user-attachments/assets/d0924f99-ec26-49e8-ab90-5da46947cc33" />

- All metrics -> EC2 -> Per-Instance Metrics.

<img width="1257" height="821" alt="image" src="https://github.com/user-attachments/assets/d1f6e0b5-f976-47bc-a9f2-3540653c6365" />

- Cole o ID da instância (anotada do passo anterior) na caixa de pesquisa.
- Cole o ID da instância na caixa de pesquisa.

<img width="1228" height="782" alt="image" src="https://github.com/user-attachments/assets/c865538d-842c-41d1-ac27-eb215fc0b51e" />

- Marque CPUUtilization da sua instância.

<img width="1240" height="843" alt="image" src="https://github.com/user-attachments/assets/21fccfa1-cd62-4f9e-bf11-a4a6174d3ce2" />

- Aba Graphed metrics (1): Verifique.
- Select metric.
- Specify metric and conditions:
- Statistic: Average.
- Period: 5 minutes.
- Conditions:
- Threshold type: Static.
- Whenever CPUUtilization is...: Greater.
- than...: 70.
- Additional configuration:
- Datapoints to alarm: 1 out of 1.
- Missing data treatment: Treat missing data as good (not breaching threshold).

<img width="1218" height="817" alt="image" src="https://github.com/user-attachments/assets/91dcd81f-e141-4d9c-b839-e0033e0435f2" />

- Next.
- Configure actions:
- Alarm state trigger: In alarm.
- Select an SNS topic: Crie um tópico SNS com o nome SNS-SeuNome (confirme a assinatura).

<img width="1263" height="816" alt="image" src="https://github.com/user-attachments/assets/77d94cf9-c91b-4194-880f-24364e6da598" />

- Next.
- Add a name and description:
- Alarm name: AlarmeCPU-Instancia-SeuNome

<img width="1265" height="700" alt="image" src="https://github.com/user-attachments/assets/91c4b972-4d64-4e03-a37f-7d95f576fe39" />

- Next.
- Preview and create: Revise e Create alarm.

### Passo 3:
- Console CloudTrail: Acesse o console do CloudTrail.
- Criar uma Trilha (Trail):
- Trails -> Create trail.

### Step 1: Choose trail attributes:
- Trail name: trilha-auditoria-seunome
- Storage location:
- Create a new S3 bucket.
- Trail log bucket and folder: O CloudTrail sugerirá um nome.
- Log file SSE-KMS encryption: Marque (Enabled).
- Customer managed AWS KMS key: New.
- AWS KMS alias: alias/CloudTrailKey-seunome.
- Additional settings:
- Log file validation: Deixe habilitado.
- SNS notification delivery: Deixe desabilitado.
- CloudWatch Logs: Deixe desabilitado.
- Clique em Next.

<img width="1265" height="840" alt="image" src="https://github.com/user-attachments/assets/9d2251f6-c324-465a-a8eb-9cf7f804b448" />

### Step 2: Choose log events:
- Event type:
- Management events: Mantenha marcado.
- API activity: Mantenha Read e Write.
- Data events: Desmarque.
- Insights events: Desmarque.
- Network activity events: Desmarque.
- Clique em Next.

<img width="1246" height="806" alt="image" src="https://github.com/user-attachments/assets/331ea0db-272a-405e-b0bf-6e9e0ea46dee" />

### Step 3: Review and create: Revise e Create trail.

## Passo 4:
- Logs do CloudTrail (no S3 - Padrão):
- Acesse o console do S3.
- Localize o bucket criado pelo CloudTrail.
- Navegue pela estrutura de pastas para encontrar os logs (arquivos .gz JSON).

<img width="1258" height="763" alt="image" src="https://github.com/user-attachments/assets/b152bd67-6c77-4f0e-9b94-16c870aa0fe3" />

- (Opcional) Enviar Logs do CloudTrail para o CloudWatch Logs:
- Console do CloudTrail -> Trails -> Sua trilha.Seção CloudWatch Logs -> Edit.
- CloudWatch Logs: Marque (Enabled).
- Log group: Crie um novo (ex: CloudTrail/Logs-SeuNome).
- IAM Role: Crie um novo: CloudTrailRoleForCloudWatchLogs, clique em Allow.
- Save changes.

<img width="1264" height="711" alt="image" src="https://github.com/user-attachments/assets/a337d549-18e1-471b-bbc6-11ae1b91523b" />

- Logs no CloudWatch Logs (se configurado):
- Console CloudWatch: Logs -> Log groups.
- Visualizar Logs: Clique no grupo de logs.

<img width="1270" height="770" alt="image" src="https://github.com/user-attachments/assets/409f9894-a53c-4d0a-844c-1cdfc4f01a49" />
<img width="1272" height="836" alt="image" src="https://github.com/user-attachments/assets/aa5d0465-f816-46f7-b651-47f0fba41bbf" />

## Passo 5:
- Conectar-se à Instância (SSH): Use SSH.
- Instalar o stress:
- Execute o primeiro comando: sudo yum update -y
- Depois execute o segundo comando: sudo amazon-linux-extras install epel -y
- Depois execute o terceiro comando: sudo yum install stress -y
- Executar o stress:
- stress --cpu 8 --timeout 600

<img width="1275" height="813" alt="image" src="https://github.com/user-attachments/assets/56696644-88dc-4b61-b248-6a0810118d93" />
<img width="1295" height="581" alt="image" src="https://github.com/user-attachments/assets/7ab98a1e-d5fc-4a9a-b468-771ad5f3940d" />

### Passo 6:
- Acesse o AWS CloudWatch no console.
- Vá até Alarms e localize o alarme criado anteriormente.
- Verifique se o estado mudou para "In alarm".
- Acesse seu e-mail e confirme o recebimento da notificação enviada pelo SNS.
- Importante: Tire um print do estado do alarme e do e-mail de notificação para anexar à entrega da atividade no Classroom.
- Parar o stress: Ctrl+C.

<img width="1268" height="671" alt="image" src="https://github.com/user-attachments/assets/fe8653f2-66e5-46f5-b1fd-b7332c24aafc" />

### Passo 7:
- Exclua o Alarme CloudWatch.
- Exclua a Trilha do CloudTrail.
- Exclua o Bucket S3 do CloudTrail (se criou um novo - esvazie antes de excluir).
- Exclua o Assinatura e tópico do SNS (se criou).
-  Exclua a instância EC2.
-  Exclua a role
-  Delete seu grupo de segurança -opcional

  > stay focused!


