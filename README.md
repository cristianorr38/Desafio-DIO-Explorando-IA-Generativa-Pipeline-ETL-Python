# Desafio DIO - Explorando IA Generativa em um Pipeline de ETL com Python

![Python](https://img.shields.io/badge/Python-3.10-blue.svg)
![Google Colab](https://img.shields.io/badge/Google%20Colab-Ready-orange.svg)
![ETL](https://img.shields.io/badge/ETL-Extract--Transform--Load-lightgrey.svg)
![Build](https://img.shields.io/badge/build-passing-success)
[![GitHub Repo Size](https://img.shields.io/github/repo-size/cristianorr38/Desafio-DIO-Explorando-IA-Generativa-Pipeline-ETL-Python)](https://github.com/cristianorr38/Desafio-DIO-Explorando-IA-Generativa-Pipeline-ETL-Python)
[![GitHub Stars](https://img.shields.io/github/stars/cristianorr38/Desafio-DIO-Explorando-IA-Generativa-Pipeline-ETL-Python?style=social)](https://github.com/cristianorr38/Desafio-DIO-Explorando-IA-Generativa-Pipeline-ETL-Python)
![Status](https://img.shields.io/badge/status-active-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue)

---

## 🎯 Objetivo
Replicar um projeto prático de **Ciência de Dados** e **Python**, explorando o fluxo **ETL (Extração, Transformação e Carregamento)**.  
O foco é compreender como os dados fluem entre as etapas e aplicar **IA Generativa** para enriquecer o processo, criando mensagens personalizadas a partir dos dados tratados.

---

## 🏆 Etapas do Desafio
1. **Extração**: Obter dados de uma fonte (API, lista em Python ou arquivo CSV).  
2. **Transformação**: Processar os dados, aplicar regras de negócio e gerar mensagens com IA.  
3. **Carregamento**: Salvar os resultados em um novo arquivo ou base de dados.  
4. **Validação**: Conferir se o pipeline funciona de ponta a ponta.  
5. **Documentação**: Registrar resultados e conclusões no repositório.  

---

## 📋 Diretrizes
- O projeto deve ser desenvolvido em **Google Colab**.  
- O código deve ser escrito em **Python 3.x**.  
- Caso a API esteja indisponível, utilizar alternativas:  
  - Criar lista fictícia de usuários diretamente no código.  
  - Utilizar arquivo CSV com colunas `Nome`, `Conta`, `Cartão`.  
- Garantir que o pipeline seja **reprodutível** e bem documentado.  
- Utilizar boas práticas de programação e organização de notebooks.  

---

## ✅ Resultado Esperado
- Notebook funcional no Google Colab.  
- Pipeline ETL implementado e documentado.  
- Mensagens geradas pela IA a partir dos dados.  
- Arquivo final com os resultados carregados.  

---

## 📦 Requisitos
- Conta Google para acesso ao Colab.  
- Conhecimento básico em Python e bibliotecas:  
  - `pandas`, `numpy`, `matplotlib`, `seaborn`  
- Dataset em formato `.csv` ou lista de dados fictícios.  
- Conexão com internet para execução no Colab.  

---

## 📂 Estrutura do Projeto
```bash
etl-ia-generativa/
│
├── data/
│   └── dados_clientes.csv
│
├── notebooks/
│   └── Pipeline_de_ETL_com_Python_e_IA_Generativa.ipynb
│
├── scripts/
│   ├── Script Python Consolidado - ETL.txt
│   └── pipeline_de_etl_com_python_e_ia_generativa.py
│
├── results/
│   └── dados_clientes_atualizado.csv
│
└── README.md
```

---

🛣️ Roadmap do Projeto


---

📌 Release

Versão 1.0.0

- Estrutura inicial criada.

- Notebook configurado no Google Colab.

- Dataset fictício importado e explorado.


---

🖥️ Script Python Consolidado

```script

# Atribuir o caminho do arquivo CSV para uma variável
dados_clientes = "/content/dados_clientes.csv"

# ==============================================================================
# 1. Extração de Dados
# ==============================================================================

# Importa a biblioteca pandas para manipulação e análise de dados.
import pandas as pd

# Carrega o arquivo CSV "dados_clientes.csv" em um DataFrame do pandas.
df = pd.read_csv(dados_clientes)

# Extrai todos os IDs de clientes do DataFrame e os armazena em uma lista.
user_ids = df['ID'].tolist()

# Imprime a lista de IDs de usuários.
print("IDs dos usuários extraídos:", user_ids)

# Importa as bibliotecas necessárias para manipulação de JSON, números e avaliação literal de strings.
import json
import numpy as np
import ast # Importa o módulo 'ast' para avaliar strings com estruturas Python

# Define uma função para obter os dados de um usuário pelo ID.
def get_user(id):
  # Filtra o DataFrame para encontrar o usuário com o ID fornecido.
  user_data = df[df['ID'] == id]
  # Verifica se o usuário foi encontrado.
  if not user_data.empty:
    # Converte os dados do usuário para um dicionário.
    user_dict = user_data.iloc[0].to_dict()

    # Tenta converter a string 'News' para uma lista se ela existir e for uma string.
    if isinstance(user_dict.get('News'), str):
      try:
        user_dict['News'] = ast.literal_eval(user_dict['News'])
      except (ValueError, SyntaxError):
        # Se houver erro na avaliação ou se não for uma lista válida, inicializa como vazia.
        user_dict['News'] = []
    # Se o campo 'News' for NaN (ausente) ou ainda não for uma lista, inicializa como uma lista vazia.
    elif pd.isna(user_dict.get('News')) or not isinstance(user_dict.get('News'), list):
      user_dict['News'] = []
    return user_dict
  # Retorna None se o usuário não for encontrado.
  return None

# Cria uma lista de dicionários, onde cada dicionário contém os dados de um usuário.
# A função get_user é chamada para cada ID, e usuários válidos são adicionados à lista.
users_data = [user for id in user_ids if (user := get_user(id)) is not None]

# Imprime os dados de todos os usuários em formato JSON indentado para melhor legibilidade.
print("\nDados dos usuários após extração e pré-processamento:")
print(json.dumps(users_data, indent=2))

# ==============================================================================
# 2. Transformação de Dados (Configuração e Geração de Notícias com Gemini)
# ==============================================================================

# Importa a biblioteca google.generativeai para interagir com o Gemini API.
import google.generativeai as genai
# Importa a função userdata do google.colab para acessar segredos.
from google.colab import userdata

# Obtém a chave da API Gemini armazenada nos segredos do Colab.
GOOGLE_API_KEY=userdata.get('api_key_gemini')
# Configura a API Gemini com a chave obtida.
genai.configure(api_key=GOOGLE_API_KEY)

# Lista todos os modelos disponíveis na API Gemini (opcional, para verificação).
print("\nModelos Gemini disponíveis que suportam generateContent:")
for m in genai.list_models():
  # Verifica se o modelo suporta o método 'generateContent' e, se sim, imprime seu nome.
  if 'generateContent' in m.supported_generation_methods:
    print(m.name)

# Inicializa o modelo generativo 'gemma-3-1b-it' da API Gemini.
gemini_model = genai.GenerativeModel('gemma-3-1b-it')

# Define uma função para gerar uma mensagem de notícia personalizada usando o modelo Gemini.
def generate_ai_news(user_info):

  # Cria a mensagem de prompt para o modelo, incluindo o nome do usuário e o tópico.
  prompt_message = f"Crie uma mensagem para {user_info['Nome']} sobre a importância dos investimentos (máximo de 100 caracteres)"

  # Gera conteúdo (notícia) usando o modelo Gemini com o prompt definido.
  response = gemini_model.generate_content(prompt_message)

  # Extrai o texto da resposta do modelo e remove as aspas extras, se houver.
  responseChatGPT = response.text.strip('"')
  return responseChatGPT

print("\nGerando e adicionando notícias aos usuários:")
# Itera sobre cada item (usuário) na lista de dados dos usuários.
for user_item in users_data:
  # Gera uma notícia de IA para o usuário atual.
  news = generate_ai_news(user_item)
  # Imprime a notícia gerada.
  print(f"Notícia para {user_item['Nome']}: {news}")

  # Adiciona a notícia gerada à lista de notícias do usuário, com um ícone padrão.
  user_item['News'].append({
      "icon": "https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/credit.svg",
      "description": news
  })

# Imprime os dados atualizados de todos os usuários em formato JSON indentado para melhor legibilidade.
print("\nDados dos usuários com notícias de IA adicionadas:")
print(json.dumps(users_data, indent=2))

# ==============================================================================
# 3. Carga de Dados
# ==============================================================================

# Cria um novo DataFrame com os IDs dos usuários e as notícias atualizadas.
# Isso é feito transformando a lista users_data em uma lista de dicionários para o DataFrame.
updated_news_df = pd.DataFrame([{'ID': user['ID'], 'News': user['News']} for user in users_data])

# Mescla o DataFrame original (df) com o DataFrame de notícias atualizadas (updated_news_df).
# A mesclagem é feita com base na coluna 'ID', usando um 'left merge' para manter todos os usuários do df original.
# O sufixo '' é usado para evitar conflitos de nomes de colunas, e os nomes de colunas '_old' são para indicar que são os dados antigos.
df = df.merge(updated_news_df, on='ID', how='left', suffixes=('_old', ''))

# Verifica se a coluna 'News_old' existe (que seria a coluna 'News' original antes da mesclagem).
if 'News_old' in df.columns:
    # Se existir, remove a coluna 'News_old' para manter apenas a coluna 'News' atualizada.
    df = df.drop(columns=['News_old'])

# Exibe as primeiras 5 linhas do DataFrame resultante para verificar as atualizações.
print("\nDataFrame final com notícias atualizadas (primeiras 5 linhas):")
# Usar print(df.head()) ou display(df.head()) em ambiente de notebook
print(df.head().to_markdown(index=False))

# Define o caminho onde o novo arquivo CSV com os dados atualizados dos clientes será salvo.
dados_clientes_atualizado = "/content/dados_clientes_atualizado.csv"
# Imprime o caminho completo onde o arquivo será salvo para confirmação.
print(f"\nNovo arquivo de clientes atualizados será salvo em: {dados_clientes_atualizado}")

# Salva o DataFrame 'df' (com os dados dos clientes atualizados) em um novo arquivo CSV.
# 'index=False' impede que o índice do DataFrame seja salvo como uma coluna no CSV.
df.to_csv(dados_clientes_atualizado, index=False)

# Imprime uma mensagem de sucesso, confirmando que os dados foram salvos e informando o caminho do arquivo.
print(f"Dados atualizados com sucesso em {dados_clientes_atualizado}")

# Carregar o arquivo CSV atualizado de volta em um DataFrame para verificação.
df_verificacao = pd.read_csv(dados_clientes_atualizado)

# Exibir as primeiras 5 linhas do DataFrame carregado para confirmar que os dados foram salvos corretamente.
display(df_verificacao.head())

```

---

📸 Prints do Projeto

Google Colab Notebook

(Inserir aqui print da execução do notebook no Google Colab)
Dataset Utilizado

(Inserir aqui print das primeiras linhas do arquivo de dados)

---

📜 Licença



---
