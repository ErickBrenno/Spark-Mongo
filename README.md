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
Nessa sessão, executaremos o Script Spark (ExemploALS) abaixo, o descritivo de cada célula: <br>

1ª - Importação das Bibliotecas
Nesta etapa, são trazidas todas as bibliotecas necessárias para criar o sistema de recomendação. Também se verifica a versão do Python para garantir a compatibilidade com a função long.

2ª - Configuração da Sessão Spark
Estabelece uma sessão Spark e configura a conexão com um banco de dados MongoDB.

3ª - Leitura e Processamento dos Dados
Carrega um arquivo de texto contendo avaliações de filmes e converte essas avaliações em um DataFrame do Spark.

4ª - Divisão dos Dados
Separa os dados em conjuntos de treinamento e teste, com proporções de 80% e 20%, respectivamente.

5ª - Treinamento do Modelo
Configura e treina um modelo de recomendação ALS (Alternating Least Squares) utilizando o conjunto de treinamento.

6ª Célula - Avaliação do Modelo
Aplica o modelo treinado aos dados de teste e avalia a precisão do modelo usando o RMSE (Root Mean Square Error).

7ª Célula - Recomendação para Usuários
Gera recomendações de filmes para todos os usuários e exibe as 10 primeiras recomendações.

8ª Célula - Recomendação para Itens (Filmes)
Gera recomendações de usuários para todos os filmes e mostra as 20 primeiras recomendações.

9ª Célula - Seleção de Recomendação por IDs de Filmes
Seleciona apenas os IDs dos filmes recomendados para os usuários.

10ª Célula - Salvando as Recomendações no MongoDB
Armazena as recomendações geradas no banco de dados MongoDB.
