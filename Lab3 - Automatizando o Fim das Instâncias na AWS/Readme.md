<img width="1263" height="843" alt="image" src="https://github.com/user-attachments/assets/c4db700f-2a44-4b2c-a4ed-7892e033c485" />### Resumo do laboratório
Neste laboratório, você aprenderá a criar e gerenciar políticas e funções no AWS, focando
em automação e integração entre serviços. A seguir, você passará por uma série de
etapas para criar uma política de IAM, que irá permitir terminar instâncias EC2. Em
seguida, você criará uma função Lambda utilizando Python, associará essa função à
política IAM e configurará os gatilhos para que ela seja executada automaticamente
através do EventBridge (anteriormente CloudWatch Logs).

### Objetivos
Ao concluir o lab, você aprenderá o seguinte:
- Criar uma política IAM para permitir a finalização de instâncias EC2
- Criar e configurar uma função Lambda para automatizar o encerramento de instâncias.
- Associar a função IAM à função Lambda, garantindo permissões adequadas.
- Implementar um gatilho no EventBridge para acionar a função Lambda automaticamente.
- Ajustar o tempo de execução da função, garantindo seu funcionamento adequado.

### 1. Cria política IAM
1.1 Acesse o IAM no console da AWS. No canto superior esquerdo, clique no menu e, no painel lateral, clique "Políticas".

<img width="278" height="435" alt="image" src="https://github.com/user-attachments/assets/83680c14-867d-442b-981f-4f7e071e37ba" />

1.2. No canto direito, clique em "Criar política".
1.3. No canto superior esquerdo, ao lado de "Editor de Políticas", selecione a opção "JSON".

<img width="1281" height="827" alt="image" src="https://github.com/user-attachments/assets/fcaab520-7373-4175-8a9f-624acc830f35" />

1.4. Na mesma atividade em que você acessou, procure e baixe o arquivo "Política IAM”. Abra o arquivo com o Bloco de Notas. Copie a política e cole no "Editor de
Políticas” e clique em “Próximo”.

Conteúdo do arquivo:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogStream",
        "logs:CreateLogGroup",
        "logs:PutLogEvents",
        "ec2:DescribeInstances",
        "ec2:TerminateInstances",
        "ec2:DescribeRegions"
      ],
      "Resource": "*"
    }
  ]
}
```

<img width="1265" height="835" alt="image" src="https://github.com/user-attachments/assets/b77a396d-c6fd-4892-b69a-ef638d20086a" />

O objetivo dessa política é conceder permissões para a função Lambda gerenciar instâncias
EC2. Ela permite que a função Lambda realize ações específicas, como descrever e
terminar instâncias EC2, além de criar e manipular logs no CloudWatch.
Com essa política, a função Lambda será capaz de automatizar a terminação de instâncias
EC2 em diversas regiões da AWS de forma segura e controlada, de acordo com as
permissões atribuídas.

1.5. No campo "Nome da política", digite "PoliticaTerminarEC2-<seu nome e sobrenome>

<img width="1277" height="834" alt="image" src="https://github.com/user-attachments/assets/6ad7386d-a982-4496-b73e-c6fa21725b60" />
<img width="1271" height="817" alt="image" src="https://github.com/user-attachments/assets/47fa518a-ddae-4fe6-9ad8-95f29da12d2d" />

### 2. Criar role/Funções

2.1. No console do IAM, no menu lateral, selecione "Funções" e clique em "Criar
perfil". A criação de uma função (role) no IAM é necessária para associar
permissões específicas a uma entidade, como uma função Lambda ou EC2. A
função define o que a entidade pode fazer dentro da AWS, garantindo que os
serviços ou recursos possam acessar apenas os recursos necessários de forma
controlada e segura.

<img width="1274" height="762" alt="image" src="https://github.com/user-attachments/assets/75b4eb7b-5b60-4608-ac3a-992defae7c51" />

2.2. No campo "Tipo de entidade confiável", selecione "Serviço da AWS" e, em
seguida, escolha "Lambda" no campo "Serviço ou caso de uso" e por fim clique
em “Próximo”. Ao selecionar "Serviço da AWS" e "Lambda", você está permitindo que a função Lambda assuma essa role para executar tarefas automatizadas,
como terminar instâncias EC2.

<img width="1265" height="801" alt="image" src="https://github.com/user-attachments/assets/42a6ca97-9489-4dea-b4a0-080bc9fed000" />

2.3. Na próxima etapa, pesquise pela política "PoliticaTerminarEC2-<seu nome e sobrenome>" que você criou na etapa 1.

2.4. Selecione a política e clique em "Próximo". Ao anexar a política ao role, você está
garantindo que a função Lambda tenha as permissões necessárias para terminar
instâncias EC2 e interagir com outros serviços, conforme definido na política
criada.

<img width="1268" height="665" alt="image" src="https://github.com/user-attachments/assets/f7330e0f-7caa-4124-831c-badd19bc8e04" />

2.5. Por último, no campo "Nome da função", digite "RoleTerminarEC2-<seu nome e sobrenome>"

2.6. Role para baixo e clique em "Criar perfil" para finalizar a criação do role.

<img width="1273" height="821" alt="image" src="https://github.com/user-attachments/assets/6d3da0ab-a3d3-4b8d-87fe-c92b6669115d" />
<img width="1271" height="810" alt="image" src="https://github.com/user-attachments/assets/fe9d507e-a10e-4d18-b3c2-82269fb65e4d" />

### 3. Criação de uma função do Lambda

3.1. Acesse o serviço Lambda no console da AWS e clique em "Criar função". Caso
já tenha funções Lambda criadas, clique no menu hambúrguer no canto superior
esquerdo, selecione "Funções" e, em seguida, clique em "Criar função".

<img width="1272" height="651" alt="image" src="https://github.com/user-attachments/assets/fb96c92e-0603-4a77-9498-fc8a04a33c7b" />

3.2. Na tela de "Criar função", selecione a opção "Criar do zero"

3.3. No campo "Nome da função", digite "LambdaTerminarEC2-<seu nome e sobrenome>"

3.4. Em "Tempo de execução", selecione "Python 3.9"

3.5. Em seguida, expanda a opção "Alterar a função de execução padrão",
selecione "Usar uma função existente" e, no campo exibido “Função existente”,
escolha a função "RoleTerminarEC2-<seu nome e sobrenome>" que foi criada
anteriormente. Essas etapas garantem que a função Lambda seja criada com o
nome adequado e as permissões corretas para interagir com os recursos da AWS.

<img width="1263" height="843" alt="image" src="https://github.com/user-attachments/assets/bd791630-ffa9-4ea5-b670-14e409314390" />

<img width="1249" height="761" alt="image" src="https://github.com/user-attachments/assets/5a006ac3-9d2a-4ce7-8511-ac0b0fa03704" />

<img width="1274" height="840" alt="image" src="https://github.com/user-attachments/assets/376bda97-4ff3-4a55-b116-ac920db14ea5" />

3.6. Baixe o script “Terminator”, está disponível no Google Salas de aula. O mesmo
contém o código necessário para deletar as instâncias EC2.

Link para download do Terminator: https://drive.google.com/file/d/1en7YJ2KwQL59cBMEHl0_nWWf74i4rsUB/view?usp=sharing

### 4. Script lambda

4.1. Na seção "Origem do código", no canto direito, clique em "Fazer upload de".

<img width="1263" height="673" alt="image" src="https://github.com/user-attachments/assets/7b779ddc-8d36-4143-b78a-b1b5a7ff7a29" />

4.2. Será aberta uma opção de seleção. Escolha "Arquivos .zip" e clique em "Fazer upload".

<img width="1262" height="731" alt="image" src="https://github.com/user-attachments/assets/949b22f6-dc6e-436a-8705-6f2631dbf590" />

4.3. Procure no seu computador o script “Terminator” que foi baixado anteriormente.

4.4. Após selecionar o arquivo, clique em "Salvar”.

4.5. Abaixo de "Origem do código", selecione o arquivo "Terminator.py" para que o
código seja exibido.Após verificar o código, clique no botão "Deploy" para garantir
que as alterações foram realizadas com sucesso.

<img width="1049" height="647" alt="image" src="https://github.com/user-attachments/assets/d2b32f3c-7003-4df9-a070-be711656b2cb" />

Conteúdo do Terminator:
```
import boto3

def terminator():
    # Apenas regiões dos EUA e América do Sul
    ec2_client = boto3.client('ec2')
    regions = ['us-east-1', 'us-east-2', 'us-west-1', 'us-west-2', 'sa-east-1']

    # Realiza um loop nas regiões da variável acima
    for region in regions:
        print(f"Terminating instances in {region}")

        # Cria uma variável para cada região
        ec2 = boto3.resource('ec2', region_name=region)

        # Com a variável anterior, filtra as instâncias que estão RUNNING, STOPPING, STOPPED
        instances = ec2.instances.filter(Filters=[{'Name': 'instance-state-name', 'Values': ['running', 'stopped', 'stopping']}])

        # Termina as instâncias
        for instance in instances:
            try:
                instance.terminate()
                print(f'{instance.id} terminated')
            except:
                print(f'Erro ao terminar {instance.id}')

def lambda_handler(event, context):
    terminator()
```

4.6. Acima da seção "Origem do código", há um menu de opções. Selecione
"Configuração". Abaixo, um menu inferior será exibido. Clique em "Configuração
geral" e, ao lado direito, clique em "Editar" para modificar as configurações da
função Lambda.

<img width="1264" height="554" alt="image" src="https://github.com/user-attachments/assets/9273755a-94bd-4304-b725-dc057ede935e" />

4.7. No final das configurações, localize o campo "Tempo limite" e altere o valor de 3
segundos para 10 segundos. O tempo limite define quanto tempo a função
Lambda pode executar antes de ser interrompida. Aumentar o tempo limite para
10 segundos é necessário porque a função que você está utilizando pode precisar
de mais tempo para terminar o processo de terminação das instâncias EC2,
especialmente se houver muitas instâncias ou se a resposta da AWS demorar um
pouco mais. Com um tempo maior, a função terá mais tempo para concluir a
execução sem ser interrompida prematuramente.

<img width="1272" height="804" alt="image" src="https://github.com/user-attachments/assets/93508e08-d655-4a1c-9350-1219aff68f10" />

4.8. Clique em "Salvar" para que as alterações sejam aplicadas com sucesso.

4.9. No console da função Lambda, vá até a aba “Código” e verifique que o nome do
arquivo é Terminator.py. Role para baixo até a seção “Configurações de tempo
de execução”, clique em “Editar” e, no campo “Manipulador”, modifique para
“Terminator.lambda_handler”, usando o nome do arquivo sem o .py. Depois,
clique em “Salvar”.

<img width="1272" height="682" alt="image" src="https://github.com/user-attachments/assets/a2c30780-ac2a-4944-b8ed-820d8c9edbbc" />

<img width="1275" height="706" alt="image" src="https://github.com/user-attachments/assets/6e9c5943-de47-4730-9f64-6839abf60494" />

Para que a função Lambda seja executada corretamente, é necessário configurar o campo
Manipulador (Handler) de acordo com o nome do arquivo principal e da função que será
executada. O manipulador informa ao AWS Lambda qual arquivo e qual função deve ser
usado como ponto de entrada da execução. Essa configuração é essencial para que o Lambda
consiga localizar e executar corretamente o código. Caso o manipulador esteja incorreto, a
execução resultará em erro.

### 5. Agendamento com EventBridge

Com o Lambda configurado corretamente, será necessário criar um gatilho para acionar a
função. Vamos utilizar o Amazon EventBridge para agendar a ativação do Lambda,
garantindo que ele seja executado automaticamente em intervalos definidos

5.1. No canto superior da página da função Lambda, clique em "Adicionar gatilho" para configurar o evento que acionará a função.

<img width="1265" height="686" alt="image" src="https://github.com/user-attachments/assets/6e219046-7c59-4eae-8155-49e52f3be705" />

5.2. Na seção "Configuração do gatilho", pesquise por EventBridge e selecione-o.
Em seguida, na seção "Regra", clique em "Criar uma regra" para configurar o
agendamento

5.3. No campo "Nome da regra", insira o nome "GatilhoTerminarEC2-<seu nome e
sobrenome>" para identificar de forma única a regra criada.

5.4. Em "Tipo de regra", marque a opção "Expressão de agendamento". Isso abrirá
o campo correspondente, onde você pode criar expressões Cron ou Rate para
agendar a ativação da função Lambda. Expressão Cron: Permite definir um
agendamento mais complexo, como uma execução em horários específicos. Já
Rate define um intervalo fixo de tempo para a execução da função, como a cada 5
minutos, uma vez por dia.

5.5. No campo " Expressão de programação", você pode definir quando a função
Lambda será acionada:

5.5.1. Para agendar a execução a cada 12 horas, utilize a expressão rate(12
hours)

5.5.2. Se preferir agendar a execução a cada 5 minutos, utilize a expressão rate(5
minutes)

<img width="1271" height="781" alt="image" src="https://github.com/user-attachments/assets/bec84ac5-2fcb-4bf5-af59-e2c9753a775a" />

5.6. Por fim, clique em "Adicionar" para concluir a configuração do gatilho e garantir
que a função Lambda seja acionada conforme o agendamento definido.

5.7. Caso você tenha escolhido o agendamento de 5 minutos para verificar a função
em ação, basta criar uma instância EC2 e aguardar a execução da função
Lambda. A função será acionada automaticamente após o intervalo de tempo
configurado e realizará a ação de terminação da instância EC2 conforme o
esperado


### Testando as configurações com novas instâncias

- vamos criar novas instâncias no EC2 e realizar os testes!!!

<img width="1035" height="275" alt="image" src="https://github.com/user-attachments/assets/f8ef04f5-9c8f-414e-b312-8244dc9683b9" />

<img width="1037" height="309" alt="image" src="https://github.com/user-attachments/assets/3c62a12a-4320-45f6-a714-f39a9e5058ff" />

<img width="1041" height="325" alt="image" src="https://github.com/user-attachments/assets/1d1c8c27-575d-4958-bef9-9e38c2f0feaf" />

<img width="1034" height="348" alt="image" src="https://github.com/user-attachments/assets/75d3b89a-6c0f-4bee-9ec7-6dc3028a905a" />

### 6. Exclusão dos recursos

6.1. Para deletar a função Lambda, acesse o menu hambúrguer no canto superior
esquerdo do console da AWS, clique em "Funções" na seção Lambda, selecione
a função desejada, clique em "Ações" no topo da página e, no menu suspenso,escolha "Excluir". Em seguida, confirme a exclusão quando solicitado para
remover a função Lambda permanentemente.

6.2. Acesse o painel do IAM, no menu à esquerda, procure por "Funções", pesquise
pela função “RoleTerminarEC2-<seu nome e sobrenome>”, selecione-a e, no
canto superior, clique no botão "Excluir". Em seguida, confirme a exclusão
quando solicitado para remover a role permanentemente.

6.3. Agora, acesse "Políticas" no menu à esquerda, pesquise pela política
"PoliticaTerminarEC2-<seu nome e sobrenome>", selecione-a e clique no
botão "Excluir". Quando aparecer a janela de confirmação, siga os passos para
concluir a remoção.

6.4. Por fim, pesquise por "Amazon EventBridge" na barra de pesquisa, no menu à
esquerda, na seção "Barramentos", clique em "Regras", selecione a sua regra e
clique em "Excluir" para removê-la permanentemente.

6.5. No canto superior direito do console de gerenciamento da AWS, clique em cima
do seu nome de usuário que está em azul, depois clique em “Sair” que aparece
mais abaixo.

6.6. Pronto! Laboratório concluído com sucesso!

> stay focused!









