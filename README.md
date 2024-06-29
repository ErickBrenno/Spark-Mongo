# Spark e MongoDB

## Introdução
Nessa documentação, abordaremos algumas funções do Spark e Utilizaremos o MongoDB para armazenamento de dados e consultas.

## Configuração do Ambiente
Nesta sessão, vamos configurar nosso ambiente, utilizando o Docker

### *Passo 1: Criação do Container Spark*
- Na base de pesquisa do Docker, procure por "jupyter/pyspark-notebook".
- Na configuração de portas, iremos utilizar a porta 1000 e daremos o nome de "jupyter-pyspark" para nosso container.

  ![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/135803f9-e8be-4224-bd71-fed693ec9515)


### *Passo 2: Criação do Container Mongo*
- Na base de pesquisa do Docker, procure por "Mongo".
- Na configuração de portas, iremos utilizar a porta 27015 e daremos o nome de "mongo" para nosso container.
  
  ![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/391cb477-f856-476d-8911-196b1f23e9a0)

### *Passo 3: Acessando Jupyter Notebook*
Após a criação dos dois Containers, conseguiremos acessar o Jupiter Notebook. Para encontrarmos a URL correta, seguiremos os seguintes passo:
- Clique no Container Jupyter no Docker.
- Em Logs, localize a URL que contem "127.0.0.1".
  
  ![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/6dd9e0a8-a26d-4d39-b12a-8018e7461727)
- Copie essa URL, cole em seu navegador, e altera a porta (que por default será 8888) para a porta que configuramos (10000)
  ![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/26a1d123-5085-4905-acd7-0f33569a739e)

  > Importe os dois arquivos disponibilizados no Canvas (sample_movielens_ratings e ExemploALS), para o Jupyter Notebook.

## Execução o Script Spark
Nessa sessão, executaremos o Script Spark (ExemploALS) abaixo, o descritivo das principais células: <br>

1º Importação das Bibliotecas
Nesta etapa, são trazidas todas as bibliotecas necessárias para criar o sistema de recomendação. Também se verifica a versão do Python para garantir a compatibilidade com a função long.

2º Configuração da Sessão Spark
Estabelece uma sessão Spark e configura a conexão com um banco de dados MongoDB.

3º Leitura e Processamento dos Dados
Carrega um arquivo de texto contendo avaliações de filmes e converte essas avaliações em um DataFrame do Spark.

4º Divisão dos Dados
Separa os dados em conjuntos de treinamento e teste, com proporções de 80% e 20%, respectivamente.

5º Treinamento do Modelo
Configura e treina um modelo de recomendação ALS (Alternating Least Squares) utilizando o conjunto de treinamento.

6º Avaliação do Modelo
Aplica o modelo treinado aos dados de teste e avalia a precisão do modelo usando o RMSE (Root Mean Square Error).

7º Recomendação para Usuários
Gera recomendações de filmes para todos os usuários e exibe as 10 primeiras recomendações.

8º Recomendação para Itens (Filmes)
Gera recomendações de usuários para todos os filmes e mostra as 20 primeiras recomendações.

9º Seleção de Recomendação por IDs de Filmes
Seleciona apenas os IDs dos filmes recomendados para os usuários.

10º Salvando as Recomendações no MongoDB
Armazena as recomendações geradas no banco de dados MongoDB.

Após a execução do Script, para treinarmos o Modelo e Iinserção dos Dados de MongoDB, podemos utilizar o MongoCompass para observarmos os dados.

![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/1f5ae70a-5c0a-4a12-af2b-5308de0efc60)

## Criando API
Para essa parte, itemos utilizar o arquivo disponibilizado no Canvas "api.zip", para criarmos uma API, utilzando a biclioteca Fast API. <br>
Para isso, vamos seguir alguns passos descritos logo abaixo.

### *1 - Instalando as Bibliotecas:*
- No arquivo "api.zip", existe um arquivo requirements.txt, iremos executar ele, para a instalação das bibliotecas necessarias.
  ```shell
  pip install -r requirements.txt
  ```
  ![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/0be82699-b612-4d4d-9bc7-3c6325387389)

### *2 - Executando o Script de Criação do API*
Este script cria uma API web usando FastAPI, conectando-se a um banco de dados MongoDB para fornecer recomendações personalizadas. A API possui duas rotas:

- Rota Padrão (/rec/v1):
Retorna uma mensagem indicando que a rota padrão foi acessada.

- Rota de Consulta de Recomendações (/rec/v2/{usuario}):
Recebe um ID de usuário como parâmetro e retorna recomendações personalizadas para esse usuário, obtidas do banco de dados MongoDB.
A aplicação é executada no servidor Uvicorn na porta 8000.

- Rota de Consulta de Multiplas Requesições ("/rec/v3/")
Esta função simplifica o processo de consulta de recomendações para vários usuários de uma vez, sendo útil para aplicações que precisam de eficiência ao lidar com múltiplas requisições de recomendação.
  
  ```shell
  from fastapi import FastAPI
  import uvicorn
  from bd import mongo
  from typing import List
  
  app = FastAPI()
  conexao = mongo.inicia_conexao()
  
  @app.get("/rec/v1")
  def rota_padrao():
      return {"Rota padrão": "Você acessou a rota default"}
  
  @app.get("/rec/v2/{usuario}")
  def consulta_rec(usuario: int):
      return {"usuario": usuario, "resultado_recs": mongo.consulta_recomendacoes(usuario, conexao)}
  
  @app.post("/rec/v3/")
  def consulta_multi_recs(usuarios: List[int]):
      results = {}
      for usuario in usuarios:
          recs = mongo.consulta_recomendacoes(usuario, conexao)
          # Extract only the movie IDs
          movie_ids = [rec['id'] for rec in recs]
          results[usuario] = movie_ids
      return results
  
  if __name__ == "__main__":
      uvicorn.run(app, host='localhost', port=8000)
  ```
  ![image](https://github.com/ErickBrenno/Spark-Mongo/assets/83048005/d0ac39bf-2e89-4c17-8658-9da93cb95f23)


### *3 - Executando Script de Conexão com MongoDB*
Este script Python utiliza o módulo pymongo para conectar e consultar um banco de dados MongoDB local. Ele define duas funções principais e executa uma consulta de exemplo:

- Função inicia_conexao():
  Estabelece uma conexão com o MongoDB local na porta 28017 e seleciona o banco de dados puc.
  A função retorna a coleção recomendacoes do banco de dados puc.

- Função consulta_recomendacoes(usuario, conexao):
  Recebe um ID de usuário (usuario) e uma conexão MongoDB (conexao) como parâmetros.
  Executa uma consulta na coleção recomendacoes para encontrar todas as recomendações associadas ao usuário especificado.
  Transforma os resultados da consulta em uma lista de dicionários, onde cada dicionário contém o id do filme recomendado e sua rating (classificação).

- Execução da Consulta de Exemplo:
  Chama a função inicia_conexao() para obter uma conexão com o MongoDB.
  Chama a função consulta_recomendacoes(28, conn) para buscar as recomendações para o usuário com ID 28.
  Imprime o resultado da consulta no formato de lista de dicionários contendo os IDs dos filmes e suas classificações.

  ```shell
  from pymongo import MongoClient

  def inicia_conexao():
  
      client = MongoClient()
      db = client['puc']
      col = db.recomendacoes
      return col
  
  def consulta_recomendacoes(usuario, conexao):
  
      recomendacoes = list(conexao.find({"userId": usuario}))
      list_rec = []
      for rec in recomendacoes:
          list_rec.append((rec['movieId'],rec['rating']))
  
      return {'Recomendações': list_rec}
  
    conn = inicia_conexao()
  ```

## *Testando a API*
Para esse teste, utilizaremos o endpoint "/rec/v3/", para executando multipas requisições:
