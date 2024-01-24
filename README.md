# cassandra-groupby-spark
Projeto dedicado à criação e configuração do Cassandra para receber carga de dados dados, com ênfase na liberação de acesso para o Apache Spark. Permite operações avançadas, como groupBy, facilitando manipulações distribuídas eficientes.

<p align="center">
    <img src="https://i.imgur.com/pBYiaGd.jpg" alt="Cassandra">
</p>      

## Planejamento básico de infraestrutura necessária para comportar dados
O cassandra foi criado em um contêiner no docker afim de evitar incompatibilidades com a minha
máquina. O cassandra foi configurado para liberar o acesso do spark por meio da porta 9042, para
permitir a execução de análise de dados e funções que não são permitidas no cassandra, como
grupby.

## Descrição das configurações feitas em um ambiente computacional para receber a infraestrutura Cassandra
Foi feita a instalação do docker no linux mint, em seguida os seguintes comandos foram feitos para
prontificar a máquina em receber o conjunto de dados:

-Criação do network com o comando “docker network create --driver bridge cassandra-network”;

-Criação do contêiner com o comando “docker run --name jupyer-cassandra --network cassandra-
network -p 9042:9042 -v /home/r/Documentos/projeto_cassandra cassandra:4.1.3”

<p align="center">
    <img src="https://i.imgur.com/0qy03hI.png" alt="Cassandra0">
</p>        

## Realização da carga de dados para o ambiente configurado e execução de todas as atividades necessárias na base para que a infraestrutura fique pronta para a execução de operações com os dados

O csv possui informações sobre casas em Amsterdam e o objetivo de trabalhar com esse csv é obter insights sobre a média de preços baseado no número de quartos da residência.

Antes da carga os arquivo csv foi copiado para o centêiner com o comando “docker cp
/home/r/Documentos/projeto_cassandra/HousingPrices-Amsterdam-August-2021.csv
73368b6d03e1:/tmp/”. 

Para receber o csv, foi necessário criar um keyspace e uma tabela com os
comandos:

CREATE KEYSPACE analises WITH replication = {'class': 'SimpleStrategy',
'replication_factor':'1'};

CREATE TABLE analises.casas (
id TEXT PRIMARY KEY,
Address TEXT,
Zip TEXT,
Price INT,
Area INT,
Room INT,
Lon DOUBLE,
Lat DOUBLE
);

O arquivo csv foi copiado para o cassandra com o seguinte comando:

COPY analises.casas (id, address, zip, price, area, room, lon, lat) FROM '/tmp/HousingPrices-
Amsterdam-August-2021.csv' WITH DELIMITER=',';

## Apresentação do código e uma comprovação de sucesso de execução de uma consulta de leitura na base de dados carregada para a infraestrutura Cassandra

A base de dados foi carregada com o seguinte código:

COPY analises.casas (id, address, zip, price, area, room, lon, lat) FROM '/tmp/HousingPrices-
Amsterdam-August-2021.csv' WITH DELIMITER=',';

A consulta foi efetuada com o seguinte código:

SELECT * FROM analises.casas;

![Cassandra2](https://i.imgur.com/Dlnqfv5.png)     

## Extração de informações utilizando o Spark

![Cassandra3](https://i.imgur.com/VJBRTwI.png)  
![Cassandra4](https://i.imgur.com/Gb7P0HJ.png)  
![Cassandra5](https://i.imgur.com/1siGDHp.png)        

## Realização de manipulação de dados utilizando o Spark

O código realiza o cálculo da média de preço por quarto para duas categorias diferentes de casas: aquelas com preço inferior a um milhão de euros e aquelas com preço igual ou superior a dois milhões de euros. Em resumo, foi possível obter insights sobre a média de preço por quarto para duas faixas de preço distintas, facilitando a compreensão das tendências de mercado para casas de diferentes valores.

![Cassandra6](https://i.imgur.com/QrcKY0z.png)   
![Cassandra7](https://i.imgur.com/kXOEvSo.png)   

## Visualização gráfica dos insights obtidos

Média de preço por quarto (casas a partir de dois milhões de euros)

![Cassandra8](https://i.imgur.com/1gNVC2o.png)        

Média de preço por quarto (casas com preço menor que um milhão de euros)

![Cassandra9](https://i.imgur.com/Udi6pmA.png)  
