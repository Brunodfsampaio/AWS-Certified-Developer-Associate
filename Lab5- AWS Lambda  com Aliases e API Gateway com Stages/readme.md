# Resumo do Laboratório
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

# Parte 2: Publicação de Versões e Criação de Aliases
1. Publicar a Versão 1 da Função Lambda:
2. Na página da sua função Lambda, vá para a aba Versões.
3. Clique em Publicar nova versão.
4. Descrição da versão (opcional): Versão inicial da função de desenvolvimento.
5. Clique em Publicação ou Publicar.

## Criar Alias "dev":
6. Volte para a sua Função lambda “minha-funcao-proxy-lab-seunomesobrenome”
7. Vá para a aba Aliases.
8. Clique em Criar alias.
9. Nome do alias: dev.
10. Descrição do alias (opcional): Alias para ambiente de desenvolvimento.
11.Versão: Selecione 1.
12. Clique em Salvar.
13.Copie o ARN e guarde-o no bloco de notas, você vai precisar posteriormente.
Modificar o Código da Função Lambda (para versão "prod"):
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

## Publicar a Versão 2 da Função Lambda:
22. Na aba Versões, clique em Publicar nova versão.
23. Descrição da versão (opcional): Versão para ambiente de produção.
24. Clique em Publicação ou Publicar.
Criar Alias "prod":
25. Volte para a sua Função lambda “minha-funcao-proxy-lab-seunomesobrenome”
26. Na aba Aliases, clique em Criar alias.
27. Nome do alias: prod.
28. Descrição do alias (opcional): Alias para ambiente de produção.
29.Versão: Selecione 2.
30.Clique em Salvar.

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

## Criar um Recurso (Path) na API:
1. No painel esquerdo ("Recursos"), com a raiz / selecionada, clique em Criar
recurso.
2. Nome do recurso: hello.
3. Clique em Criar recurso.

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
Atenção: Esse é o meu exemplo. Você não deve colar esse ARN, Você deverá
colar o ARN do do seu alias “dev”.

```arn:aws:lambda:us-east-1:237482015336:function:minha-funcao-proxy-lab-seunomesobrenome:dev``` 

8. Clique em Criar método.
### Agora vamos implantar criando o Estágio para o ambiente de Desenvolvimento:
9. Clique em Implantar API
10. Em Estágio, selecione “Novo estágio”
11. Nome do estágio: Desenvolvimento.
12. Descrição da implantação: API/Lambda - Versao Desenvovimento.
13. Implantar

## Vamos implantar criando o Estágio para o ambiente de Produção:
14. Volte em Recursos
15. Selecione o método GET
16. Selecione Solicitação de integração.
17. Editar
18. Em Função Lambda, troque pelo ARN do alias “prod”. Cole o ARN do alias prod.
19. Será algo parecido com: Atenção: Esse é o meu exemplo. Você não deve colar esse ARN, Você deverá colar o ARN do do seu alias “prod”.
```arn:aws:lambda:us-east-1:237482015336:function:minha-funcao-proxy-lab-seunomesobrenome:prod```
20. Salve

## Agora vamos implantar criando o Estágio para o ambiente de Produção:
21.Clique em Implantar API
22. Em Estágio, selecione “Novo estágio”
23. Nome do estágio: Producao.
24. Descrição da implantação: API/Lambda - Producao.
25. Implantar

# Parte 4: Testes
Vamos testar se a ligação da sua função Lambda com o API Gateway funcionou?
1. Volte em Estágios.
2. Clique no sinal de “+” (Desenvolvimento) para expandir, novamente e novamente, até você ver o método GET.
3. Selecione o GET, você verá a URL “Invocar URL”
4. Copie e cole no seu navegador.
5. Se tudo deu certo, você verá um resultado parecido com as imagens abaixo:
<img width="794" height="89" alt="image" src="https://github.com/user-attachments/assets/9ead0416-0b23-433c-b459-8cb8a735a4ab" />
6. Selecione a caixinha “Estilo de formatação”
<img width="514" height="170" alt="image" src="https://github.com/user-attachments/assets/f56831e5-669a-4b04-9cc2-8f2ae9c1b14e" />

Repita o procedimento para testar a base de Produção.
7. Se tudo ocorreu bem, o resultado será igual as imagens abaixo:
<img width="733" height="286" alt="image" src="https://github.com/user-attachments/assets/37e08be2-8742-4f59-8503-d5f8fd9b5d88" />

# Parte 6: Limpeza
## Limpeza de Recursos (IMPORTANTE):
1. Excluir Stages API Gateway: No console API Gateway, selecione sua API, selecione a sua API e clique em "Excluir".
2. Excluir Função Lambda: No console do Lambda, selecione a função Lambda e clique em "Ações" -> "Excluir".
3. Confirme a exclusão.

