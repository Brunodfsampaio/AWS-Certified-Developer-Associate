## Passos iniciais:
#### 1. Executar uma Instância EC2 pelo console
- Iremos iniciar uma instância através do console de gerenciamento, que será um servidor
web de teste. Depois iremos testar a conectividade desse servidor web.

#### 3. No canto superior esquerdo, na barra de pesquisa, pesquise pelo serviço EC2.
#### 4. Clique em Executar instância. Se não aparecer o botão laranja, você ir no menu
esquerdo, em Instâncias e depois em Executar instância.
#### 5. Preencha as informações conforme abaixo. Os campos que não forem mencionados, deixe no valor padrão:
> Nome: webserver-<seu nome e sobrenome>.
> (Não digite espaço, nem os sinais <>. Por exemplo, se seu nome for Jeff Bezos, digite webserver-jeffbezos)
> 
> Imagem de máquina da Amazon (AMI): Amazon Linux 2 AMI (HVM)
> 
> Tipo de instância: t2.micro
> 
> Par de chaves (login): no canto direito há a opção azul Criar novo par de chaves. Clique nela, para criar um par de chaves.
>
> Para o par de chaves ainda, crie com o nome parchave-<seu nome e sobrenome>.
>
> (Não digite espaço, nem os sinais <>. Por exemplo, se seu nome for Jeff Bezos, digite parchave-jeffbezos)
>
> Tipo de par de chaves: RSA
>
> Formato de arquivo de chave privada: .pem
>
> Clique em Criar par de chaves. Irá aparecer uma tela para salvar o arquivo .pem. Salve-o em uma pasta de fácil localização, pois o utilizaremos em breve.
>
> Depois volte à tela do console da AWS e selecione o par de chaves com o seu nome.
>
> Configurações de rede > Firewall (grupos de segurança): Criar grupo de segurança. Marque a opção “Permitir tráfego HTTP”.
>
> Detalhes avançados: clique nessa opção, depois vá até o final, em Dados de usuário (opcional) e insira o texto abaixo:
>
```
#!/bin/bash
yum -y install httpd
systemctl enable httpd
systemctl start httpd
echo '<html><h1>Olá do seu servidor web!</h1></html>' > /var/www/html/index.html
```
> Com estes dados de usuário, iremos instalar e iniciar um componente, para subirmos um servidor web. E então é criada uma página web simples.
>
> Após isso, clique em Executar instância.

![image](https://github.com/user-attachments/assets/6d2fe4dc-4943-404c-9457-90a808336e6f)

![image](https://github.com/user-attachments/assets/b3db53e9-4bf3-4b4e-b39d-6acbe05f041c)

![image](https://github.com/user-attachments/assets/637c5631-b778-4179-b69e-89893c616905)

![image](https://github.com/user-attachments/assets/fc9dbb0a-24a9-4a98-b175-9c57941a7f81)


- Caso precisar, anote o nome da instância que você utilizou, pois iremos precisar em breve. Após clicar para executar a instância, irá mostrar uma mensagem de Êxito. Você pode clicar em cima do ID da instância (geralmente começa com i- e terão várias letras e números) ou volte para o serviço EC2 e no menu esquerdo, clique em Instâncias.

#### 6. Após abrir a tela de Instâncias, localize a instância pelo seu nome e clique em cima do ID da instância e aguarde a Verificação de status mostrar 2/2 verificações aprovadas.

#### 7. Após isso, clique em cima do ID da sua instância, para mostrar todos os detalhes. Logo no início, irá mostrar o Endereço IPv4 público. Copie-o e deixe-o salvo em algum editor de texto. Usaremos este IP em breve.

#### 8. Abra outra aba no seu navegador e digite http:// e então o Endereço IPv4 público da sua instância. (Por exemplo, se o IP da sua instância for 8.8.8.8, digite http://8.8.8.8) Ao acessar pelo navegador, alguém vai te dar um oi!

#### 9. O servidor de teste está iniciado! Se apareceu a mensagem corretamente, pode fechar a aba da mensagem. Agora iremos iniciar outra instância pelo CloudShell.

![image](https://github.com/user-attachments/assets/8f06a0a2-1b10-4fbd-a898-8c5ccff13870)

![image](https://github.com/user-attachments/assets/7764f784-5fbe-4796-b340-8021d0fcdfc0)

---

### 2. Inicializar outra instância através do CloudShell  
- Agora que aprendemos como inicializar uma instância através console de gerenciamento da AWS, vamos inicializar outra instância através de uma ferramenta útil, que é o CloudShell. O CloudShell e também o AWS CLI nos permitem gerenciar toda a nossa infraestrutura, através de comandos ou scripts. Apesar de demandar uma curva maior de aprendizado, nos permite ter um controle muito mais apurado e escalável da nossa infraestrutura. Com essas ferramentas é possível inicializarmos dezenas ou até centenas de serviços com poucos ou nenhum clique de mouse, além de terem mecanismos acessíveis apenas destas formas.

#### 10. Volte ao console de gerenciamento da AWS.
#### 11. No canto superior direito do console de gerenciamento da AWS, próximo de onde mostra o nosso login, irá mostrar o ícone conforme abaixo. Clique nele e irá mostrar o CloudShell.

#### Após isso, o CloudShell irá aparecer na parte de baixo da tela.

#### 12. Agora iremos executar diversos comandos dentro do CloudShell. Copie o comando abaixo para um editor de texto. Mude-o para colocar o seu nome e sobrenome antes do -grupo, sem espaços e sem apagar os caracteres antes ou após esse nome. Depois, copie o comando que você editou, clique com o botão direito do mouse no meio do CloudShell que apareceu em baixo e irá aparecer o comando copiado. Depois disso, aperte Enter e irá executá-lo. 

```
GRUPO_SEGURANCA="seunome-grupo"
```

#### 13. Da mesma forma, copie os dois comandos seguintes para um editor de texto e  coloque seu nome, tanto no nome_instancia quanto no par_chave. No par_chave, use o mesmo nome do par de chaves que você criou. Depois disso, aperte Enter e irá executá-lo.
```
NOME_INSTANCIA="instancia-seunome"
PAR_CHAVE="parchave-seunome"
```

#### 14. Execute o comando abaixo.

```
SECURITY_GROUP_ID=$(aws ec2 create-security-group --group-name $GRUPO_SEGURANCA --description "Permitir HTTP" --query "GroupId" --output text)

```
- Irá aparecer um aviso no meio da tela. Clique no botão azul Colar e aperte Enter. Se aparecer uma tela com vários caracteres no CloudShell, para sair clique com o botão esquerdo do mouse no meio dele, aperte a letra Q e depois aperte Enter. Caso tiver dificuldades para sair, abra uma nova guia no próprio CloudShell ou feche-o e reabra o mesmo.

#### 15. Copie e execute o comando abaixo. Não precisa editá-lo.

```
aws ec2 authorize-security-group-ingress --group-id $SECURITY_GROUP_ID --protocol tcp --port 80 --cidr 0.0.0.0/0
```

#### 16. Execute o comando abaixo.
```
aws ec2 run-instances --instance-type t2.micro --image-id $(aws ssm get-parameters-by-path --path "/aws/service/ami-amazon-linux-latest" --query "Parameters[?ends_with(Name, 'al2023-ami-kernel-default-x86_64')].Value" --output text) --security-group-ids $SECURITY_GROUP_ID --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value='$NOME_INSTANCIA'}]" --key-name $PAR_CHAVE --user-data "IyEvYmluL2Jhc2gKeXVtIC15IGluc3RhbGwgaHR0cGQKc3lzdGVtY3RsIGVuYWJsZSBodHRwZApzeXN0ZW1jdGwgc3RhcnQgaHR0cGQKZWNobyAnPGh0bWw+PGgxPk9sw6EgZG8gc2V1IHNlcnZpZG9yIHdlYiE8L2gxPjwvaHRtbD4nID4gL3Zhci93d3cvaHRtbC9pbmRleC5odG1sCg=="
```

![image](https://github.com/user-attachments/assets/18dbbd9f-5efc-42cd-8d0b-9544c9ec2530)

![image](https://github.com/user-attachments/assets/1f70cbb1-59af-4653-988a-2fb0feb94a2f)

> para sair, digitar "q"

![image](https://github.com/user-attachments/assets/95fe373a-6bf5-4c1b-b706-a83b0e51526a)

![image](https://github.com/user-attachments/assets/ef7fb525-c839-4ffc-a1c4-fbd24704daf8)


#### 17. Pronto! Iniciamos uma instância pelo console de gerenciamento e outra com apenas um comando no CloudShell. Quer testar? Localize o IPv4 público da instância nova e acesse tanto via SSH quanto pelo navegador, digitando http:// e o IPv4 público. Não se lembra como encontrar o IP público? Volte nos passos anteriores, lá nos passos 6 e 7. 

![image](https://github.com/user-attachments/assets/621ab2cc-a06b-4b3e-b74d-45b2bfa4b741)
![image](https://github.com/user-attachments/assets/9baa5f59-3419-4954-99db-a14c7b263a45)


> Stay focused!
