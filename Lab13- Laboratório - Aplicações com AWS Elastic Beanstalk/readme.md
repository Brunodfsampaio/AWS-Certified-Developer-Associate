AWS Elastic Beanstalk
## Observação: 
A interface do Console de Gerenciamento da AWS pode sofrer
pequenas alterações visuais ao longo do tempo, mas os conceitos e a localização
geral dos serviços permanecem consistentes. As instruções neste resumo
seguem a estrutura geral das funcionalidades.

Este laboratório prático guia você através do processo de criação de uma role IAM
necessária, implantação de uma aplicação web de exemplo usando o AWS Elastic
Beanstalk, resolução de um problema comum de permissão relacionado a
atualizações gerenciadas e exploração das funcionalidades de gerenciamento e
monitoramento do Elastic Beanstalk. Ao final, você terá uma compreensão prática
de como o Elastic Beanstalk simplifica a implantação e o gerenciamento de
aplicações na nuvem AWS.

## Objetivos
Este laboratório ensina como:
- Criar uma role IAM (Perfil de Instância EC2) com as permissões
necessárias para o Elastic Beanstalk.
- Configurar e lançar um ambiente Elastic Beanstalk para uma aplicação
web (Node.js neste exemplo).
- Navegar pelo console do Elastic Beanstalk para monitorar a saúde, logs
e métricas da aplicação.
- Entender como o Elastic Beanstalk abstrai e gerencia a infraestrutura
AWS subjacente (EC2, Auto Scaling, ELB, CloudWatch).

# Cenário: 
Você é um desenvolvedor ou engenheiro de DevOps encarregado de
implantar rapidamente uma aplicação web na AWS. Você precisa garantir que a
infraestrutura seja configurada corretamente, que as permissões adequadas
estejam em vigor para gerenciamento e atualizações, e que a aplicação possa ser
facilmente gerenciada e monitorada. O AWS Elastic Beanstalk é a ferramenta
escolhida para automatizar a maior parte desse processo.
Pré-requisitos:
- Uma conta AWS ativa.
- Acesso ao Console de Gerenciamento da AWS com permissões para criar roles IAM, ambientes Elastic Beanstalk e visualizar recursos relacionados (EC2, CloudFormation, CloudWatch).
- Conhecimento básico de navegação no Console AWS.

## Parte 1: Criação da Role IAM para Instâncias EC2 do Beanstalk
Objetivo: Criar um perfil de instância EC2 que o Elastic Beanstalk usará para
gerenciar as instâncias EC2 em seu nome.
1. Acesse o Console de Gerenciamento da AWS e navegue até o serviço IAM.
2. No painel de navegação esquerdo, clique em Roles (Funções).
3. Clique em Create role (Criar função).
4. Em "Select trusted entity" (Selecionar entidade confiável), escolha AWS
service.
5. Em "Use case" (Caso de uso), selecione EC2 e clique em Next.
6. Na página "Add permissions" (Adicionar permissões), procure por políticas
relacionadas ao Beanstalk. Anexe as seguintes políticas gerenciadas pela
AWS (ou similares, dependendo das necessidades exatas):
- AWSElasticBeanstalkWebTier (Para ambientes Web Server)
- AWSElasticBeanstalkWorkerTier (Se usar Worker Tiers)
- AWSElasticBeanstalkMulticontainerDocker (Se usar Multi-containerDocker)
- AWSElasticBeanstalkEnhancedHealth (Essencial para relatórios de saúde detalhados)
- AWSElasticBeanstalkManagedUpdatesCustomerRolePolicy
7. Clique em Next.
8. Na página "Name, review, and create" (Nomear, revisar e criar):
- Digite um nome para a role, por exemplo: role-beanstalk-ec2-SeuNomeSobreNome.
- (Opcional) Adicione uma descrição.
- Revise as políticas anexadas e a política de confiança (deve confiar no ec2.amazonaws.com).

9. Clique em Create role.

<img width="1251" height="642" alt="image" src="https://github.com/user-attachments/assets/93f36a6f-21d7-49ac-a9a9-9068dc28ac9e" />
<img width="1262" height="767" alt="image" src="https://github.com/user-attachments/assets/7c447588-f28d-4b23-92f3-6d2d4b1e39b0" />

## Parte 2: Criação do Ambiente Elastic Beanstalk
Objetivo: Configurar e lançar um ambiente Elastic Beanstalk usando a role criada e implantar a aplicação de exemplo.
1. Navegue até o serviço Elastic Beanstalk no Console AWS.
2. Clique em Create application (Criar aplicação).
3. Insira um nome para a aplicação, por exemplo: MeuAppsNomeSobrenome.
4. Em "Environment information" (Informações do ambiente):
- Insira um nome para o ambiente, por exemplo: MeuAppsNomeSobrenome-desenvolvimento.
- (Opcional) Personalize o domínio – Para esse laboratório, deixe em branco para geração automática.

5. Em "Platform" (Plataforma):
- Selecione a plataforma desejada (ex: Node.js, Python, Java, etc.).
Use Node.js como no exemplo.
- Platform Branch e Platform version (ramificação e a versão da
plataforma) deixe padrão.

6. Em "Application code" (Código da aplicação):
- Selecione Sample application para usar a aplicação de
demonstração fornecida pela AWS.

7. Em "Presets" (Predefinições):
- Selecione Single instance (free tier eligible) para este laboratório,
para simplificar e manter custos baixos.
- Next

<img width="1269" height="816" alt="image" src="https://github.com/user-attachments/assets/f5879d09-0135-46d7-a7a1-4fbfe57a0282" />
<img width="1260" height="798" alt="image" src="https://github.com/user-attachments/assets/007003ad-167b-49fa-91d6-28bd1cd82741" />

8. Em Configure service access.
- Marque Use an existing service role.
- Service role: Geralmente, selecione aws-elasticbeanstalk-service-role (se não existir, o Beanstalk pode oferecer para criá-la.

- Nota: Esta é a role que o serviço Beanstalk usa, diferente da role da instância.
- Selecione a role que você criou na Parte 1: “role-beanstalk-ec2–SeuNomeSobreNome”.
- o EC2 instance profile: Selecione novamente “role-beanstalk-ec2–SeuNomeSobreNome”.
- Cliente em Skip to review.
<img width="1236" height="590" alt="image" src="https://github.com/user-attachments/assets/816c4e98-f1bd-437b-aac3-8fa6cc576d5a" />

9. Na página Review (Revisar), verifique todas as configurações,
especialmente o "EC2 instance profile" em "Service access".
10.Clique em Submit (Enviar) ou Create environment (Criar ambiente).
11. O Elastic Beanstalk começará a provisionar os recursos (Isso pode levar de
5 a 10 minutos). Você pode observar os eventos sendo criados no painel do
ambiente ou no CloudFormation. A aplicação de exemplo será implantada
automaticamente.
12. Navegue até o console do CloudFormation.
- Selecione a Stack com o seu nome (verifique a coluna descrição)
<img width="862" height="163" alt="image" src="https://github.com/user-attachments/assets/6799b879-d257-4071-a78b-2839c2df60f7" />
13. Após a conclusão, o status do ambiente mudará para CREATE_COMPLETE.
14. Navegue por todas as colunas para visualizar o que foi criado pelo
CloudFormation.
<img width="744" height="562" alt="image" src="https://github.com/user-attachments/assets/c1936224-6c3a-4e9f-895c-55397364e7a9" />
<img width="1262" height="552" alt="image" src="https://github.com/user-attachments/assets/bc5585a4-9886-4e05-9661-4c6a4e1e8a13" />

Visão Geral e Exploração Pós-Laboratório:
Após a criação bem-sucedida do ambiente, explore as funcionalidades do console

do Elastic Beanstalk para o ambiente MeuAppsNomeSobrenome-
desenvolvimento:

1. Upload e Implantação:
o Clique no botão Upload and deploy (Carregar e implantar).
o Você pode fazer upload de um arquivo .zip contendo sua própria
aplicação ou uma nova versão.
o Dê um "Version label" (Rótulo de versão) e clique em Deploy
(Implantar). O Beanstalk cuidará da atualização das instâncias.

2. Saúde (Health):
o Clique na aba Health. Veja o status geral e informações detalhadas
sobre as solicitações e o estado das instâncias EC2 subjacentes.

3. Logs:
4. Clique na aba Logs. Você pode solicitar logs das instâncias (últimos 100
linhas ou completos). Os logs são puxados das instâncias EC2 e
disponibilizados para download via console:
<img width="867" height="201" alt="image" src="https://github.com/user-attachments/assets/4d18c354-3a1c-4945-a4a7-5963347b97bb" />
- Clique em Download.
<img width="1086" height="738" alt="image" src="https://github.com/user-attachments/assets/823dbad2-ea44-4ba2-a448-da27a02882f1" />

5. Monitoramento (Monitoring):
o Clique na aba Monitoring. Visualize gráficos do CloudWatch para
métricas essenciais como utilização da CPU, tráfego de rede,
latência do ELB (se aplicável), etc.

6. Alarmes (Alarms):
o Clique na aba Alarms. Veja os alarmes padrão do CloudWatch
criados pelo Beanstalk (geralmente relacionados à saúde do
ambiente) ou configurando alarmes personalizados.

7. Atualizações Gerenciadas (Managed updates):
o Clique na aba Managed updates. Configure janelas de manutenção
semanais para permitir que o Beanstalk aplique automaticamente
patches e atualizações de plataforma, mantendo seu ambiente
seguro e atualizado.
o Managed updates
o Essa fornece informações cronológica de todas as ações e
mudanças que ocorreram no ambiente.

8. Configuração (Configuration):
9. Clique em Configuration no painel esquerdo. Aqui você pode modificar
toda a infraestrutura e configuração do software gerenciado pelo
Beanstalk:
<img width="869" height="536" alt="image" src="https://github.com/user-attachments/assets/ab97c325-e93b-467c-afca-a1c73c520b55" />
- Service access: Mudar as roles IAM.
- Network: Mudar VPC, subnets, security groups.
- Instances: Alterar o tipo de instância EC2 (ex: t3.micro para m5.large).
- Capacity: Configurar Auto Scaling (número mínimo/máximo de instâncias, gatilhos de escalonamento).
-  Load balancer: Adicionar ou configurar um Application Load Balancer.
-  Rolling updates and deployments: Definir estratégias de implantação (All at once, Rolling, Immutable, etc.).
-  Monitoring: Ajustar as configurações de monitoramento do CloudWatch.
-  Database: Adicionar um banco de dados RDS gerenciado ao ambiente.
-  E muito mais.
10.Recursos Subjacentes: Lembre-se que o Beanstalk orquestra outros
serviços AWS. Você pode visitar os consoles do EC2, para ver as instâncias,security groups, load balancers, auto scaling groups, Elastic IPs. Além do
CloudFormation (para ver a stack que define a infraestrutura - conforme
passos anteriores), e CloudWatch (para métricas e logs detalhados).

Conclusão:
O AWS Elastic Beanstalk demonstra ser uma ferramenta poderosa para simplificar
a implantação e o gerenciamento de aplicações na AWS. Ao lidar com o
provisionamento da infraestrutura, balanceamento de carga, escalonamento
automático, monitoramento e implantação de código, ele permite que as equipes
de desenvolvimento se concentrem mais na lógica da aplicação e menos na
complexidade da infraestrutura subjacente. Este laboratório forneceu uma visão
prática de como configurar um ambiente, solucionar problemas comuns de
permissão e utilizar as diversas funcionalidades de gerenciamento oferecidas pelo
serviço, centralizando o ciclo de vida da sua aplicação web.





