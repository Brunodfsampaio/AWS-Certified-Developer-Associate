# Laboratório - Jogo de Adivinhação com AWS Lambda, API Gateway e S3
## Objetivos
Ao concluir o lab, você aprenderá o seguinte:
- Desenvolver uma função Lambda em Python para implementar a lógica do jogo.
- Criar uma API RESTful com Amazon API Gateway para expor a função Lambda.
- Publicar um frontend estático no Amazon S3 para interação com o usuário.
- Conectar o frontend à API Gateway, permitindo chamadas à função Lambda.

# 1. Criação de uma função do Lambda
1.1. Acesse o serviço Lambda no console da AWS e clique em "Criar função". Caso já tenha funções Lambda criadas, clique no menu hambúrguer no canto superior esquerdo, selecione "Funções" e, em seguida, clique em "Criar função".

1.2. Na tela de "Criar função", selecione a opção "Criar do zero"

<img width="959" height="265" alt="image" src="https://github.com/user-attachments/assets/3448c541-ea5c-48d9-99e3-c569f78a23db" />

1.3. No campo "Nome da função", digite "LambdaGame-<seu-nome-e-sobrenome>"

1.4. Em "Tempo de execução", selecione "Python 3.9". Em seguinda desça até o final da página, localize e clique em "Criar função”.

1.5. Baixe o arquivo zip “lambda_function”, está disponível no Google Salas de aula. O mesmo contém o código necessário para executar o nosso jogo.

<img width="1227" height="780" alt="image" src="https://github.com/user-attachments/assets/5c142133-9634-48d0-8231-cba6dac82dc5" />
<img width="1263" height="739" alt="image" src="https://github.com/user-attachments/assets/3af6775e-8d84-4824-9f03-13539544a10f" />

- conteúdo do arquivo Lambda_fuction:
```
import random

def lambda_handler(event, context):
    # Número pensado pela máquina
    numero_secreto = random.randint(1, 10)
    
    # Obtendo o palpite
    palpite = int(event['queryStringParameters']['palpite'])
    
    # Verificando o palpite
    if palpite == numero_secreto - 1 or palpite == numero_secreto + 1:
        resposta = f"Quase! O número era {numero_secreto}. Tente novamente!🤓"
    elif palpite != numero_secreto:
        resposta = f"Incorreto, o correto é {numero_secreto}. Vou pensar em outro número.😋 "
    else:
        resposta = f"Parabéns! Você acertou o número {numero_secreto}!😎🤜🤛"

    # Retornando a resposta
    return {
        'statusCode': 200,
        'body': f'{{"resultado": "{resposta}"}}'
    }
```

# 2. Script lambda
2.1. Na seção "Origem do código", no canto direito, clique em "Fazer upload de".

2.2. Será aberta uma opção de seleção. Escolha "Arquivos .zip" e clique em "Fazer
upload".

2.3. Procure no seu computador o script “Terminator” que foi baixado anteriormente.

2.4. Após selecionar o arquivo, clique em "Salvar”.

<img width="1258" height="651" alt="image" src="https://github.com/user-attachments/assets/ce246f33-4b9a-47ff-9d9e-d5a87eb19f8e" />

2.5. Abaixo de "Origem do código", selecione o arquivo "Lambda_function.zip" para
que o código seja exibido.Após verificar o código, clique no botão "Deploy" para
garantir que as alterações foram realizadas com sucesso.

# 3. Criando API comAmazon API Gateway

Após configurar corretamente a função AWS Lambda, o próximo passo é criar uma API no
Amazon API Gateway. Essa API permitirá que a função Lambda seja acionada sempre
que uma requisição for enviada para um endpoint específico

3.1. No canto superior da tela, use a barra de pesquisa para procurar por Amazon API
Gateway e acesse o serviço. No menu à esquerda, clique em “APIs”. Se essa
opção não estiver visível, clique no ícone de menu (☰). procure por “API HTTP” e,
ao lado dessa opção, clique em “Compilar” para começar a configuração da sua
API.

<img width="1266" height="793" alt="image" src="https://github.com/user-attachments/assets/943118f5-603a-40ca-b0a8-2df25a736879" />

3.2. No campo "Nome da API", digite "Api-<seu-nome-e-sobrenome>". Em seguida,
na seção "Integrações", clique no botão "Adicionar integrações" para
configurar a conexão com o Lambda.

3.3. Em "Integrações", será exibido um campo de seleção. Selecione "Lambda" como
o serviço de integração. Após essa escolha, mais opções aparecerão. No campo
"Função do Lambda", selecione a função que você criou anteriormente. Lembre-
se de que, ao criar um recurso na AWS, ele recebe um ARN (Amazon Resource
Name), que é seu identificador único. Caso sua função Lambda não apareça na
lista, você pode estar procurando na região errada. Ao lado do campo "Função do
Lambda", há o campo "Região da AWS", onde você pode selecionar a região
correta em que sua função foi criada. Por fim clique em avançar

<img width="1268" height="788" alt="image" src="https://github.com/user-attachments/assets/c3c28f82-eb5c-4605-878a-b9acc4b30ffd" />

3.4. Agora vamos configurar a rota da API, que funciona como um endereço onde a
API recebe as requisições e as envia para o Lambda.No campo "Método",
escolha "GET". Isso significa que essa rota será usada para buscar informações
do Lambda. No campo "Caminho do recurso", digite "/jogo". Isso define o
endereço da API que será acessado. Por exemplo, se a AWS gerar o link
"https://sua-api.amazonaws.com/jogo", quando alguém acessar esse endereço,
a API chamará sua função Lambda automaticamente. Após selecionar as opções
corretamente, clique no botão "Avançar" para continuar com a configuração da
API.

<img width="1265" height="501" alt="image" src="https://github.com/user-attachments/assets/edacbbfd-3853-4438-a014-35cdc9e50edd" />

3.5. Na próxima etapa, você vai configurar os estágios, que são versões da API. Aqui,
vamos trocar o estágio padrão "$default" para "prod", que representa a versão
oficial da API. No nosso caso, vamos manter a implantação automática ativada,
para que qualquer mudança feita na API seja aplicada imediatamente, sem a
necessidade de um deploy manual. Após essa configuração clique no botão
“Avançar” e em seguida “Criar”.

<img width="1263" height="594" alt="image" src="https://github.com/user-attachments/assets/db5d3041-dda6-4ad8-bc29-4e448782605b" />

3.6. No menu esquerdo, clique em "APIs" para visualizar a API que você acabou de
criar. Clique na sua API para acessar a mesma.

<img width="1264" height="769" alt="image" src="https://github.com/user-attachments/assets/420bfa6e-7025-4d24-9319-d2dfe9d2a75c" />

<img width="1261" height="439" alt="image" src="https://github.com/user-attachments/assets/dd9849df-5709-4210-87bc-3f8ca30c6d26" />

3.7. Quando entrar na API, novas opções aparecerão no menu esquerdo. Clique em
"API: Api-seu nome e sobrenome" e role a página para baixo. Na seção
"Estágios", procure por "Estágios" e logo abaixo você verá o campo "Invocar
URL". Copie esse link e salve no Bloco de Notas, pois ele será usado para que
nosso site se comunique com a API.

<img width="1264" height="802" alt="image" src="https://github.com/user-attachments/assets/7781d11c-95c8-4300-b906-fe6c10c99755" />

3.8. Estamos quase finalizando a configuração da API! O último passo é acessar o
CORS. Para isso, vá até o menu esquerdo e clique em "CORS". No canto superior
direito, clique no botão "Configurar".

<img width="1265" height="778" alt="image" src="https://github.com/user-attachments/assets/dfc5872e-14a8-4368-a757-bb27393a57c8" />

3.8.1. Em "Access-Control-Allow-Origin", digite "*" e clique em "Adicionar".

3.8.2. Em "Access-Control-Allow-Headers", digite "content-type" e clique em
"Adicionar".

3.8.3. Em "Access-Control-Allow-Methods", selecione "GET".

3.8.4. Clique no botão “Salvar”

<img width="967" height="667" alt="image" src="https://github.com/user-attachments/assets/35f64627-d9ff-4b90-a5e7-52afef71440a" />

# Por que essa configuração é importante?

O CORS (Cross-Origin Resource Sharing) é uma configuração essencial para permitir que
aplicações web rodando em um domínio possam fazer requisições para uma API
hospedada em outro domínio. Sem essa configuração, o navegador bloquearia a
comunicação entre o nosso site e a API por questões de segurança.

Campos Configurados:
- Access-Control-Allow-Origin: "*" → Permite que qualquer domínio acesse a API. Isso é útil quando queremos que nossa API possa ser usada por diferentes sites
- Access-Control-Allow-Headers: "content-type" → Permite que a API aceite requisições que contenham o cabeçalho Content-Type, necessário para enviar e receber dados no formato correto.
- Access-Control-Allow-Methods: "GET" → Define que nossa API aceita requisições do tipo GET, que é o método utilizado para buscar informações.

Por fim como configuramos a implantação automática, essa configuração do CORS será
aplicada imediatamente, sem a necessidade de fazer um deploy manual. Isso garante que
nosso site já possa se comunicar corretamente com a API sem bloqueios.

# 4. Website estático S3

4.1. Vamos configurar nosso site utilizando o Amazon S3. Antes disso, baixe o arquivo
"index.html", que contém a página inicial do nosso site. Procure o arquivo
"index.html" no seu computador, clique com o botão direito sobre ele e selecione
"Abrir com" > "Bloco de Notas". Isso permitirá visualizar e editar o código da
página inicial do site.

- conteúdo do index.html
```
<html lang="pt">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Jogo de Adivinhação</title>
    <style>
      body {
        font-family: 'Arial', sans-serif;
        background: linear-gradient(to right, #4facfe, #00f2fe);
        margin: 0;
        padding: 0;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
      }
      .container {
        text-align: center;
        background: rgba(0, 0, 0, 0.7);
        border-radius: 10px;
        padding: 40px;
        box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        color: #fff;
        width: 100%;
        max-width: 400px;
      }
      h2 {
        font-size: 2em;
        margin-bottom: 20px;
        color: #ffdd57;
      }
      input[type="number"] {
        padding: 10px;
        font-size: 1.2em;
        width: 80%;
        margin-bottom: 20px;
        border: none;
        border-radius: 5px;
        text-align: center;
      }
      button {
        background-color: #ff6f61;
        color: white;
        padding: 15px 30px;
        font-size: 1.2em;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s ease;
      }
      button:hover {
        background-color: #ff8c7b;
      }
      #resultado {
        margin-top: 20px;
        font-size: 1.5em;
        font-weight: bold;
        color: #ffdd57;
      }
      footer {
        margin-top: 30px;
        font-size: 0.9em;
        color: #ddd;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h2>Jogo de Adivinhação</h2>
      <p>Eu pensei em um número de 1 a 10. Tente adivinhar!</p>
      <input type="number" id="palpite" min="1" max="10" />
      <button onclick="enviarPalpite()">Enviar</button>
      <div id="resultado"></div>
      <footer>
        <p>Escola da Nuvem💙</p>
      </footer>
    </div>

    <script>
      function enviarPalpite() {
        var palpite = document.getElementById("palpite").value;
        var url =
          "<Invocar URL>/<Caminho do recurso>?palpite=" +
          palpite;

        fetch(url)
          .then((response) => response.json())
          .then((data) => {
            document.getElementById("resultado").innerText =
              "Resultado: " + data.resultado;
          })
          .catch((error) => {
            document.getElementById("resultado").innerText =
              "Erro ao comunicar com a API.";
          });
      }
    </script>
  </body>
</html>
```
4.2. No código do arquivo "index.html", procure por "Invocar URL" e substitua pelo
link que você já copiou anteriormente do API Gateway e salvou no Bloco de
Notas. Em seguida, localize "Caminho do recurso" e substitua por "jogo". Isso
formará o link completo para que o site consiga chamar corretamente a API.

<img width="904" height="626" alt="image" src="https://github.com/user-attachments/assets/31cdd24b-1b15-4f6d-a3f2-2ff2b56a5919" />

<img width="1276" height="763" alt="image" src="https://github.com/user-attachments/assets/9af7f0e2-e5e2-400c-ae36-d7677bc27c93" />

4.3. Procure no código pelo texto "Escola da Nuvem💙" e, logo após ele, adicione o
seu nome completo. Exemplo: Escola da Nuvem💙 - Mario da Silva. Depois salve
o arquivo.

4.4. Acesse o serviço Amazon S3. No menu esquerdo, clique em "Buckets de uso
geral" e, em seguida, clique no botão "Criar bucket" para iniciar a configuração.

4.5. No campo "Nome do bucket", digite "s3-website-<seu-nome-e-sobrenome>.
Lembre-se de que o nome do bucket deve ser único globalmente, ou seja,
nenhum outro usuário da AWS pode ter um bucket com o mesmo nome. Se o
nome escolhido já estiver em uso, tente variações, como adicionar números ou
outras identificações.

4.6. Role para baixo e clique em “Criar bucket”.

<img width="1260" height="647" alt="image" src="https://github.com/user-attachments/assets/1a1754b4-55a4-4eb8-b519-a9db52da769f" />

4.7. Com o bucket criado, clique no nome " s3-website-<seu nome e sobrenome ".

4.8. Faça o upload do arquivo "index.html" para o bucket. No canto superior direito,
clique em "Carregar", depois em "Adicionar arquivos", selecione o arquivo
"index.html", clique em "Abrir", role para baixo e clique em "Carregar" e no canto
superior em “fechar” após o uploud bem-sucedido.

<img width="1246" height="799" alt="image" src="https://github.com/user-attachments/assets/645e7a52-8ab2-4d16-97ab-a70c61d89196" />

<img width="1273" height="505" alt="image" src="https://github.com/user-attachments/assets/abb98e91-b5d2-4436-b9a2-7bdd6eb56749" />

4.9. Na última etapa, vamos habilitar o website estático no S3. No menu superior,
acesse "Propriedades".

<img width="1267" height="808" alt="image" src="https://github.com/user-attachments/assets/012a1d6f-bdd7-4bcb-9064-fe9897d4db32" />

4.9.1. Role até o final da página, encontre a seção "Hospedagem de site
estático" e, ao lado, clique no botão "Editar".

4.9.2. Em "Hospedagem de site estático", selecione "Ativar".No campo
"Documento de índice", digite "index.html" (o nome deve ser exatamente
igual ao arquivo que você fez upload). Por fim desça até o fim da página e
clique em “Salvar alterações”.

<img width="1258" height="748" alt="image" src="https://github.com/user-attachments/assets/4a015980-b1a9-43f2-bc26-716e5fe89d85" />

4.9.3. Após ativar a hospedagem de site estático, você será redirecionado para o
início do menu "Propriedades". Role até o final da página, copie o link do
campo "Endpoint de site de bucket" e cole em uma nova aba do navegador
para acessar o site.
Você enfrentará o erro 403 Forbidden ao acessar o site. Isso acontece porque, por
padrão, o bucket e seus arquivos são privados. Para corrigir isso, precisamos liberar o
acesso público.

<img width="1264" height="451" alt="image" src="https://github.com/user-attachments/assets/600f8f16-6560-496e-9f8e-d4637a06a8bb" />

<img width="949" height="229" alt="image" src="https://github.com/user-attachments/assets/300aeff4-2fb4-47cd-880d-ee36dbb4c670" />

Na próxima etapa, vamos configurar as permissões corretamente para que o site fique
acessível.

# 5. Permissão bucket S3

5.1. Para liberar o acesso público ao bucket, no menu superior clique em
"Permissões". Em seguida, localize a seção "Bloquear acesso público
(configurações do bucket)" e clique no botão "Editar" ao lado.

<img width="1234" height="768" alt="image" src="https://github.com/user-attachments/assets/384a42dc-a6e7-4e53-b39c-ee68bf575300" />

5.2. Ao desmarcar a opção "Bloquear todo o acesso público", você permite que o
bucket seja acessado publicamente, o que é necessário para hospedar um site
estático no S3.No entanto, ao manter marcadas as opções "Bloquear acesso
público a buckets e objetos concedidos por meio de novas listas de controle
de acesso (ACLs)" e "Bloquear acesso público a buckets e objetos
concedidos por meio de qualquer lista de controle de acesso (ACLs)", você
impede que permissões de acesso sejam concedidas via ACLs, garantindo que o
controle de acesso seja feito exclusivamente por meio de políticas de bucket.
Clique em "Salvar alterações" e, quando aparecer a caixa de confirmação, clique
em "Confirmar" para aplicar as novas configurações de acesso público ao bucket.

<img width="1272" height="716" alt="image" src="https://github.com/user-attachments/assets/c91d4b6c-5bd3-4a33-9dc5-1d26fd3cbbf1" />

Se as opções "Bloquear acesso público a buckets e objetos concedidos por meio de
novas políticas de ponto de acesso e bucket público" e "Bloquear acesso público e entre contas a buckets e objetos por meio de qualquer política de bucket ou ponto
de acesso público" estiverem marcadas, o bucket não aceitará políticas públicas, o que
impediria a configuração da política necessária na próxima etapa. Por isso, precisamos
desmarcar essas opções, garantindo que possamos adicionar uma política que permita o
acesso público ao index.html. Isso é essencial para que qualquer usuário possa acessar o
site hospedado no S3.

5.3. Ainda no menu "Permissões", desça até a seção "Política do bucket" e clique
em "Editar" para modificar as permissões de acesso ao bucket.

<img width="1235" height="701" alt="image" src="https://github.com/user-attachments/assets/29dde9d8-0a21-4f8c-9886-ef7d1091497d" />

5.4. Copie o valor do ARN do bucket, pois vamos utilizá-lo na política de acesso. O
ARN (Amazon Resource Name) é um identificador único da AWS que
especifica recursos dentro do ambiente da AWS. No caso do bucket S3, ele
segue o formato: arn:aws:s3:::nome-do-seu-bucket. Esse identificador será
usado na política do bucket para definir quem pode acessar o conteúdo e quais
permissões serão concedidas.

<img width="1271" height="589" alt="image" src="https://github.com/user-attachments/assets/7ceec54b-fa91-4bec-8d5d-a6d5a4a68562" />

5.5. No canto direito, clique em "Gerador de políticas" para criar a política de acesso
ao bucket de forma mais fácil e sem necessidade de escrever o código
manualmente. Uma nova guia será aberta. Certifique-se de desativar a tradução
automática do navegador, pois a página deve estar em inglês. Caso contrário, isso
pode impactar a geração correta da política.

5.5.1. Em "Select Type of Policy", selecione "S3 Bucket Policy". Isso define
que a política será aplicada a um bucket do Amazon S3, permitindo
configurar permissões específicas para acesso aos arquivos armazenados
nele.

5.5.2. No campo "Effect", deixe selecionado "Allow". Isso significa que a política
irá permitir (em vez de negar) as ações que você vai definir, como visualizar
ou acessar os arquivos do bucket. É essencial para tornar seu site acessível
ao público.

5.5.3. No campo "Principal", digite um asterisco: “*”. Isso significa que você está
permitindo o acesso a qualquer pessoa (público geral). É necessário para que
qualquer usuário na internet possa acessar seu site estático hospedado no
S3.

5.5.4. No campo "Actions", selecione “GetObject”. Essa ação permite que
qualquer pessoa acesse e visualize os arquivos do bucket, como o
index.html. É essencial para que os visitantes consigam carregar seu site no
navegador.

5.5.5. No campo "Amazon Resource Name (ARN)", cole o ARN do seu bucket,
que você copiou anteriormente. Depois, adicione “/*” ao final do ARN. Esse /*
indica que a permissão será aplicada a todos os arquivos dentro do bucket, e
não apenas ao bucket em si. Isso garante que qualquer pessoa possa
acessar todos os arquivos públicos, como o index.html. Exemplo:
arn:aws:s3:::S3-Website-seunome-sobrenome/*

<img width="1265" height="822" alt="image" src="https://github.com/user-attachments/assets/f048c5f5-af7c-41aa-ad00-dcc12d363a8e" />

<img width="1263" height="752" alt="image" src="https://github.com/user-attachments/assets/03eb1a2b-029e-4ab9-8ab6-12575b42096d" />

<img width="824" height="626" alt="image" src="https://github.com/user-attachments/assets/b110ddcb-6a6f-442b-95bf-36bd4b0d93d3" />

5.5.6. Clique em "Add Statement" para adicionar as configurações definidas, e
em seguida clique em "Generate Policy" para gerar automaticamente o
código da política com base nas opções que você preencheu.

5.5.7. Copie o JSON gerado pelo Gerador de Políticas. Volte para a aba do seu
bucket S3, na seção "Política do bucket", e cole o conteúdo no campo
disponível. Isso aplicará a política de acesso público ao seu bucket,
permitindo que o site seja acessado via navegador. Antes de clique em arn:aws:s3:::s3-website-brunosampaio
“Salvar alterações”, verifique se não há espaços em branco no início ou no
final do JSON. O formato JSON é sensível: espaços extras, falta de colchetes
{}, ou aspas mal colocadas podem causar erro na hora de aplicar a política.
Certifique-se de que o conteúdo está correto e limpo para evitar falhas.

<img width="1261" height="773" alt="image" src="https://github.com/user-attachments/assets/00a8a36e-9bf6-445c-acef-5da6f3bd52e3" />

<img width="1274" height="829" alt="image" src="https://github.com/user-attachments/assets/c6fdc0a3-efd6-4b9a-8b43-5601b457d07e" />

5.5.8. Por fim, atualize a página no navegador — seu site estará disponível!
Nesse jogo, o usuário digita um número de 1 a 10 e clica no botão "Enviar".
O site, hospedado no S3, envia a requisição para o API Gateway, que por
sua vez aciona a função Lambda. A função Lambda processa o número e
retorna a resposta dizendo se o usuário acertou ou não. Tudo isso acontece
de forma integrada entre S3, API Gateway e Lambda!

<img width="1157" height="865" alt="image" src="https://github.com/user-attachments/assets/4a91a325-0d07-44c8-9a45-c5abe661a3a1" />

<img width="848" height="681" alt="image" src="https://github.com/user-attachments/assets/d53c8bbe-0ad4-4c7d-b6b0-f6530904538c" />

# 6. Deleção do recursos

6.1. Para deletar os recursos, acesse o S3, no menu esquerdo clique em "Buckets de
uso geral", selecione o seu bucket e clique no botão "Vazio" que fica no canto
superior direito. A AWS só permite apagar um bucket se ele estiver vazio, por isso
é necessário remover os arquivos antes. Na caixa de confirmação, digite
exatamente: excluir permanentemente. Após isso, o botão "Vazio" ficará
disponível para apagar todos os arquivos do bucket. Depois de esvaziar, você
poderá excluir o bucket normalmente.


6.1.1. O botão "Vazio" apaga todos os dados dentro do bucket de uma vez. Caso
você queira apagar arquivos específicos, basta entrar no bucket, selecionar
o(s) arquivo(s) desejado(s) e, no canto superior direito, clicar no botão
"Excluir". Assim, você remove apenas os itens selecionados sem apagar o
bucket inteiro.

6.2. Para apagar o bucket, selecione-o na lista, clique em "Excluir" no canto superior
direito. Na caixa de confirmação que será exibida, digite exatamente o nome do
seu bucket (ex: S3-Website-seu nome e sobrenome) para confirmar a exclusão.
Depois disso, clique em "Excluir bucket" para finalizar o processo.

6.3. Acesse o API Gateway, no menu esquerdo clique em "APIs", localize e selecione
a API com seu nome (por exemplo: "API-seu nome e sobrenome"), e depois
clique no botão "Excluir" no canto superior direito. Confirme a exclusão na caixa
que será exibida. Isso removerá a API e liberará os recursos associados.

6.4. Por fim, acesse a página do Lambda. No painel esquerdo, clique em "Funções",
localize e selecione a sua função Lambda. No canto superior direito, clique em
"Ações" e, em seguida, em "Excluir". Confirme a exclusão quando solicitado —
isso encerrará completamente o uso dos recursos criados durante o laboratório.

6.5. Pronto! Laboratório concluído com sucesso!


> stay focused!
