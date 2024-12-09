# Projeto de Gráfico de Potenciômetro com API

## Descrição

Este projeto utiliza a API FIWARE para obter dados de luminosidade de um dispositivo e gerar um gráfico com o histórico de medições de um **potenciômetro**. O script permite que o usuário informe a quantidade de dados a ser recuperada, exibe um gráfico com os valores do potenciômetro ao longo do tempo e fornece uma visualização simples para análise.

## Requisitos

- Python 3.x
- Bibliotecas necessárias:
  - **requests** para fazer requisições HTTP.
  - **matplotlib** para criar gráficos.
  - **numpy** (opcional, pode ser usada para manipulação de dados, se necessário).
  
  Você pode instalar as bibliotecas necessárias com o comando:
  ```bash
  pip install requests matplotlib numpy
  ```

- Acesso a uma API FIWARE ou a um servidor que disponibilize os dados do potenciômetro.

## Funcionalidade

1. **Obter Dados da API**:
   - O código solicita ao usuário um valor **lastN** (entre 1 e 100) e utiliza esse valor para obter as últimas medições do potenciômetro a partir de uma API FIWARE.
   
2. **Criar Gráfico**:
   - Após recuperar os dados, o script gera um gráfico de linha utilizando a biblioteca `matplotlib`, exibindo os valores do potenciômetro ao longo do tempo.
   
3. **Exibição de Gráfico**:
   - O gráfico é exibido em uma janela separada, mostrando a relação entre o tempo e o valor do potenciômetro.

## Como Funciona

1. **Função `obter_dados_potentiometer`**:
   - Faz uma requisição HTTP GET para a API FIWARE, usando o URL configurado. A função recupera os dados de luminosidade (potenciômetro) e retorna uma lista de medições.
   
2. **Função `plotar_grafico`**:
   - Recebe os dados do potenciômetro e plota um gráfico de linha, com o tempo no eixo X e os valores do potenciômetro no eixo Y.
   
3. **Entrada do Usuário**:
   - O código solicita ao usuário um valor para `lastN`, que indica quantos dados de medições serão recuperados da API.

4. **Execução**:
   - O gráfico é gerado e exibido, com os dados obtidos da API.

## Como Usar

1. **Configurar a URL da API**:
   - Substitua `{{url}}` pelo endereço do servidor FIWARE ou pela URL do seu endpoint API.

2. **Executar o Script**:
   - Execute o script Python:
     ```bash
     python nome_do_arquivo.py
     ```

3. **Interação com o Usuário**:
   - O script pedirá para o usuário digitar um valor **lastN** entre 1 e 100, que será usado para obter as últimas medições.
   
4. **Visualizar o Gráfico**:
   - O gráfico será exibido automaticamente após a coleta dos dados. Ele mostrará a evolução dos valores do potenciômetro ao longo do tempo.

## Código

```python
import requests
import matplotlib.pyplot as plt
import numpy as np

# Função para obter os dados de luminosidade a partir da API
def obter_dados_potentiometer(lastN):
    url = f"http://{{url}}:8666/STH/v1/contextEntities/type/device/id/urn:ngsi-ld:device:001/attributes/potentiometer?lastN={lastN}"

    headers = {
        'fiware-service': 'smart',
        'fiware-servicepath': '/'
    }

    response = requests.get(url, headers=headers)

    if response.status_code == 200:
        data = response.json()
        luminosity_data = data['contextResponses'][0]['contextElement']['attributes'][0]['values']
        return luminosity_data
    else:
        print(f"Erro ao obter dados: {response.status_code}")
        return []

# Função para criar e exibir o gráfico
def plotar_grafico(potentiometer_data):
    if not potentiometer_data:
        print("Nenhum dado disponível para plotar.")
        return

    potentiometer = [entry['attrValue'] for entry in potentiometer_data]
    tempos = [entry['recvTime'] for entry in potentiometer_data]

    plt.figure(figsize=(12, 6))
    plt.plot(tempos, potentiometer, marker='o', linestyle='-', color='r')
    plt.title('Gráfico de mediçoes do potênciometro em função do Tempo')
    plt.xlabel('Tempo')
    plt.ylabel('Valores')
    plt.xticks(rotation=90)
    plt.grid(True)
    plt.tight_layout()
    plt.show()

# Solicitar ao usuário um valor "lastN" entre 1 e 100
while True:
    try:
        lastN = int(input("Digite um valor para lastN (entre 1 e 100): "))
        if 1 <= lastN <= 100:
            break
        else:
            print("O valor deve estar entre 1 e 100. Tente novamente.")
    except ValueError:
        print("Por favor, digite um número válido.")

# Obter os dados de luminosidade e plotar o gráfico
potentiometer_data = obter_dados_potentiometer(lastN)
plotar_grafico(potentiometer_data)
```

## Observações

- **API FIWARE**: Este código depende de um servidor FIWARE para fornecer os dados de luminosidade do potenciômetro. A URL da API precisa ser configurada corretamente.
- **Formato dos Dados**: O código assume que os dados retornados pela API estão no formato esperado. Se o formato mudar, será necessário ajustar o processamento dos dados.
- **Limite de Dados**: O valor de **lastN** controla o número de medições recuperadas da API, limitando a quantidade de dados processados e exibidos.

## Possíveis Melhorias

- **Exibição em Tempo Real**: Implementar a atualização do gráfico em tempo real à medida que novos dados forem recebidos.
- **Mais Variáveis**: Permitir a exibição de múltiplos gráficos ou adicionar mais variáveis a serem monitoradas na plataforma.
