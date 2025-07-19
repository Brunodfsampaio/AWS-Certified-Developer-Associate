
#### Observação: 
A interface do Console de Gerenciamento da AWS pode sofrer pequenas
alterações visuais ao longo do tempo, mas os conceitos e a localização geral dos
serviços Lambda e API Gateway permanecem consistentes. As instruções neste
resumo seguem a estrutura geral das funcionalidades.
Este laboratório demonstra uma prática essencial para gerenciar múltiplos ambientes
(como desenvolvimento e produção) de forma organizada e segura em aplicações
serverless, utilizando as funcionalidades de versionamento e direcionamento do
Lambda e API Gateway.

## Objetivos
Este laboratório ensina como:
- Criar uma função AWS Lambda compatível com a integração Proxy do API Gateway.
- Publicar diferentes versões da função Lambda para representar estados distintos do código.
- Criar Aliases no Lambda (ex: dev, prod) que funcionam como ponteiros para versões específicas da função.
- Criar uma API REST no API Gateway.
- Configurar a integração do tipo Proxy entre a API Gateway e a função Lambda.
- Criar Stages no API Gateway (ex: Desenvolvimento, Producao) para representar os diferentes ambientes de implantação da API.
- Integrar cada Stage da API Gateway com o Alias correspondente da função Lambda, garantindo que cada ambiente da API invoque a versão correta da função.
- Testar os endpoints de cada Stage para verificar o correto direcionamento das chamadas.
- Compreender os benefícios do uso combinado de Aliases (Lambda) e Stages (API Gateway) para o gerenciamento do ciclo de vida e implantação controlada de aplicações serverless.

## Cenário:  
Você está desenvolvendo ou mantendo uma API serverless que serve como backend para uma aplicação. É crucial poder implantar e testar novas funcionalidades ou correções em um ambiente isolado (desenvolvimento) antes de liberá-las para os
usuários finais (produção). Você precisa de um mecanismo que permita ter endpoints
de API estáveis para cada ambiente (/dev/recurso e /prod/recurso, por exemplo), onde
cada um invoque a versão apropriada do código da sua função Lambda, sem a
necessidade de criar APIs ou funções separadas para cada ambiente. Este laboratório
implementa exatamente essa solução usando Aliases do Lambda e Stages do API
Gateway.

# Parte 1: Criação da Função AWS Lambda
1. Acessar Console Lambda: Faça login no Console AWS e navegue até o serviço
Lambda.
2. Criar Função Lambda:
3. Clique em Criar função.
4. Criar do zero: Selecione essa opção.
5. Nome da função: Insira um nome descritivo, ex: minha-funcao-proxy-lab- seunomesobrenome.
6. Runtime (Tempo de execução): Selecione Python 3.9.
7. Arquitetura: x86_64.
8. Alterar a função de execução padrão:
9. de execução: Selecione "Criar uma nova função com permissões básicas do Lambda".
10.Clique em Criar função.

<img width="1266" height="824" alt="image" src="https://github.com/user-attachments/assets/92fc43a9-2d11-4bd1-84df-0c4257622dbc" />
<img width="1244" height="785" alt="image" src="https://github.com/user-attachments/assets/3c7eec80-933c-4f9b-8d5e-8b6d8a5e789a" />

## Código da Função Lambda (versão desenvolvimento):
11. Na página da função Lambda recém-criada, role para baixo até a seção
Código (Origem do código).
12. Substitua o código padrão no editor (lambda_function.py) pelo: Código: Cód. py do ambiente de DESENVOLVIMENTO!
```
import json
import os

def lambda_handler(event, context):
    try:
        stage = event['requestContext']['stage']
    except KeyError:
        stage = 'desconhecido'

    response_body = {
        'message': f'Ola do ambiente {stage}!',
        'functionVersion': context.function_version,
        'functionAlias': (
            context.invoked_function_arn.split(':')[-1]
            if ':' in context.invoked_function_arn else '$LATEST'
        )
    }

    return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'
        },
        'body': json.dumps(response_body, ensure_ascii=False)
    }
```
13.Clique em Deploy.
<img width="1275" height="800" alt="image" src="https://github.com/user-attachments/assets/e5fd9364-115f-4fb6-828c-490e5be721df" />

## Testar a Função Lambda (versão $LATEST):
14. Clique na aba Testar (ao lado da aba Código).
15. Em Evento de teste, selecione Criar novo evento.
16. Nome do evento: teste-simulado.
17. Modelo: Selecione API Gateway AWS Proxy.
18. No JSON do evento, localize a chave requestContext e dentro dela, a chave
stage (você a localizará na linha 100).
19. Altere o valor para "test-stage":
20.Clique em Salvar e depois em Testar.
21. Verifique o resultado em Resultado da execução -> Detalhes.

> A imagem do seu documento mostra a execução bem-sucedida

<img width="1270" height="810" alt="image" src="https://github.com/user-attachments/assets/886cf3ca-9d82-4017-9f86-9c1fa54ccd57" />
<img width="1051" height="675" alt="image" src="https://github.com/user-attachments/assets/1a153373-cfdf-48a2-8df3-4d2d7226c92c" />
<img width="1263" height="615" alt="image" src="https://github.com/user-attachments/assets/fabddca3-e580-4b02-84a1-107725f7be4b" />
<img width="1253" height="832" alt="image" src="https://github.com/user-attachments/assets/2c02e36a-6335-4bb1-9928-9820460a5ea6" />
<img width="1260" height="762" alt="image" src="https://github.com/user-attachments/assets/431e24bd-3e05-4f0a-bb54-86983dfe457a" />

# Parte 2: Publicação de Versões e Criação de Aliases
1. Publicar a Versão 1 da Função Lambda:
2. Na página da sua função Lambda, vá para a aba Versões.
3. Clique em Publicar nova versão.
4. Descrição da versão (opcional): Versão inicial da função de desenvolvimento.
5. Clique em Publicação ou Publicar.

<img width="1269" height="818" alt="image" src="https://github.com/user-attachments/assets/66d2f3e4-cf73-4614-b8ba-df9f5d686df0" />
<img width="1259" height="721" alt="image" src="https://github.com/user-attachments/assets/181f5350-2971-4c64-9ee5-daa90c9a90a8" />
<img width="1277" height="823" alt="image" src="https://github.com/user-attachments/assets/ed403b5c-c826-4b45-b62f-62d1029d41b3" />

## Criar Alias "dev":
6. Volte para a sua Função lambda “minha-funcao-proxy-lab-seunomesobrenome”
7. Vá para a aba Aliases.
8. Clique em Criar alias.
9. Nome do alias: dev.
10. Descrição do alias (opcional): Alias para ambiente de desenvolvimento.
11.Versão: Selecione 1.
12. Clique em Salvar.
13.Copie o ARN e guarde-o no bloco de notas, você vai precisar posteriormente.
<img width="1267" height="780" alt="image" src="https://github.com/user-attachments/assets/275e4bd0-77e0-4e58-a9cf-4efd1eb4f285" />
<img width="1270" height="673" alt="image" src="https://github.com/user-attachments/assets/6a149c5b-9fb7-4806-9cc7-c1c0e12b0980" />
<img width="928" height="401" alt="image" src="https://github.com/user-attachments/assets/20834265-290f-4758-ab1b-87cc885bcf1b" />

## Modificar o Código da Função Lambda (para versão "prod"):
14. Volte para a sua Função lambda “minha-funcao-proxy-lab-seunomesobrenome”
15. Volte para a aba Código.
16. Substitua o código no editor (lambda_function.py) pelo:

Código clicando aqui: Cód. py do ambiente de PRODUÇÃO!
```
import json
import os

def lambda_handler(event, context):
    try:
        stage = event['requestContext']['stage']
    except KeyError:
        stage = 'desconhecido'

    response_body = {
        'message': f'Ola do ambiente de Producao!',
        'functionVersion': context.function_version,
        'functionAlias': (
            context.invoked_function_arn.split(':')[-1]
            if ':' in context.invoked_function_arn else '$LATEST'
        )
    }

    return {
        'statusCode': 200,
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'
        },
        'body': json.dumps(response_body, ensure_ascii=False)
    }
```
17.Clique em Deploy.
18. Teste novamente a versão $LATEST (usando o mesmo evento teste-simulado).
19.Clique na aba Testar.
20.Clique no botão Testar.
21. Verifique o resultado em Resultado da execução -> Detalhes.

<img width="1266" height="807" alt="image" src="https://github.com/user-attachments/assets/fa5ce2a1-e15c-41db-bdd6-5d90dc7c7b62" />
<img width="1163" height="682" alt="image" src="https://github.com/user-attachments/assets/46ab00ee-ae5c-4d57-a009-7ef283257ff4" />

## Publicar a Versão 2 da Função Lambda:
22. Na aba Versões, clique em Publicar nova versão.
23. Descrição da versão (opcional): Versão para ambiente de produção.
24. Clique em Publicação ou Publicar.

<img width="1249" height="649" alt="image" src="https://github.com/user-attachments/assets/ef8ddeee-62dc-4127-b5f7-43ddf6b5c327" />

## Criar Alias "prod":
25. Volte para a sua Função lambda “minha-funcao-proxy-lab-seunomesobrenome”
26. Na aba Aliases, clique em Criar alias.
27. Nome do alias: prod.
28. Descrição do alias (opcional): Alias para ambiente de produção.
29.Versão: Selecione 2.
30.Clique em Salvar.

<img width="1265" height="707" alt="image" src="https://github.com/user-attachments/assets/a19614b6-17e7-47f6-8005-716853e45f0e" />
<img width="1271" height="558" alt="image" src="https://github.com/user-attachments/assets/3c181658-2bae-4afd-8610-dc4dcfca1860" />
<img width="1261" height="618" alt="image" src="https://github.com/user-attachments/assets/f6f7bb26-1a5f-4130-8364-e8561fc668c8" />

31.Copie o ARN e guarde-o no bloco de notas, você vai precisar posteriormente.

# Parte 3: Criação e Configuração do API Gateway
1. Acessar Console API Gateway: Navegue até o serviço API Gateway.
2. Clique em Criar uma API.
3. Escolher um tipo de API:
4. Localize a caixa API REST e clique em Compilar.
5. Atenção: Não é a opção API REST privada
6. Criar nova API: Deixe "API nova" selecionado.
7. Nome da API: Insira um nome, ex: minha-api-proxy-lab-seunomesobrenome.
8. Descrição (opcional): API Gateway (proxy) para lab de Lambda com Aliases.
9. Tipo de endpoint de API: "Regional".
10.Clique em Criar API.

<img width="1257" height="667" alt="image" src="https://github.com/user-attachments/assets/821ee6e0-4647-4a8e-a384-feb1454c923c" />
<img width="1265" height="810" alt="image" src="https://github.com/user-attachments/assets/b07ddd20-7201-4e7b-b6dd-bea30de48533" />
<img width="1257" height="815" alt="image" src="https://github.com/user-attachments/assets/12892699-a2f9-406b-a93e-183de940bc19" />

## Criar um Recurso (Path) na API:
1. No painel esquerdo ("Recursos"), com a raiz / selecionada, clique em Criar
recurso.
2. Nome do recurso: hello.
3. Clique em Criar recurso.

<img width="1255" height="732" alt="image" src="https://github.com/user-attachments/assets/5324ec6d-9f5f-449e-812a-5e9637c60b1c" />
<img width="1270" height="397" alt="image" src="https://github.com/user-attachments/assets/453f0669-9e85-40e7-b690-e6bab9433b79" />

## Criar Método GET no Recurso /hello (com Proxy)
Para o ambiente de desenvolvimento:
1. Selecione o recurso /hello recém-criado.
2. Clique em Criar método.
3. Tipo de método: Selecione GET.
4. Tipo de integração: Selecione Função Lambda.
5. Integração do proxy do Lambda: Habilite.
6. Região da Lambda: Selecione a região onde sua função Lambda foi criada (já
deve esta selecionada).
7. Função Lambda: Cole o ARN do Alias Dev.
Atenção: Esse é o meu exemplo. Você não deve colar esse ARN, Você deverá colar o ARN do do seu alias “dev”.

```arn:aws:lambda:us-east-1:237482015336:function:minha-funcao-proxy-lab-seunomesobrenome:dev``` 
8. Clique em Criar método.

<img width="981" height="565" alt="image" src="https://github.com/user-attachments/assets/dd3ae48b-e679-4277-9b6f-1c3d86cd44a6" />
<img width="1269" height="792" alt="image" src="https://github.com/user-attachments/assets/d9e6d427-d215-4734-9f23-febd2e47b29a" />

### Agora vamos implantar criando o Estágio para o ambiente de Desenvolvimento:
9. Clique em Implantar API
10. Em Estágio, selecione “Novo estágio”
11. Nome do estágio: Desenvolvimento.
12. Descrição da implantação: API/Lambda - Versao Desenvovimento.
13. Implantar

<img width="1270" height="654" alt="image" src="https://github.com/user-attachments/assets/975b8556-a904-4743-8757-51e741b70da9" />
<img width="1265" height="796" alt="image" src="https://github.com/user-attachments/assets/4fabcb22-c13e-4257-98b6-b0187c135e3d" />
<img width="1267" height="641" alt="image" src="https://github.com/user-attachments/assets/7909ff30-6709-4129-a598-ec364b70430f" />

## Vamos implantar criando o Estágio para o ambiente de Produção:
14. Volte em Recursos
15. Selecione o método GET
16. Selecione Solicitação de integração.
17. Editar
18. Em Função Lambda, troque pelo ARN do alias “prod”. Cole o ARN do alias prod.
19. Será algo parecido com: Atenção: Esse é o meu exemplo. Você não deve colar esse ARN, Você deverá colar o ARN do do seu alias “prod”.
```arn:aws:lambda:us-east-1:237482015336:function:minha-funcao-proxy-lab-seunomesobrenome:prod```
20. Salve

<img width="1267" height="720" alt="image" src="https://github.com/user-attachments/assets/7369550b-1db7-463d-b085-ad62d53afc26" />
<img width="1276" height="768" alt="image" src="https://github.com/user-attachments/assets/034d5549-9056-4c32-bad0-07546a22b285" />

## Agora vamos implantar criando o Estágio para o ambiente de Produção:
21.Clique em Implantar API
22. Em Estágio, selecione “Novo estágio”
23. Nome do estágio: Producao.
24. Descrição da implantação: API/Lambda - Producao.
25. Implantar

<img width="981" height="657" alt="image" src="https://github.com/user-attachments/assets/7e304f90-0fdf-41e5-b09a-12af76b7175c" />
<img width="1266" height="748" alt="image" src="https://github.com/user-attachments/assets/dba07297-204b-4c48-890f-6a75e5ae433a" />
<img width="1292" height="635" alt="image" src="https://github.com/user-attachments/assets/7b68792a-c436-48f8-9b90-1ee0a9e0b9c7" />
<img width="1270" height="688" alt="image" src="https://github.com/user-attachments/assets/ed565269-f49a-459a-925e-f36b46973a35" />

# Parte 4: Testes
Vamos testar se a ligação da sua função Lambda com o API Gateway funcionou?
1. Volte em Estágios.
2. Clique no sinal de “+” (Desenvolvimento) para expandir, novamente e novamente, até você ver o método GET.
3. Selecione o GET, você verá a URL “Invocar URL”

<img width="1269" height="572" alt="image" src="https://github.com/user-attachments/assets/2b674676-a9fb-44df-bf39-1a3b3c58d681" />

5. Copie e cole no seu navegador.
6. Se tudo deu certo, você verá um resultado parecido com as imagens abaixo:
<img width="794" height="89" alt="image" src="https://github.com/user-attachments/assets/9ead0416-0b23-433c-b459-8cb8a735a4ab" />
7. Selecione a caixinha “Estilo de formatação”
<img width="514" height="170" alt="image" src="https://github.com/user-attachments/assets/f56831e5-669a-4b04-9cc2-8f2ae9c1b14e" />

Repita o procedimento para testar a base de Produção.
7. Se tudo ocorreu bem, o resultado será igual as imagens abaixo:
<img width="733" height="286" alt="image" src="https://github.com/user-attachments/assets/37e08be2-8742-4f59-8503-d5f8fd9b5d88" />

<img width="1031" height="196" alt="image" src="https://github.com/user-attachments/assets/cbfc9816-99ae-4898-a4c2-d342382bfe86" />
<img width="544" height="207" alt="image" src="https://github.com/user-attachments/assets/4030a87b-02b0-494f-ab22-e30f3982806b" />

--

<img width="936" height="239" alt="image" src="https://github.com/user-attachments/assets/caf5a436-81f6-4c15-af62-75a8ecb703ae" />
<img width="416" height="166" alt="image" src="https://github.com/user-attachments/assets/b6fa3dd3-2930-428c-8423-c850b7f58c0d" />

# Parte 6: Limpeza
## Limpeza de Recursos (IMPORTANTE):
1. Excluir Stages API Gateway: No console API Gateway, selecione sua API, selecione a sua API e clique em "Excluir".
2. Excluir Função Lambda: No console do Lambda, selecione a função Lambda e clique em "Ações" -> "Excluir".
3. Confirme a exclusão.

<img width="1265" height="530" alt="image" src="https://github.com/user-attachments/assets/c692c356-cf4e-428f-88bc-0260437d44af" />
<img width="1282" height="798" alt="image" src="https://github.com/user-attachments/assets/f300a66d-3222-4926-8485-351acb704b90" />

> stay focused!


