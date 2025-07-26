# Laboratório – SSM Parameter Store
Hands on (CLI )

# Observação: A interface do Console de Gerenciamento da AWS pode sofrer
pequenas alterações visuais ao longo do tempo, mas os conceitos e a localização
geral dos serviços permanecem consistentes. As instruções neste resumo
seguem a estrutura geral das funcionalidades.
Neste laboratório prático, você aprenderá a criar e gerenciar parâmetros utilizando
o AWS Systems Manager Parameter Store, incluindo parâmetros seguros
criptografados com chaves do KMS (AWS Key Management Service), além de
acessar os valores por meio da AWS CLI no CloudShell.
Objetivos do Laboratório:
Este laboratório ensina como:
- Criar parâmetros do tipo String e SecureString no Parameter Store.
- Criar uma chave de criptografia no KMS.
- Recuperar os parâmetros utilizando a AWS CLI, com e sem descriptografia.
- Navegar com confiança pelos principais recursos do AWS Systems Manager.

### Cenário:
Você é um desenvolvedor responsável por armazenar e acessar configurações de
um aplicativo, como URLs de banco de dados e senhas, de forma segura. Para
isso, usará o AWS Systems Manager Parameter Store, em conjunto com o AWS
KMS para criptografar dados sensíveis. Posteriormente, utilizará o AWS CloudShell
para acessar esses parâmetros pela CLI.
Pré-requisitos:
- Conta AWS ativa.
- Acesso ao Console de Gerenciamento da AWS.
- Permissões para usar o Systems Manager, KMS e CloudShell.

# Parte 1: Criação SSM Parameter Store
1. Acesse o Console de Gerenciamento da AWS e navegue até o serviço SSM( AWS Systems Manager ).
2. No painel de navegação esquerdo, localize Application Tools (Ferramentas
de aplicação) e clique em Parameter store (Armazenamento deparametros).
3. Clique em Create parameter (Criar parametro).
4. Agora vamos criar quatro parameter store, e preencher somente os campos que estão marcado na imagem abaixo.

<img width="575" height="613" alt="image" src="https://github.com/user-attachments/assets/2db04e16-0ca7-460f-a060-1d1a5bf28fea" />
<img width="1278" height="821" alt="image" src="https://github.com/user-attachments/assets/71b7f320-80ed-4cbc-a049-d7d346619b93" />


**Criação de Parâmetros do Tipo String**
5. Agora vamos criar dois parâmetros no Parameter Store com informações de URLs de banco de dados.

## Parâmetro 1 – Ambiente de Desenvolvimento

6. No campo Name, insira:
/meu-app-SeuNomeSobreNome/dev/db-url

7. No campo Description, insira:
Database URL for my app in dev

8. No campo Type, selecione:
String

9. No campo Value, insira:
dev.database.SeuNomeSobrenome.com:3306
10.Clique em Create parameter (Criar parâmetro) para salvar.
• Anote em um bloco de notas o nome do seu parâmetro, pois você
precisará dessa informação em etapas futuras.

## Parâmetro 2 – Ambiente de Produção
11. Repita o processo de criação do parâmetro clicando novamente em Create parameter.

12. No campo Name, insira:
/meu-app-SeuNomeSobreNome/prod/db-url

13. No campo Description, insira:
Database URL for my app in prod

14. No campo Type, selecione: String

15. No campo Value, insira:
prod.database.SeuNomeSobrenome.com:3306

- Anote em um bloco de notas o nome do seu parâmetro, pois você precisará dessa informação em etapas futuras.
16. Pronto! Agora temos dois parâmetros criados:

<img width="821" height="207" alt="image" src="https://github.com/user-attachments/assets/3a9b1bec-14b2-473b-8d8c-5db88bf23449" />
<img width="896" height="803" alt="image" src="https://github.com/user-attachments/assets/71aab395-3959-4dd4-b694-9a1a3de39f28" />
<img width="906" height="765" alt="image" src="https://github.com/user-attachments/assets/1a0ca50a-b2ae-40af-a50a-78c1e99ebee3" />

17. Antes de criamos os outros 2 parâmetros, precisaremos cria uma chave KMS.

# Parte 2: Criação da Chave KMS (AWS Key Management Service)
18. Acesse o Console de Gerenciamento da AWS e navegue até o serviço KMS
(AWS Key Management Service).
19.Clique no botão Create a key (Criar uma chave).
20. Na primeira tela, não altere nada. Clique em Next (Próximo).
21. Na segunda tela, preencha somente os campos:
a. Alias:
SeuNomeSobrenome-key
b. Description:
SeuNomeSobrenome-key

<img width="854" height="387" alt="image" src="https://github.com/user-attachments/assets/7d912da6-7f2e-4f47-94e2-4d5294663ff5" />
<img width="1238" height="807" alt="image" src="https://github.com/user-attachments/assets/16a158b2-1b87-47f6-9d1a-451b87bc75fc" />

22.Clique em Skip to review ( pular para revisão ) e depois clique em Finish (
Finalizar ).
Vamos criar mais dois Parameter Store.

## Parâmetro 3 – Ambiente de Desenvolvimento
23. Volte ao serviço SSM ( AWS Systems Manager ).
24. No painel de navegação esquerdo, localize Application Tools (Ferramentas
de aplicação) e clique em Parameter store (Armazenamento de
parametros).
25.Clique em Create parameter (Criar parametro).
26. No campo Name, insira:
/meu-app-SeuNomeSobreNome/dev/db-password
27. No campo Description, coloque a descrição:
28. Database password for my app in dev
29. No campo Type, selecione a opção:
SecureString
30. No campo KMS key source, não altere a opção.
31. No campo KMS key id, procure pela chave que foi criada no passo 16.
32. No campo Value, coloque a informação:
aqui é a senha do dev
33. Clique em Create parameter (Criar parâmetro).
- Anote em um bloco de notas o nome do seu parâmetro, pois você precisará dessa informação em etapas futuras.

<img width="933" height="812" alt="image" src="https://github.com/user-attachments/assets/e33d84cd-6cdc-4d1c-b8b1-5afc2b42c7d8" />
<img width="898" height="825" alt="image" src="https://github.com/user-attachments/assets/4b86b794-a097-4983-a0d9-e37b3624380b" />

## Parâmetro 4 – Ambiente de Produção
34. No campo Name, insira:
/meu-app-SeuNomeSobreNome/prod/db-password
35. No campo Description, coloque:
Database password for my app in prod
36. No campo Type, selecione a opção:
SecureString
37. No campo Value, coloque:
aqui é a senha da prod
38.Clique em Create parameter (Criar parâmetro).
- Anote em um bloco de notas o nome do seu parâmetro, pois você precisará dessa informação em etapas futuras.

<img width="967" height="807" alt="image" src="https://github.com/user-attachments/assets/38e8b29a-203b-4ad5-bb0a-e0090efc8fe9" />
<img width="967" height="807" alt="image" src="https://github.com/user-attachments/assets/3566d31b-b7b0-4c05-95a7-60fa1a63eb55" />

Ao finalizar todas as etapas, confirme que foram criados 4 parâmetros no
Parameter Store: 2 do tipo String e 2 do tipo SecureString, conforme
mostrado na imagem exemplo abaixo:

<img width="770" height="380" alt="image" src="https://github.com/user-attachments/assets/c7b5357d-3056-4b55-a260-6bbad81a9d91" />
<img width="931" height="578" alt="image" src="https://github.com/user-attachments/assets/71d28f57-353e-4a73-83c4-6dbace25d104" />

# Parte 3: Acessar o CLI pelo Cloudshell
39. Acesse o Console de Gerenciamento da AWS e navegue até o serviço
CloudShell ( AWS CLOUDSHELL ).
40. Na tela, será exibido o terminal do AWS CLI dentro da própria plataforma da AWS. Vamos começar a executar alguns comandos.
41.Digite o seguinte comando:

```aws ssm get-parameters --names /meu-app-SeuNomeSobreNome/dev/db-url /meu-app-SeuNomeSobreNome/dev/db-password```

- Observe que você deve substituir os campos destacados em amarelo pelos nomes exatos dos parâmetros que você criou nos passos anteriores.
42. Pressione Enter.
43. Verifique se o resultado exibido é semelhante ao da imagem abaixo.

<img width="884" height="557" alt="image" src="https://github.com/user-attachments/assets/0d6f8dbc-ab3a-4128-8a65-763b3ea83d2a" />
<img width="1264" height="533" alt="image" src="https://github.com/user-attachments/assets/2ff38b9e-e605-488d-a688-90fb891d6105" />

- Observe que as informações exibidas no campo “Value” estão em formatos diferentes: uma está descriptografada e a outra aparece criptografada.

Agora vamos descriptografar a informação que aparece criptografada, utilizando
o mesmo comando anterior, com um acréscimo no final.
44.Digite o seguinte comando:
```aws ssm get-parameters --names /meu-app-SeuNomeSobrenome/dev/db-url /meu-app-SeuNomeSobrenome/dev/db-password --with-decryption```

Observe que você deve substituir os campos destacados em amarelo
pelos nomes exatos dos parâmetros que você criou nos passos anteriores.
45. Pressione Enter.
Verifique se o resultado exibido na sua tela é semelhante ao da imagem
abaixo.

<img width="665" height="704" alt="image" src="https://github.com/user-attachments/assets/476900b5-3f98-4b77-a64b-9d58ab9becb4" />
<img width="1268" height="804" alt="image" src="https://github.com/user-attachments/assets/8b8402e2-dcd8-43bf-ab94-eb3fc4f9f56b" />

Se você conseguiu executar todas as etapas, parabéns! Não se esqueça de excluir todos os recursos criados durante este exercício para evitar cobranças desnecessárias.
Primeiro, exclua os parâmetros. Acesse novamente a tela do Systems Manager
Parameter Store (Armazém de Parâmetros), procure pelos parâmetros pelo
mesmo nome e sobrenome que inseriu, selecione-os e clique em Excluir.

<img width="964" height="333" alt="image" src="https://github.com/user-attachments/assets/f6ff3ad3-cc90-4132-90c5-4fca8077917a" />

Confirme a exclusão e em Excluir parâmetros.

Depois, vá no KMS, procure pela chave com o seu nome e sobrenome e selecione-
a. Após isso, clique em Ações da chave > Programar exclusão da chave.

<img width="962" height="272" alt="image" src="https://github.com/user-attachments/assets/a5218307-7d0e-4176-985c-3794731b15fe" />

Na tela seguinte, altere o Período de espera (em dias) para 7.
Mais abaixo, selecione a opção de Confirmação (Confirme se você deseja
programar essas chaves para exclusão após um período de espera de 7 dias.),
igual na imagem abaixo:

<img width="900" height="398" alt="image" src="https://github.com/user-attachments/assets/e77f23ed-f367-4fcb-9451-2f1b5d8072b3" />
<img width="1238" height="738" alt="image" src="https://github.com/user-attachments/assets/af56ef5b-8fa4-4218-8981-98847d18d686" />

Após isso, clique em Programar exclusão e pronto! Tudo finalizado!

> stay focused!





