# Laboratório - AWS Security Token Service (STS)

# Resumo
Observação: A interface do Console de Gerenciamento da AWS pode sofrer
pequenas alterações visuais ao longo do tempo, mas os conceitos e a localização
geral dos serviços permanecem consistentes. As instruções neste resumo seguem a
estrutura geral das funcionalidades.
Este laboratório prático demonstra o uso de credenciais temporárias na AWS via
AWS Security Token Service (STS). Você aprenderá a criar e assumir uma role IAM
com permissões restritas, executar scripts Python no CloudShell para gerar
credenciais temporárias e validar acessos permitidos/negados.
Objetivo do laboratório
- Criar uma role IAM temporária com AmazonS3FullAccess.
- Alternar para a role no console e via CLI.
- Gerar credenciais temporárias via STS.
- Configurar e testar credenciais na AWS CLI.
- Simular expiração de credenciais.
- Compreender o impacto da política de confiança da role.
- Excluir todos os recursos criados.

# Cenário:
Você atuará como um usuário IAM com permissão para criar e assumir roles
temporárias, navegando por etapas práticas no console e no CloudShell.
Pré-requisitos
- Acesso ao Console de Gerenciamento da AWS.
- Usuário IAM (não o root) com permissões para:
- Criar e gerenciar roles no IAM.
- Usar o AWS CloudShell (incluindo instalar pacotes como Python e Boto3).
- Assumir roles.
- Familiaridade básica com a linha de comando (Linux) e o AWS CLI.

## Passo 1: Configuração do Ambiente
1. Abrir CloudShell: No Console AWS, clique no ícone do CloudShell (canto
superior).
2. Atualizar e Instalar Python:
```
sudo yum update -y
sudo yum install -y python3
python3 --version
```
3. Instalar Boto3:
```
pip3 install boto3 --upgrade
python3 -c "import boto3; print(boto3.__version__)"
```

<img width="1259" height="531" alt="image" src="https://github.com/user-attachments/assets/05e812fa-06c7-4d48-8628-a74ae299b79e" />

## Passo 2: Criando uma Role Temporária
1. Navegar para IAM Roles: Acesse o console AWS, vá para IAM > Funções
(no menu lateral "Gerenciamento de acesso") > Criar perfil.
<img width="601" height="263" alt="image" src="https://github.com/user-attachments/assets/d0dabb45-6559-4925-b1f7-f06611bb6550" />
2. Configurar Política de Confiança:
a. Em "Tipo de entidade confiável", selecione "Política de confiança
personalizada".
<img width="513" height="307" alt="image" src="https://github.com/user-attachments/assets/b58f83de-0a1f-46cb-a818-8e54290da2a1" />
b. Role a página, clique em "Adicionar uma entidade principal".
<img width="605" height="234" alt="image" src="https://github.com/user-attachments/assets/49eb5a6e-8753-4674-8030-86293ad7cc56" />
c. Em "Tipo de entidade principal", selecione "Usuários IAM".
d. Em "ARN", cole o ARN do seu usuário IAM (encontre-o em IAM > Usuários > Seu Usuário).
<img width="613" height="237" alt="image" src="https://github.com/user-attachments/assets/983ff139-6791-42f5-9b23-fb0a0dfe6477" />
e. Clique em "Adicionar entidade principal" e depois em "Próximo".
<img width="557" height="294" alt="image" src="https://github.com/user-attachments/assets/e142d8b0-78c6-4956-9f31-6e00d32f610a" />
3. Adicionar Permissões:
a. Na tela "Adicionar políticas de permissões", procure e selecione
AmazonS3FullAccess.
b. Clique em "Próximo".
<img width="1216" height="506" alt="image" src="https://github.com/user-attachments/assets/b3ac3ad4-d62f-43cd-815a-2d499e93829b" />
4. Nomear e Criar Role:
a. Em "Nome da função", digite SeuNomeRole.
b. Clique em "Criar perfil".
<img width="1174" height="238" alt="image" src="https://github.com/user-attachments/assets/547f00d8-9513-47f9-a11f-29207e1b638a" />

## Passo 3: Alternando para a Role Temporária
1. Alternar no Console:
a. Duplique a aba do seu navegador.
b. Na aba duplicada, clique no ícone do nome da sua conta (canto
superior direito).
c. Copie o "ID da conta".
d. Clique em "Alternar função".
<img width="451" height="546" alt="image" src="https://github.com/user-attachments/assets/a2f722a1-a19a-4f39-9b61-1d7515470e01" />
e. Preencha os campos:
▪ ID da conta: Cole o ID da sua conta.
▪ Nome do perfil do IAM: SeuNomeRole
▪ Nome de exibição opcional: SeuNomeAcessoTemporario
▪ Cor da tela opcional: Escolha uma cor.
f. Clique em "Alternar perfil".
<img width="590" height="410" alt="image" src="https://github.com/user-attachments/assets/821cf6df-b1bf-434d-a40e-7908a70ec806" />
<img width="1066" height="445" alt="image" src="https://github.com/user-attachments/assets/8e473f63-6cae-462e-8bb1-bc249c354d55" />

g. Validação: Observe que o console mudará de cor e você terá acesso
ao S3, mas acesso negado a outros serviços como Lambda.
<img width="601" height="210" alt="image" src="https://github.com/user-attachments/assets/87667158-1d91-4315-a389-8eff16ea5f30" />
2. Validar Acesso S3: Na barra de busca, digite S3 e valide o acesso.
<img width="1285" height="524" alt="image" src="https://github.com/user-attachments/assets/2b7683ed-df02-4cf4-a871-60396aa0e6bb" />
3. Como sair da role e voltar para sua conta original no console da AWS
a. No canto superior direito do console da AWS, clique no ícone com o
seu nome de usuário (ou no nome da role que está em uso).
b. No menu suspenso, clique em “Retornar” (ou “Switch back”, se o
console estiver em inglês) para voltar à sua conta original.
<img width="703" height="621" alt="image" src="https://github.com/user-attachments/assets/6143193c-fd7c-48de-9464-48d701b8addf" />
4. Verificar Identidade no CloudShell:
a. Localize o ícone do terminal, ele está à esquerda do ícone do sino
(notificações)
b. Clique nesse ícone para abrir o AWS CloudShell, o terminal interativo
integrado ao console e execute:
```
aws sts get-caller-identity
```
<img width="902" height="507" alt="image" src="https://github.com/user-attachments/assets/955cd684-8b1b-4596-9317-fbbbfbc7e6e0" />
5. Assumir uma role via AWS CLI:
a. Acesse o CloudShell
b. Faça o download do arquivo em python credenciais_temporarias.py.
```
import boto3
import sys
import argparse
from botocore.exceptions import ClientError


def gerar_credenciais_temporarias(role_arn, session_name, duration):
    # Inicializa o cliente STS
    sts_client = boto3.client('sts')

    try:
        # Solicita as credenciais temporárias
        response = sts_client.assume_role(
            RoleArn=role_arn,
            RoleSessionName=session_name,
            DurationSeconds=duration
        )

        # Exibe as credenciais temporárias
        credentials = response['Credentials']
        print(f"AWS Access Key ID: {credentials['AccessKeyId']}")
        print(f"AWS Secret Access Key: {credentials['SecretAccessKey']}")
        print(f"AWS Session Token: {credentials['SessionToken']}")

    except ClientError as e:
        if e.response['Error']['Code'] == 'ValidationError' and 'DurationSeconds exceeds the MaxSessionDuration' in \
                e.response['Error']['Message']:
            print("[ERRO] A duração solicitada excede o limite de tempo máximo permitido para esta role.")
            print("Por favor, solicite ao administrador da role para aumentar o limite de tempo.")
        else:
            print(f"[ERRO] Falha ao obter credenciais: {e}")
            sys.exit(1)


def main():
    parser = argparse.ArgumentParser(description="Gerar credenciais temporárias do AWS STS")
    parser.add_argument('--role-arn', required=True, help='ARN da role a ser assumida')
    parser.add_argument('--session-name', required=True, help='Nome único para a sessão')
    parser.add_argument('--duration', type=int, default=3600, help='Duração em segundos (padrão: 3600 = 1 hora)')

    args = parser.parse_args()

    gerar_credenciais_temporarias(args.role_arn, args.session_name, args.duration)


if __name__ == "__main__":
    main()
```

c. Depois faça o upload do arquivo em python para o CloudShell (botão
"Ações" > "Carregar arquivo").
<img width="918" height="282" alt="image" src="https://github.com/user-attachments/assets/a71e07ad-ff14-4463-aa61-9e1b9a410520" />
d. Verifique o upload com: ls
<img width="622" height="329" alt="image" src="https://github.com/user-attachments/assets/de14b6a8-f3ff-44ee-aa73-3de6c6eb4b0f" />

## Passo 4: Executar o Script
1. Obter ARN da Role:
a. No console, vá para IAM > Funções.
b. Pesquise e clique em SeuNomeRole.
c. Copie o ARN da função.
<img width="907" height="263" alt="image" src="https://github.com/user-attachments/assets/97f04d9d-30e6-4944-add3-876d45d1f6f3" />
No campo “Duração máxima da sessão” indica por quanto tempo uma sessão/login
permanece ativa antes de expirar. Por padrão, esse tempo é de 1 hora. Caso queira
aumentar esse período, basta clicar em Editar.
Neste laboratório, a duração da sessão permanece no valor padrão de 1 hora.

2. Testar Duração (Erro Esperado):
a. Execute o comando abaixo, substituindo SEU_ROLE_ARN pelo ARN
copiado e SeuNomeRole pelo nome da sua role.

```
python3 credenciais_temporarias.py --role-arn
arn:aws:iam::SEU_ROLE_ARN:role/SeuNomeRole --session-name AcessoTemporario --
duration 7200
```

b. Observe o erro, pois a duração padrão máxima é 3600 segundos igual a (1 hora).
<img width="906" height="141" alt="image" src="https://github.com/user-attachments/assets/8997a2cd-eba1-48dc-b366-8febe14f4096" />

3. Executar com Duração Válida:
a. Altere --duration para 3600 e execute novamente:
```
python3 credenciais_temporarias.py --role-arn
arn:aws:iam::SEU_ROLE_ARN:role/SeuNomeRole --session-name AcessoTemporario --
duration 3600
```

<img width="902" height="240" alt="image" src="https://github.com/user-attachments/assets/72ab5a89-6084-457d-ba80-5e0232247075" />

b. Copie as informações: AWS Access Key ID, AWS Secret Access Key, AWS Session Token exibidas no terminal.

## Passo 5: Configurar no AWS CLI
1. Configurar Credenciais Temporárias: No CloudShell, digite aws configure
e preencha:
a. AWS Access Key ID [None]: (Cole seu AWS Access Key ID)
b. AWS Secret Access Key [None]: (Cole seu AWS Secret Access Key)
c. Default region name [None]: us-east-1
d. Default output format [None]: json
2. Testar Acesso S3 (Erro Esperado):
```
aws s3 ls
```

a. Observe o erro, pois o Session Token ainda não foi configurado.
<img width="906" height="70" alt="image" src="https://github.com/user-attachments/assets/cddbcab0-355d-419e-8d89-aca9fd26ffd3" />

## Passo 6: Adicionar o Session Token
1. Editar Arquivo de Credenciais:
```
sudo nano ~/.aws/credentials
```
2. Adicionar Session Token:
a. Vá para a última linha e adicione:
```
aws_session_token = SEU_SESSION_TOKEN
```

<img width="908" height="467" alt="image" src="https://github.com/user-attachments/assets/69ce3439-14ce-4459-9ed7-60199a210c36" />

(Substitua SEU_SESSION_TOKEN pelo token que você copiou).

b. Pressione CTRL+O e Enter para salvar o arquivo e depois CTRL+X para
sair.

3. Verificar Conteúdo:
```
cat ~/.aws/credentials
```
## Passo 7: Testar Permissões
1. Testar Acesso S3 (Permitido):
```
aws s3 ls
```

a. Deve listar seus buckets S3.
<img width="895" height="285" alt="image" src="https://github.com/user-attachments/assets/b2417fdc-322d-43db-8a00-365b5f2c999e" />

2. Verificar Identidade Atual:
```
aws sts get-caller-identity
```
<img width="898" height="172" alt="image" src="https://github.com/user-attachments/assets/d9c132ef-db59-42f5-a2b7-c6ad3257ec53" />

3. Testar Acesso Lambda (Negado):
```
aws lambda list-functions
```

a. Deve retornar "acesso negado".
<img width="926" height="140" alt="image" src="https://github.com/user-attachments/assets/4a6a5159-56fc-4cbc-bf78-7214888573b9" />

## Passo 8: Simular Expiração
1. Verificar Tempo de Sessão:
uptime

a. Observe o tempo de atividade do CloudShell.
<img width="910" height="139" alt="image" src="https://github.com/user-attachments/assets/65d48717-3cec-48ed-a108-1a57e0e250a0" />
2. Testar Acesso Após Expiração: Após a duração definida (--duration 3600
= 1 hora), tente novamente:
```
aws s3 ls
```
a. Deve retornar um erro de credenciais expiradas.
<img width="910" height="274" alt="image" src="https://github.com/user-attachments/assets/96127e5d-5ab7-4a11-941e-2e19e8f7234c" />

## Passo 9: Restaurar Credenciais Originais
1. Editar ~/.aws/credentials:
```
sudo nano ~/.aws/credentials
```
a. Comente (#) ou remova as linhas: aws_access_key_id, aws_secret_access_key, aws_session_token.
b. Salve e saia (CTRL+O e Enter, CTRL+X).
<img width="907" height="111" alt="image" src="https://github.com/user-attachments/assets/eace952b-62d6-400b-81e8-35f0f9c5aa46" />
2. Editar ~/.aws/config:
```
sudo nano ~/.aws/config
```
a. Comente (#) ou remova as linhas: region = us-east-1, output = json.
b. Salve e saia (CTRL+O e Enter, CTRL+X).

## Passo 10: Confirmar Identidade Atual
1. Verificar Identidade:
```
aws sts get-caller-identity
```
a. Confirme que você voltou ao seu usuário IAM original.
<img width="901" height="196" alt="image" src="https://github.com/user-attachments/assets/cab3e2ef-e5aa-479d-b4a6-83a83e213ef5" />

## Passo 11: Acessar a Role no Console – Teste de Segurança - Política de
Confiança
1. Navegar para IAM Roles: No console, vá para IAM > Funções.
2. Selecionar Role: Clique em SeuNomeRole.

## Passo 12: Editar Política de Confiança
1. Editar Política: Vá para a aba "Relações de Confiança" > "Editar política de
confiança".
<img width="916" height="342" alt="image" src="https://github.com/user-attachments/assets/5d8d14f0-12df-489a-9ae9-6f09d12e3164" />
2. Modificar JSON:
a. Apague o JSON existente.
b. Adicione uma nova instrução (clique em "Adicionar nova instrução").
c. Clique para adicionar uma entidade principal e selecione "IAM Roles".
d. Informe o ARN da role de um colega (ou outra role que não seja a
sua).
e. Salve as alterações.
<img width="553" height="329" alt="image" src="https://github.com/user-attachments/assets/089490c4-9150-4ff7-a0f8-732902fd8b40" />
Passo 13: Reexecutar o Script
1. Executar Script Novamente: No CloudShell, execute o script
credenciais_temporarias.py novamente com os mesmos parâmetros do Passo 4.
a. Resultado: Você receberá um erro de "acesso negado" (ou similar).
<img width="912" height="128" alt="image" src="https://github.com/user-attachments/assets/e38c6760-6ecd-4ce5-a9c8-95371e0076f8" />
b. Explicação: A política de confiança da role foi modificada, e seu
usuário não está mais autorizado a assumir essa role.

## Passo 14: Excluindo Recursos
1. Excluir CloudShell: No CloudShell, clique em "Ações" > "Excluir". Digite
delete e clique em "Excluir".
<img width="585" height="594" alt="image" src="https://github.com/user-attachments/assets/99ca7b7f-2560-490b-91ba-aca06c4064dc" />
2. Excluir Role IAM:
a. No console, vá para IAM > Funções.
b. Pesquise por SeuNomeRole, marque a caixa ao lado e clique em
"Excluir".
c. Digite o nome da role para confirmar e clique em "Excluir".
<img width="907" height="283" alt="image" src="https://github.com/user-attachments/assets/6a024599-9681-4e69-b403-9799472953a9" />

> Escola da Nuvem · Todos os direitos reservados.
> Stay focused !!!













