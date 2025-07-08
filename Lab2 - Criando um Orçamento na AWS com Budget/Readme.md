![image](https://github.com/user-attachments/assets/201b1f9d-934f-4c58-9ca5-809530cac58c)#### Observação: A interface do Console de Gerenciamento da AWS pode sofrer pequenas alterações visuais ao longo do tempo, mas os conceitos e a localização geral dos serviços permanecem consistentes. As instruções neste resumo seguem a estrutura geral das  funcionalidades. Este laboratório orienta passo a passo como configurar um orçamento de gastos na AWS utilizando o serviço AWS Budgets. A atividade inclui a criação do orçamento, configuração de alertas por e-mail e verificação das definições aplicadas. 
##### Objetivos do laboratório Este laboratório ensina como: 
- Acessar o serviço AWS Budgets pelo Console de Gerenciamento da AWS.
- Criar um orçamento com limite personalizado.
- Configurar alertas por e-mail baseados em percentuais de uso.
- Validar a criação do orçamento e testar notificações automáticas.
---
### Passo 1: Criando um Budget
1. Acesse o Console de Gerenciamento da AWS e navegue até o serviço AWS Budgets. Clique no botão Criar orçamento.
2. No campo Nome do orçamento, digite meu-budget-seunome. 
3. No campo Destinatários de e-mail, digite o seu endereço de e-mail (é para este e-mail que você receberá as notificações).
4. Role a página para baixo e clique no botão Criar orçamento.

![image](https://github.com/user-attachments/assets/94d25e5b-1792-4640-944b-78c10eb3456f)
- clicar em "criar orçamento"

![image](https://github.com/user-attachments/assets/d39e1573-6a6a-4050-8c41-c85dd5185a3c)
- depois ir em criar orçamento.

6. Na lista de orçamentos, clique no nome do seu orçamento (meu-budget- seunome) para acessá-lo.

![image](https://github.com/user-attachments/assets/1f175e38-11aa-4d95-b187-33d7e03e88a9)

8. Na tela de detalhes do orçamento, no canto superior direito, clique no botão Editar.

![image](https://github.com/user-attachments/assets/ce29d27d-80fb-4e99-9a3a-0cf919225555)

10. Agora role para baixo e navegue até a seção Alerta #1.
12. No campo Limite, digite 10 e selecione % do valor orçado no dropdown ao lado.

![image](https://github.com/user-attachments/assets/60f7e8d6-2e4b-42fa-9abc-3459e3989fc8)

14. No campo Acionador, deixe selecionado Real. Role para baixo e clique em Próximo.
15. Na tela Associar ações, não altere nenhuma configuração nesta seção e clique em Próximo.

![image](https://github.com/user-attachments/assets/043503c4-ce8f-4f92-9c70-a85ce1607555)

17. A próxima tela é a de Revisão. Aqui, você poderá conferir todas as informações que configurou para o seu orçamento. Role a página para baixo e clique em Salvar.
18. Agora, vamos visualizar o resumo das configurações que acabamos de realizar. Retorne à lista principal de orçamentos. Você deverá ver o seu orçamento listado com as informações configuradas (nome, orçamento definido, valor usado, etc.), conforme mostra a imagem de referência abaixo. Verifique se o Nome do orçamento, o Orçamento (valor definido) e o Valor usado correspondem ao que você configurou e ao que é mostrado na imagem.

![image](https://github.com/user-attachments/assets/3d1aa2fc-c4f8-4636-920f-d173276a7215)

![image](https://github.com/user-attachments/assets/6ffce1c8-30f6-4960-ab53-1d72af0e8547)

##### Excelente! Concluir a configuração de um Budget na AWS é um passo muito importante. Configurar um Budget como este que você acabou de criar é uma prática fundamental na gestão de custos na AWS. Ele não é apenas um número definido, mas sim uma ferramenta proativa essencial. A importância do AWS Budgets reside precisamente em sua capacidade de oferecer visibilidade sobre seus gastos e, crucialmente, de alertar você antes que seus custos ultrapassem limites definidos. Ao ser notificado quando atinge um percentual (como os 10% configurados) ou um valor absoluto do seu orçamento, você ganha a oportunidade de: 
- Investigar a origem dos custos.
- Tomar ações corretivas a tempo (desligar recursos não utilizados, otimizar serviços, etc.).
- Evitar surpresas desagradáveis e contas elevadas ao final do ciclo de faturamento.

##### É uma camada essencial de controle financeiro que transforma o gerenciamento de
custos de reativo (descobrir o gasto no final do mês) para proativo (monitorar e agir
enquanto o gasto acontece). Portanto, utilize essa ferramenta e fique sempre atento
aos alertas para manter seus custos na nuvem sob controle!


> stay focused!
