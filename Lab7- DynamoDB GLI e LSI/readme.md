# Objetivo do laboratório
- Criar uma tabela no DynamoDB com chave de partição e chave de ordenação.
- Adicionar um índice secundário local (LSI) e um índice secundário global (GLI).
- Popular a tabela com dados de forma manual e automatizada (via CloudShell).
- Realizar buscas utilizando diferentes métodos e analisar a eficiência.
- Entender como os índices impactam a performance de leitura.
- Aprender a excluir índices e tabelas após uso.

# Cenário
Você foi contratado por uma empresa fictícia que precisa armazenar pedidos realizados por
clientes em uma base NoSQL. Sua missão é construir uma tabela chamada Pedido-
seunome no DynamoDB que possibilite consultas eficientes tanto por ID de usuário e data
do pedido quanto por status e valor total do pedido. Para isso, serão utilizados índices
secundários locais e globais.

# Pré-requisitos
- Acesso ao Console de Gerenciamento da AWS.
- Permissão para criar recursos no serviço DynamoDB.
- Familiaridade básica com o terminal CloudShell da AWS.
- Ter um editor de texto simples para editar arquivos .json.
- Conexão de internet ativa.

# Passo 1: Acessando o DynamoDB e Criando uma Nova Tabela
1. Acesse o Console de Gerenciamento da AWS.
2. Navegue até o serviço DynamoDB.
3. Clique em Criar tabela.
4. Preencha os campos obrigatórios:
- **Nome da tabela**: Pedido-seunome
- **Chave de partição**: ID do Usuário (Tipo: **String**)
- **Chave de classificação**: Data do Pedido (Tipo: **String**)
5. Consulte a imagem de exemplo para garantir que os campos estejam corretos.

<img width="733" height="299" alt="image" src="https://github.com/user-attachments/assets/b8ac2eb7-b6bb-4ed6-a319-72a2d7ac7b00" />
<img width="1247" height="548" alt="image" src="https://github.com/user-attachments/assets/db68c4b1-0c77-4875-8d6c-0c04da2ecb0d" />

# Passo 2: Configurar a tabela no DynamoDB
1. Em Configurações da tabela, marque Personalizar configurações.
2. Em Classe da tabela, selecione DynamoDB Standard.
3. Em Configurações da capacidade de leitura/gravação, escolha Provisionada.
4. Em Capacidade de leitura:
- Desative o Auto Scaling
- Defina Unidades provisionadas como 5
5. Em Capacidade de gravação:
- Desative o Auto Scaling
- Defina Unidades provisionadas como 5
6. Em Índices secundários, clique em Criar índice local e preencha:
- Chave de classificação: Status
- Tipo de dados: String
- Nome do índice: LSI-PedidoseunomeStatus
- Projeções de atributos: All

<img width="1242" height="803" alt="image" src="https://github.com/user-attachments/assets/180e405f-b3e8-4b13-ac74-5e9317e8aac6" />
<img width="1270" height="555" alt="image" src="https://github.com/user-attachments/assets/ebc19301-a8ba-4cd7-940f-547ad8f79cdc" />

7. Clique em Criar índice.
8. Verifique se a configuração corresponde à imagem de exemplo abaixo:
<img width="731" height="68" alt="image" src="https://github.com/user-attachments/assets/cf175f71-af9e-4fa1-a33d-c7928e1d2c14" />
9. Não altere mais nenhuma configuração.
10. Clique no botão Criar tabela.
11. Verifique se a tabela foi criada com sucesso, comparando com a imagem de referência.
<img width="727" height="154" alt="image" src="https://github.com/user-attachments/assets/27f3b4f0-9e5f-4ffd-8fa9-65d3fa9298f6" />

12. Clique no nome da tabela que você acabou de criar para adicionarmos itens.
13. Em seguida, clique em “Explorar itens da tabela”, como mostra a imagem

abaixo:
<img width="725" height="178" alt="image" src="https://github.com/user-attachments/assets/2ff09fbc-81e4-4a11-af94-6bcca7295b6e" />

# Passo 3: Configurar a tabela no DynamoDB
14. Acesse a página desejada e role até o final.
15. Clique no campo Criar item.

<img width="1266" height="789" alt="image" src="https://github.com/user-attachments/assets/0880c27a-123f-4829-b150-1e0ab71b26b1" />

16. Preencha os campos iniciais:
- ID do Usuário: U000
- Data do Pedido: 2025-05-16
(Formato americano - utilize a data do dia da atividade, se preferir)
17. Adicione o atributo "Endereco" (tipo: mapa):
- Clique em Adicionar novo atributo > selecione mapa
- Nome do campo: Endereco
18. Dentro do campo Endereco, clique em inserir um campo para adicionar os
subcampos:
- Tipo: String | Nome: bairro | Valor: Santa Maria
- Tipo: String | Nome: cidade | Valor: Recife
- Tipo: String | Nome: estado | Valor: PE
- Tipo: String | Nome: numero | Valor: S/D
- Tipo: String | Nome: rua | Valor: Cavalcante Novaes 2
19. Adicione o atributo "tags" (tipo: Lista):
- Clique em Adicionar novo atributo > selecione Lista
- Nome do campo: tags
20. Dentro de tags, clique em inserir um campo para adicionar:
- Tipo: String | Valor: Entrega zona rural
- Tipo: String | Valor: Equipe Rocket
- Tipo: String | Valor: Recife
21. Adicione os demais atributos:
- Adicionar novo atributo > Tipo: String | Nome: IDPedido | Valor: P1000
- Adicionar novo atributo > Tipo: String | Nome: Status | Valor: Pendente
- Adicionar novo atributo > Tipo: número | Nome: ValorTotal | Valor: 900

  <img width="609" height="366" alt="image" src="https://github.com/user-attachments/assets/502a7c95-6751-4af1-9392-77567e9f6bd3" />

22. Finalize:
- Clique no botão Criar item.
23. Validação final:
- Verifique se os campos foram criados corretamente.
- Expanda os campos compostos (como Endereco) para conferir os dados inseridos.

<img width="668" height="225" alt="image" src="https://github.com/user-attachments/assets/4069214b-84fc-45c1-a113-7929ee3e2e56" />

# Passo 4: Popular a tabela com o CloudShell
24. Clique no ícone do CloudShell
- Localize e clique no ícone do CloudShell no canto superior da interface.
25. Aguarde a abertura do painel
- Uma nova área será exibida na parte inferior da tela.
26. Clique em "Open in xxxxx environment"
- Ao abrir o painel, clique no botão “Open in xxxxx environment” para iniciar o terminal.

27. Aguarde o terminal carregar
- O terminal será exibido, como mostrado na imagem abaixo, pronto para uso.
<img width="665" height="255" alt="image" src="https://github.com/user-attachments/assets/219eae4c-d2b4-42cb-9733-0e43fc317ea9" />

# Passo 5: Editar o Arquivo “pedidos_import.json
28. Verifique se o terminal está visível
o Após os passos anteriores, o shell do terminal já deve estar aberto na parte
inferior da tela.

29. Faça o download do arquivo pedidos_import.json
```
{
  "Pedido": [
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U001"},
          "Data do Pedido": {"S": "2025-10-01"},
          "IDPedido": {"S": "P1001"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "120"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Acácias"},
              "numero": {"S": "123"},
              "bairro": {"S": "Jardim Florido"},
              "cidade": {"S": "São Paulo"},
              "estado": {"S": "SP"},
              "tags": {"L": [{"S": "residencial"}, {"S": "entrega"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U001"},
          "Data do Pedido": {"S": "2025-10-05"},
          "IDPedido": {"S": "P1002"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "60"},
          "Endereco": {
            "M": {
              "rua": {"S": "Av. dos Jacarandás"},
              "numero": {"S": "456"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Campinas"},
              "estado": {"S": "SP"},
              "tags": {"L": [{"S": "comercial"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U002"},
          "Data do Pedido": {"S": "2025-09-25"},
          "IDPedido": {"S": "P1003"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "200"},
          "Endereco": {
            "M": {
              "rua": {"S": "Travessa das Rosas"},
              "numero": {"S": "789"},
              "bairro": {"S": "Alto Alegre"},
              "cidade": {"S": "Curitiba"},
              "estado": {"S": "PR"},
              "tags": {"L": [{"S": "residencial"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U001"},
          "Data do Pedido": {"S": "2025-09-20"},
          "IDPedido": {"S": "P1004"},
          "Status": {"S": "Cancelado"},
          "ValorTotal": {"N": "80"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Palmeiras"},
              "numero": {"S": "321"},
              "bairro": {"S": "Bela Vista"},
              "cidade": {"S": "Rio de Janeiro"},
              "estado": {"S": "RJ"},
              "tags": {"L": [{"S": "residencial"}, {"S": "cliente novo"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U003"},
          "Data do Pedido": {"S": "2025-10-01"},
          "IDPedido": {"S": "P1005"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "150"},
          "Endereco": {
            "M": {
              "rua": {"S": "Av. Brasil"},
              "numero": {"S": "654"},
              "bairro": {"S": "Boa Esperança"},
              "cidade": {"S": "Belo Horizonte"},
              "estado": {"S": "MG"},
              "tags": {"L": [{"S": "entrega rápida"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U004"},
          "Data do Pedido": {"S": "2025-10-01"},
          "IDPedido": {"S": "P1006"},
          "Status": {"S": "Cancelado"},
          "ValorTotal": {"N": "180"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua do Sol"},
              "numero": {"S": "987"},
              "bairro": {"S": "Vila Nova"},
              "cidade": {"S": "Salvador"},
              "estado": {"S": "BA"},
              "tags": {"L": [{"S": "reentrega"}, {"S": "prioridade baixa"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U005"},
          "Data do Pedido": {"S": "2025-11-10"},
          "IDPedido": {"S": "P1007"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "95"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Paulista"},
              "numero": {"S": "1001"},
              "bairro": {"S": "Bela Vista"},
              "cidade": {"S": "São Paulo"},
              "estado": {"S": "SP"},
              "tags": {"L": [{"S": "urgente"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U006"},
          "Data do Pedido": {"S": "2025-11-15"},
          "IDPedido": {"S": "P1008"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "210"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua da Praia"},
              "numero": {"S": "200"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Florianópolis"},
              "estado": {"S": "SC"},
              "tags": {"L": [{"S": "fragil"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U007"},
          "Data do Pedido": {"S": "2025-09-20"},
          "IDPedido": {"S": "P1009"},
          "Status": {"S": "Cancelado"},
          "ValorTotal": {"N": "75"},
          "Endereco": {
            "M": {
              "rua": {"S": "Alameda Santos"},
              "numero": {"S": "500"},
              "bairro": {"S": "Jardins"},
              "cidade": {"S": "São Paulo"},
              "estado": {"S": "SP"},
              "tags": {"L": [{"S": "devolução"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U008"},
          "Data do Pedido": {"S": "2025-12-25"},
          "IDPedido": {"S": "P1010"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "300"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Flores"},
              "numero": {"S": "33"},
              "bairro": {"S": "Copacabana"},
              "cidade": {"S": "Rio de Janeiro"},
              "estado": {"S": "RJ"},
              "tags": {"L": [{"S": "natal"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U009"},
          "Data do Pedido": {"S": "2025-07-03"},
          "IDPedido": {"S": "P1011"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "150"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Brasil"},
              "numero": {"S": "1500"},
              "bairro": {"S": "Funcionários"},
              "cidade": {"S": "Belo Horizonte"},
              "estado": {"S": "MG"},
              "tags": {"L": [{"S": "grande porte"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U010"},
          "Data do Pedido": {"S": "2025-08-10"},
          "IDPedido": {"S": "P1012"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "85"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Palmeiras"},
              "numero": {"S": "77"},
              "bairro": {"S": "Barra"},
              "cidade": {"S": "Salvador"},
              "estado": {"S": "BA"},
              "tags": {"L": [{"S": "frágil"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U011"},
          "Data do Pedido": {"S": "2025-05-12"},
          "IDPedido": {"S": "P1013"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "220"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Acácias"},
              "numero": {"S": "45"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Porto Alegre"},
              "estado": {"S": "RS"},
              "tags": {"L": [{"S": "urgente"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U012"},
          "Data do Pedido": {"S": "2025-06-18"},
          "IDPedido": {"S": "P1014"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "130"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Rio Branco"},
              "numero": {"S": "100"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Vitória"},
              "estado": {"S": "ES"},
              "tags": {"L": [{"S": "presente"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U013"},
          "Data do Pedido": {"S": "2025-07-22"},
          "IDPedido": {"S": "P1015"},
          "Status": {"S": "Cancelado"},
          "ValorTotal": {"N": "65"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Flores"},
              "numero": {"S": "88"},
              "bairro": {"S": "Jardim América"},
              "cidade": {"S": "Goiânia"},
              "estado": {"S": "GO"},
              "tags": {"L": [{"S": "devolução"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U014"},
          "Data do Pedido": {"S": "2025-09-30"},
          "IDPedido": {"S": "P1016"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "275"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Beira Rio"},
              "numero": {"S": "300"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Manaus"},
              "estado": {"S": "AM"},
              "tags": {"L": [{"S": "fragil"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U015"},
          "Data do Pedido": {"S": "2025-02-14"},
          "IDPedido": {"S": "P1017"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "180"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua dos Coqueiros"},
              "numero": {"S": "22"},
              "bairro": {"S": "Praia Grande"},
              "cidade": {"S": "Maceió"},
              "estado": {"S": "AL"},
              "tags": {"L": [{"S": "presente"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U016"},
          "Data do Pedido": {"S": "2025-04-05"},
          "IDPedido": {"S": "P1018"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "90"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Central"},
              "numero": {"S": "1234"},
              "bairro": {"S": "Industrial"},
              "cidade": {"S": "Cuiabá"},
              "estado": {"S": "MT"},
              "tags": {"L": [{"S": "urgente"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U017"},
          "Data do Pedido": {"S": "2025-08-19"},
          "IDPedido": {"S": "P1019"},
          "Status": {"S": "Cancelado"},
          "ValorTotal": {"N": "45"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Palmeiras"},
              "numero": {"S": "99"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Teresina"},
              "estado": {"S": "PI"},
              "tags": {"L": [{"S": "devolução"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U018"},
          "Data do Pedido": {"S": "2025-03-25"},
          "IDPedido": {"S": "P1020"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "160"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida das Américas"},
              "numero": {"S": "2000"},
              "bairro": {"S": "Barra da Tijuca"},
              "cidade": {"S": "Rio de Janeiro"},
              "estado": {"S": "RJ"},
              "tags": {"L": [{"S": "presente"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U019"},
          "Data do Pedido": {"S": "2025-11-11"},
          "IDPedido": {"S": "P1021"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "320"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Acácias"},
              "numero": {"S": "55"},
              "bairro": {"S": "Jardim Botânico"},
              "cidade": {"S": "Curitiba"},
              "estado": {"S": "PR"},
              "tags": {"L": [{"S": "grande porte"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U020"},
          "Data do Pedido": {"S": "2025-12-31"},
          "IDPedido": {"S": "P1022"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "199"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Beira-Mar"},
              "numero": {"S": "2025"},
              "bairro": {"S": "Praia do Futuro"},
              "cidade": {"S": "Fortaleza"},
              "estado": {"S": "CE"},
              "tags": {"L": [{"S": "réveillon"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U021"},
          "Data do Pedido": {"S": "2025-01-07"},
          "IDPedido": {"S": "P1023"},
          "Status": {"S": "Pendente"},
          "ValorTotal": {"N": "110"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Orquídeas"},
              "numero": {"S": "15"},
              "bairro": {"S": "Centro"},
              "cidade": {"S": "Natal"},
              "estado": {"S": "RN"},
              "tags": {"L": [{"S": "promoção"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U022"},
          "Data do Pedido": {"S": "2025-05-14"},
          "IDPedido": {"S": "P1024"},
          "Status": {"S": "Cancelado"},
          "ValorTotal": {"N": "70"},
          "Endereco": {
            "M": {
              "rua": {"S": "Avenida Principal"},
              "numero": {"S": "700"},
              "bairro": {"S": "Vila Nova"},
              "cidade": {"S": "Campo Grande"},
              "estado": {"S": "MS"},
              "tags": {"L": [{"S": "devolução"}]}
            }
          }
        }
      }
    },
    {
      "PutRequest": {
        "Item": {
          "ID do Usuário": {"S": "U023"},
          "Data do Pedido": {"S": "2025-10-22"},
          "IDPedido": {"S": "P1025"},
          "Status": {"S": "Entregue"},
          "ValorTotal": {"N": "250"},
          "Endereco": {
            "M": {
              "rua": {"S": "Rua das Margaridas"},
              "numero": {"S": "88"},
              "bairro": {"S": "Jardim das Flores"},
              "cidade": {"S": "João Pessoa"},
              "estado": {"S": "PB"},
              "tags": {"L": [{"S": "presente"}]}
            }
          }
        }
      }
    }
  ]
}
```
30. Abra o arquivo para edição
- Localize e abra o arquivo em um editor de texto.
31. Atualize o nome da tabela
- Na primeira linha, substitua o nome "Pedido" pelo nome da sua tabela criada no passo 1.
32. Salve o arquivo
- Após a alteração, salve o arquivo — como mostrado na imagem abaixo.

<img width="697" height="120" alt="image" src="https://github.com/user-attachments/assets/b17ca1c2-5ef7-43cc-a171-ac0d443399fd" />

# Passo 6: Fazer Upload do Arquivo para a AWS via CloudShell
33. Clique no botão “Ações”
- No CloudShell, clique em “Ações” no canto superior direito do terminal.
34. Selecione “Carregar arquivo”
 Escolha a opção “Carregar arquivo” no menu.
35. Localize o arquivo no seu computador
- Navegue até a pasta onde o arquivo pedidos_import.json foi salvo e selecione-o para upload.

36. Verifique se o upload foi concluído com sucesso
- No terminal do CloudShell, digite o comando abaixo para listar os arquivos na pasta atual:
- ls
- Se o arquivo aparecer na listagem, o upload foi realizado com sucesso.
<img width="675" height="175" alt="image" src="https://github.com/user-attachments/assets/67465115-5f0e-4e4a-a36e-4d8d3929f98d" />
<img width="1276" height="751" alt="image" src="https://github.com/user-attachments/assets/e2d17417-6a2e-4558-8251-c7c212b45c31" />


# Passo 7: Importar os Dados para o DynamoDB
37. Execute o comando no terminal do CloudShell
38. No terminal, digite o comando abaixo para importar os dados do arquivo para sua tabela no DynamoDB:
- aws dynamodb batch-write-item --request-items
file://pedidos_import.json
39. Confira a imagem de exemplo abaixo
- Se tiver dúvidas, utilize a imagem como referência para confirmar se o comando foi digitado corretamente.

<img width="669" height="43" alt="image" src="https://github.com/user-attachments/assets/d15c0955-418e-4ed5-9fe3-279cef49013b" />

40. Confirme a importação dos Dados
- Após executar o comando, uma mensagem de retorno será exibida no terminal — semelhante à mostrada na imagem abaixo.
<img width="667" height="134" alt="image" src="https://github.com/user-attachments/assets/de9146eb-c14a-48fb-ae4e-3addc5bd0c7f" />
<img width="1264" height="832" alt="image" src="https://github.com/user-attachments/assets/b569d3e7-94e8-4c83-93c7-fc3a45156b7a" />

# Passo 8: Verificar os itens na Tabela DynamoDB
41. Confirme o sucesso da importação
o A mensagem exibida no terminal indica que o processo foi concluído com
sucesso. Podemos seguir em frente.

42. Atualize a visualização da tabela no console da AWS
- No console do DynamoDB, clique no botão “Atualizar” na seção da sua tabela.

43. Confira os itens inseridos
- No campo “Tabela: Pedido-seunome – Itens retornados”, os dados carregados devem aparecer conforme mostrado na imagem abaixo.
<img width="682" height="274" alt="image" src="https://github.com/user-attachments/assets/729020b3-7884-4e51-bcbe-a142ed69f983" />
<img width="1232" height="754" alt="image" src="https://github.com/user-attachments/assets/d037f925-5389-4091-ad59-2143c4a77c75" />

# Passo 9: Realizar Busca pelo Campo “Verificar”
44. Selecione a aba “Verificar”
- No console do DynamoDB, vá até a aba “Verificar”.
45. Acesse os filtros
o Marque a caixa “verificar” e clique em “Filtros - opcional”.
46. Configure os critérios da busca
- No campo “Nome do atributo”, selecione: ID do Usuario
- No campo “Condição”, selecione: Igual a
- No campo “Tipo”, selecione: String
- No campo “Valor”, digite: U001

<img width="1229" height="776" alt="image" src="https://github.com/user-attachments/assets/44c2aaa6-e1a3-4bab-9a50-b2f5efaee8a5" />

47. Execute a busca
- Clique no botão “Executar”.
48. Visualize o resultado
- Uma mensagem com o resultado da busca será exibida, semelhante à imagem abaixo.
<img width="609" height="313" alt="image" src="https://github.com/user-attachments/assets/5205fafa-04e9-466a-af76-4a662b8768f9" />

#### **Atenção:**
Observação sobre a Busca por “Verificar”
- A busca pelo campo “Verificar” percorre todos os itens da tabela, o que consome mais recursos e processamento.
- Na imagem abaixo, repare o destaque em verde com a informação “Eficiência: 11,54%” — isso indica baixo aproveitamento da operação.

# Passo 10: Realizar Busca pelo Campo “Consulta”
49. Selecione a aba “Consulta”
- Marque a caixa “Consulta” no console da tabela.
50. Informe o critério de busca
- No campo “Chave de partição: ID do Usuario”, digite: U001
- Não é preciso preencher a Chave de classificação: Data do Pedido e nem a opção Filtros-opcional
51. Execute a consulta
- Clique em “Executar” para visualizar os resultados.
52. Confira o retorno
- O sistema exibirá os itens relacionados ao ID informado — conforme mostrado na imagem abaixo.

<img width="608" height="300" alt="image" src="https://github.com/user-attachments/assets/0e88058d-2147-45fa-88c5-032120cfe68e" />
<img width="1208" height="772" alt="image" src="https://github.com/user-attachments/assets/ecac6062-05f7-439d-9ebf-81ab35e69077" />
<img width="1247" height="636" alt="image" src="https://github.com/user-attachments/assets/01fd3506-8eb5-4661-86cf-40404e134b31" />

Atenção:
Observação sobre a Busca por “Consulta”
- Eficiência elevada:
- A busca via “Consulta” é mais eficiente, pois retorna apenas os dados que correspondem aos critérios definidos.
- Na imagem abaixo, veja o destaque em verde com “Eficiência: 100%”, indicando excelente aproveitamento dos recursos.

# Passo 11: Consulta Usando um índice (LSI)
53. Selecione o índice desejado
- No campo “Selecionar uma tabela ou índice”, escolha: índice - LSI- PedidoseunomeStatus

54. Informe os critérios da consulta
- Chave de partição: ID do Usuario → digite: U001
- Selecionar projeção de atributos → manter “Todos os atributos”
- Chave de classificação: Status
- Selecione: “Igual a” no campo ao lado, digite: Entregue

<img width="1256" height="714" alt="image" src="https://github.com/user-attachments/assets/0adaf63e-603b-4ad2-a16c-ff2f48df86f9" />

55. Execute a consulta
- Clique em “Executar”.
56. Visualize os resultados
- Os itens com status “Entregue” para o usuário U001 serão exibidos, conforme mostrado na imagem abaixo.

<img width="607" height="408" alt="image" src="https://github.com/user-attachments/assets/6658fead-7b5b-4e1c-a777-f4f868df0e52" />
<img width="1253" height="752" alt="image" src="https://github.com/user-attachments/assets/40c05d5c-78dd-42a8-b94b-19b3bab10cb5" />

Atenção:
Observação sobre a Consulta com Índice

- Alta eficiência:
- Assim como a consulta anterior, essa busca é feita com base em chave de partição e filtros específicos, o que garante eficiência de 100%, como destacado em verde na imagem abaixo.

# Passo 12: Consulta com Filtro Adicional Usando um índice
57. Selecione o índice
- No campo “Selecionar uma tabela ou índice”, escolha:
- índice - LSI-PedidoseunomeStatus
58. Preencha a chave de partição
- No campo “Chave de partição: ID do usuário”, digite:
- U001
- Não é preciso preencher o campo “Chave de classificação: Status”. Caso esteja preenchido o valor como “Entregue” é só deletá-lo.

59. Adicionar filtro por valor total
- Clique em “Filtros – opcional”
- Selecione “Adicionar filtro” e preencha os campos:
- Nome do atributo: ValorTotal
- Condição: Maior que ou igual a
- Tipo: Número
- Valor: 80

<img width="1228" height="814" alt="image" src="https://github.com/user-attachments/assets/3d558841-ac07-44ae-92d0-2206c124405a" />
<img width="1199" height="729" alt="image" src="https://github.com/user-attachments/assets/d9d8ea2b-7a5d-44e0-a49f-b4c340cc64cd" />

60. Execute a consulta
- Clique em “Executar”.
61. Verifique os resultados
- Os itens filtrados serão exibidos na tela, como ilustrado na imagem abaixo.

<img width="759" height="405" alt="image" src="https://github.com/user-attachments/assets/c6ed8b47-f515-46f0-9b78-342efc68b8e8" />

# Passo 13: Criar um índice Global (GLI)
62. Acesse as tabelas no console do DynamoDB
- No menu lateral do DynamoDB, clique em “Tabelas”.
63. Selecione sua tabela
- Na lista de tabelas, clique no nome da tabela que você criou anteriormente — conforme mostrado na imagem abaixo.

<img width="766" height="164" alt="image" src="https://github.com/user-attachments/assets/e054d216-90c8-4c10-bdd9-5e6881fc0773" />

64. Acesse a aba “Índices” da tabela
- Com a tabela aberta, clique na aba “Índices”.
65. Iniciar a criação do índice
- Na seção “Índices secundários globais”, clique em “Criar índice”.

<img width="1264" height="678" alt="image" src="https://github.com/user-attachments/assets/f30cbd41-d33f-4627-9778-802d69345546" />

66. Preencha os campos do novo índice
- Chave de partição: Status
- Tipo de dado: String
- Chave de classificação (opcional): ValorTotal
- Tipo de dado: Número
- Nome do índice: GLI-Status-ValorTotal
67. Finalizar criação do índice
- Não altere nenhuma outra configuração.
- Role até o final da página e clique no botão “Criar índice”.

<img width="1251" height="589" alt="image" src="https://github.com/user-attachments/assets/e57fd2ab-9bc2-4a95-8ba2-3151e9230d03" />

68. Aguarde a criação
- O processo pode levar cerca de 10 minutos para ser concluído — veja o exemplo na imagem abaixo.

<img width="755" height="252" alt="image" src="https://github.com/user-attachments/assets/a6e999ff-c2c1-4df3-8b0e-50a60db9cf89" />
<img width="666" height="656" alt="image" src="https://github.com/user-attachments/assets/adb55ab5-7ddd-4bd7-ad86-a556fd095ca5" />

Passo 14: Acessar e Visualizar o índice Secundário Global
69. Clique em “Explorar itens da tabela”
- No console do DynamoDB, com a tabela ainda selecionada, clique no botão
“Explorar itens da tabela”.
70. Selecione o índice recém-criado
- No campo “Selecionar uma tabela ou índice”, você já poderá visualizar o índice GLI-Status-ValorTotal criado anteriormente.
<img width="664" height="561" alt="image" src="https://github.com/user-attachments/assets/4bbfa2bc-2ff2-4f4d-8ffb-b600513c1e3b" />

71. Confirme a seleção
- Após selecionar o índice, os dados relacionados começarão a ser exibidos — conforme mostrado na imagem abaixo.

<img width="756" height="118" alt="image" src="https://github.com/user-attachments/assets/e3d4ecd9-a1a1-42a3-9029-f763188a59ed" />

# Passo 15: Realizar Consulta no índice Global com Parâmetros Definidos
72. Preencha os critérios de consulta
- Chave de partição: digite Entregue
- Chave de classificação:
- Selecione “Maior que ou igual a”
- No campo ao lado, digite 60
73. Remova filtros opcionais
- Clique no botão “Remover” dentro da seção “Filtros – opcional”, caso exista algum filtro configurado anteriormente.
- Isso garante que os filtros não interfiram na nova busca.
74. Execute a consulta
- Clique em “Executar” para visualizar os resultados com base nos parâmetros definidos.
75. Confira a resposta
- Os dados serão exibidos conforme mostrado na imagem abaixo, com desempenho otimizado devido ao uso direto das chaves do índice.

<img width="756" height="351" alt="image" src="https://github.com/user-attachments/assets/63afac84-92c9-4aee-8680-98e50b87b194" />
<img width="629" height="778" alt="image" src="https://github.com/user-attachments/assets/a9d69887-6f09-422c-88d3-a6435a77b9ce" />

-  Após clicar em “Executar”, verifique se:
76. Os dados exibidos correspondem aos critérios definidos
- Os registros com Status “Entregue” e ValorTotal maior ou igual a 60 devem ser listados.
<img width="674" height="665" alt="image" src="https://github.com/user-attachments/assets/dcb129a7-39b6-4f84-a490-26ab65425777" />

77. A mensagem de eficiência aparece em verde
- Observe se há uma mensagem em destaque verde, indicando a eficiência da consulta e confirmando que os recursos foram bem utilizados — como mostrado na imagem abaixo.

<img width="756" height="375" alt="image" src="https://github.com/user-attachments/assets/5eb745e3-f559-4955-b741-40d1c618d32d" />

# Passo 16: Encerramento do Laboratório - Excluir os Recursos Criados
78. Parabéns por concluir o laboratório! Agora, vamos excluir todos os recursos criados para evitar cobranças desnecessárias e manter o ambiente limpo.
79. Excluir o Índice Secundário Global (GSI)
- No menu lateral do DynamoDB, clique em “Tabelas”.
- Selecione o nome da sua tabela criada durante o laboratório.
- Acesse a aba “Índices”.

80. Em “Índices secundários globais”, localize o índice criado (GLI-Status-ValorTotal), clique em “Excluir”.

81. Confirme a exclusão:
- Digite excluir no campo solicitado.
- Clique no botão “Excluir índice”.
- Consulte a imagem abaixo como referência para garantir que os passos estão corretos.

<img width="757" height="210" alt="image" src="https://github.com/user-attachments/assets/cc97287b-eb10-459a-ab33-309f6dce5a76" />

82. Excluir a Tabela Principal
- Agora vamos excluir a tabela criada durante o laboratório.
- No menu lateral do DynamoDB, clique em “Tabelas”.
- Na lista exibida, selecione o nome da sua tabela.
- Com a tabela aberta, clique no botão “Excluir”.
83. Para confirmar a exclusão:
- Digite confirmar no campo solicitado.
- Clique no botão “Excluir”.
- Consulte a imagem abaixo para conferir se os passos estão corretos.
- Parabéns! Agora sim você finalizou todo o processo: da criação à limpeza do ambiente.

<img width="762" height="651" alt="image" src="https://github.com/user-attachments/assets/9c0281df-80d5-4520-88dc-f9a0033b0fc8" />

84. Após excluir o índice e a tabela:
85. Volte ao menu lateral do DynamoDB e clique novamente em “Tabelas”.
- Verifique se nenhuma tabela relacionada ao laboratório aparece na lista.
- Clique também na aba “Índices” (caso disponível) para garantir que nenhum índice secundário global ainda está ativo.
- Essa verificação é essencial para garantir que nenhum recurso fique ativo e evitar cobranças indevidas.

Laboratório Finalizado com Sucesso!

86. Parabéns! Você criou, manipulou e consultou dados em uma tabela do DynamoDB, utilizando índices locais e globais, e finalizou tudo com a limpeza correta dos recursos.


> stay focused!


