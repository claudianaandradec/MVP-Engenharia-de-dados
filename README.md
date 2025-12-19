# MVP-Engenharia-de-dados
MVP - Engenharia de dados

# OBJETIVO

Este MVP tem como objetivo construir um pipeline de dados na nuvem para analisar informações sobre livros, usuários e avaliações, utilizando tecnologias em nuvem com Databricks e seu Delta Lake. O pipeline envolverá as etapas de busca, coleta, modelagem, carga e análise dos dados, com o propósito de fornecer insights sobre padrões de leitura, comportamento dos usuários e características que influenciam a avaliação de livros — além de estabelecer a base para um sistema simples de recomendação.

O problema central que este MVP busca resolver é a ausência de uma visão consolidada e acessível dos dados de avaliações e características dos livros, que permita identificar tendências, preferências dos usuários e padrões relevantes para o consumo de conteúdo literário. Para isso, serão respondidas as seguintes perguntas:

 Sobre livros e avaliações
   
1. Quais são os livros mais bem avaliados pelos usuários?
2. Qual é a média de avaliação por gênero literário?
3. Quais livros possuem maior volume de avaliações ao longo do tempo?
4. Há livros com avaliações baixas, mas muito populares?

Sobre comportamento dos usuários
   
6. Quais usuários são mais ativos na plataforma (maior número de avaliações)?
7. Existe relação entre idade e tipo de livro avaliado?
8. Há diferenças de avaliação entre gêneros masculino/feminino?
9. Usuários de determinadas faixas etárias preferem determinados gêneros?

Sobre padrões no catálogo

11. Quais editoras possuem melhor média de avaliação?
12. Existe concentração de livros mal avaliados em determinadas editoras ou autores?

Sobre recomendação

14. Quais são os livros mais recomendados para novos usuários (modelo baseado em popularidade)?
15. É possível sugerir livros semelhantes com base no histórico de avaliações (modelo item-item)?
16. Quais recomendações diferem entre faixas etárias e gêneros?

Sobre qualidade dos dados

14. Existem inconsistências nas avaliações (valores nulos, fora do intervalo ou duplicados)?
15. Existem livros ou usuários com informações incompletas no dataset?

🎯 Objetivo final

Ao final do projeto, espera-se entregar:
Um pipeline de dados completo (Raw → Bronze → Silver → Gold)
Um modelo de dados estruturado em formato estrela (DW)
Um catálogo dos dados documentado
Análises exploratórias e de qualidade dos dados
Visualizações e respostas às perguntas definidas
Um sistema simples de recomendação baseado nas avaliações
Com isso, o MVP pretende demonstrar como pipelines em nuvem podem apoiar experiências de recomendação e permitir uma análise eficiente de grandes volumes de dados literários.

# COLETA DE DADOS

# Fonte dos Dados e Processo de Coleta

Os dados utilizados neste projeto foram obtidos de fontes abertas e públicas, eliminando riscos relacionados à confidencialidade das informações. A base principal reúne metadados de livros, avaliações e preferências de usuários, compondo o insumo necessário para o desenvolvimento de um sistema de recomendação. Foram coletados dados entre os anos de 1998 e 2024, conforme a disponibilidade de cada fonte.

# Tabela Fato – Interações Usuário–Livro

A tabela fato do projeto, denominada fato_interacoes_usuarios_gold, foi construída a partir de registros de interações (ratings, reviews e marcações) coletados do portal Goodreads por meio de datasets disponibilizados publicamente no Kaggle.

🔗 # Fonte principal:
Goodreads Books Dataset – Kaggle (https://www.kaggle.com/datasets/zygmunt/goodbooks-10k)
(arquivo contendo livros, avaliações, notas e interações de usuários)

Esse dataset foi escolhido por possuir volume significativo, metadata completa e padronização adequada para análises avançadas de recomendação.

Os arquivos foram baixados manualmente, no formato CSV, e posteriormente enviados para o repositório GitHub do projeto. O pipeline foi configurado para realizar o ingest dos dados diretamente via API do GitHub, permitindo atualização simplificada e versionada.

Tabelas Dimensão

As tabelas dimensão foram obtidas de diferentes fontes complementares, conforme detalhado abaixo.

📘 Dimensão Livros (dim_livros_gold)

Para enriquecer as informações dos livros, foi utilizada a base do dataset:

🔗 Fonte:
Goodreads Books Dataset (books.csv)
Contém: título, autores, ISBN, idioma, número de páginas, ano de publicação, média de avaliação e descrição.

Esse conjunto foi selecionado por fornecer metadados essenciais para a qualidade das recomendações baseadas em conteúdo (content-based filtering).

🧑‍💻 # Dimensão Usuários (dim_usuarios_gold)

Como as bases públicas de recomendação não incluem dados pessoais, somente IDs anônimos, utilizamos os identificadores do próprio dataset:

🔗 Fonte:
Goodreads Interactions Dataset (ratings.csv / interactions.csv)
Contém: user_id, book_id, rating e timestamp.

Por motivos de privacidade, nenhuma informação sensível é incluída, mantendo o dataset totalmente anonimizado e compatível com LGPD.

🏷️ # Dimensão Gêneros Literários (dim_generos_gold)

As informações de gêneros foram extraídas de forma complementar a partir da API pública do Google Books, utilizada apenas para enriquecer metadata faltante em alguns livros.

🔗 Fonte:
Google Books API – consulta automatizada para gêneros e categorias literárias.

Os dados foram coletados em formato JSON e transformados em CSV para carga no pipeline.

🌐 # Outras Tabelas Dimensão Criadas Manualmente

Algumas tabelas possuíam poucas linhas e foram facilmente criadas manualmente em CSV, com separação por “;”, utilizando um editor de texto. Essas tabelas incluem classificações auxiliares utilizadas no modelo de recomendação:

Popularidade (popularidade_gold)
Faixa de Ano de Publicação (faixa_ano_gold)
Categorias Simplificadas (categoria_simplificada_gold)

Essas tabelas foram construídas com base na estrutura do próprio dataset e projetadas para auxiliar no enriquecimento do processo analítico.

# MODELAGEM E CATÁLOGO DE DADOS

Para estruturar e organizar os dados de forma eficiente, foi adotado o Esquema Estrela, amplamente utilizado em soluções de Data Warehousing, Business Intelligence e sistemas de recomendação baseados em análises analíticas.

# Estrutura do Esquema Estrela

O esquema estrela do projeto foi construído com uma tabela fato principal contendo as interações entre usuários e livros, acompanhada de cinco tabelas dimensão, que consolidam e organizam os metadados necessários para alimentar o motor de recomendações.

A arquitetura ficou estruturada da seguinte forma:

📘 # Tabela Fato: fato_interacoes_usuarios_gold

A tabela fato contém os registros de comportamento dos usuários, sendo o núcleo central do modelo.
Cada linha representa uma interação única, como:

* avaliação (rating)
* marcação de leitura (read / want-to-read)
* review textual
* data e hora da interação

Esses dados são a base para algoritmos de recomendação como:
✔ Filtragem Colaborativa
✔ Content-Based Filtering
✔ Modelos Híbridos

📊 # Tabelas Dimensão

Foram criadas tabelas auxiliares para armazenar atributos descritivos, garantindo enriquecimento e consistência das análises por meio de joins.

As dimensões utilizadas foram:

* dim_livros_gold – informações dos livros (título, autor, ISBN, idioma, ano)
* dim_usuarios_gold – identificador do usuário (anonimizado)
* dim_generos_gold – gêneros literários e categorias temáticas
* dim_popularidade_gold – classificação de popularidade baseada em média de notas e volume de reviews
* dim_ano_publicacao_gold – agrupamentos de ano para análises temporais

Essas dimensões permitem que cada interação seja contextualizada, criando um ambiente analítico robusto para recomendações personalizadas.

# Catálogo de Dados
📌 # Tabela fato_interacoes_usuarios_gold

A tabela fato foi construída a partir da base original do Goodreads, que continha dezenas de campos sobre avaliações, metadados do livro e comportamento do usuário.

Durante o processo de ETL, vários campos redundantes ou irrelevantes foram removidos, resultando em uma estrutura mais enxuta, organizada e otimizada para análises de recomendação.

A estrutura final da tabela fato contém os seguintes campos:
📌 Tabelas Dimensão

| Campo            | Descrição                                    |
| ---------------- | -------------------------------------------- |
| interaction_id   | Identificador único da interação             |
| user_id          | Identificador anonimizado do usuário         |
| book_id          | Identificador único do livro                 |
| rating           | Nota atribuída pelo usuário                  |
| review_text      | Texto do comentário (quando disponível)      |
| interaction_type | Tipo de interação (rating, review, marcação) |
| timestamp        | Data/hora da interação                       |
| source           | Origem do dado (Goodreads / Kaggle / API)    |

Resumo da estrutura das dimensões incluídas no modelo:
# dim_livros_gold

| Campo            | Descrição                 |
| ---------------- | ------------------------- |
| book_id          | Chave primária do livro   |
| title            | Título                    |
| authors          | Autor(es)                 |
| isbn             | Identificação ISBN        |
| language         | Idioma                    |
| pages            | Número de páginas         |
| publication_year | Ano de publicação         |
| avg_rating       | Média geral de avaliações |

# dim_usuarios_gold

| Campo         | Descrição                  |
| ------------- | -------------------------- |
| user_id       | Identificador anonimizado  |
| total_reviews | Volume de reviews escritos |
| total_ratings | Total de avaliações        |

# dim_generos_gold
| Campo      | Descrição                |
| ---------- | ------------------------ |
| genre_id   | Chave primária           |
| genre_name | Nome do gênero literário |

# dim_popularidade_gold
| Campo            | Descrição                        |
| ---------------- | -------------------------------- |
| popularity_id    | Chave primária                   |
| popularity_level | Baixa / Média / Alta             |
| rule             | Regra utilizada na classificação |

# dim_ano_publicacao_gold
| Campo       | Descrição                       |
| ----------- | ------------------------------- |
| year_group  | Faixa de ano (ex.: "1990–1999") |
| description | Categoria analítica             |

![image alt](https://github.com/claudianaandradec/MVP-Engenharia-de-dados/blob/a3d93143e37b0f6e5e48777a1158f837517be1c7/Diagrama%20ER.jpg)

Carga (ETL) – Pipeline no Databricks

Nesta etapa será construído o pipeline de ETL (Extração, Transformação e Carga) responsável por ingerir, limpar, padronizar e disponibilizar os dados no Delta Lake.

Utilizaremos a arquitetura Medallion, dividindo o processamento em três camadas:

🥉 Bronze – dados brutos

🥈 Silver – dados tratados e padronizados

🥇 Gold – dados modelados no Esquema Estrela

[carga] esta sessão esta no notebook

ANÁLISE DE QUALIDADE DOS DADOS
Durante a execução do pipeline de dados (arquitetura Medallion: Bronze → Silver → Gold), os dados passaram por diversas verificações de integridade, tipagem e consistência. O objetivo foi garantir que a camada final (Gold) estivesse apta para gerar insights confiáveis sobre o comportamento de leitura.
Nesta seção, foi detalahda a qualidade dos atributos, os problemas encontrados nos arquivos brutos e as soluções técnicas aplicadas.
1. Seleção e Limpeza de Atributos
O dataset original (books.csv) possuía diversas colunas irrelevantes para o objetivo analítico deste MVP, como URLs de capas de livros (image_url, small_image_url) e múltiplos contadores internos redundantes.
Para otimizar o armazenamento e a performance das consultas no Data Warehouse, foi realizada a remoção dessas colunas durante a transição para a camada Silver, mantendo apenas os metadados essenciais (título, autor, ano, ISBN e identificadores).
2. Inconsistências de Tipagem e "Dados Sujos"
Um dos problemas críticos identificados foi a presença de "sujeira" (dirty data) em colunas numéricas.
Problema: Na coluna average_rating, que deveria conter apenas valores decimais (double), foram encontrados registros contendo texto (ex: o valor "eng", que pertence à coluna de idioma, foi deslocado para a coluna de nota em algumas linhas corrompidas).
Solução: foi utilizada a função try_cast do Spark. Diferente de uma conversão comum que falharia o pipeline, essa abordagem converteu os valores inválidos para NULL. Em seguida, aplicamos um filtro (isNotNull) na chave primária para descartar essas linhas corrompidas, garantindo que apenas livros com dados estruturados chegassem à camada Gold.
3. Tratamento de Identificadores (Chaves Primárias)
Houve uma inconsistência semântica nos identificadores dos livros. O dataset continha dois IDs distintos: um sequencial do próprio arquivo (book_id) e outro original do site Goodreads (goodreads_book_id).
Problema: A tabela de tags utilizava o ID do site, enquanto a tabela de ratings utilizava o ID sequencial.
Solução: Realizado um mapeamento explícito e renomeação dessas colunas na camada Silver. Isso garantiu a integridade referencial nos Joins da camada Gold, permitindo cruzar corretamente as avaliações dos usuários com os gêneros literários.
4. Tratamento de Valores Nulos e Duplicatas
Duplicatas: Embora raro, o processo de ingestão poderia gerar duplicidade de processamento. Foi aplicada a função .dropDuplicates() baseada nas chaves naturais (ex: um usuário não pode ter duas avaliações para o mesmo livro; mantivemos apenas a mais recente ou única).
Nulos: Campos textuais essenciais, como original_title, passaram por tratamento de limpeza (trim) para remover espaços em branco. Para livros sem histórico de avaliações no dataset, foi preenchido os valores nulos de contagem com 0 (zero) para não prejudicar os cálculos de média.
5. Qualidade Estatística para Recomendação
Notou-se que alguns livros possuíam médias de avaliação muito altas (5.0), mas com apenas 1 ou 2 votos. Estatisticamente, isso gera um viés de recomendação ("falsos melhores livros").
Solução: Foram criadas regras de negócio na camada Gold para classificar a popularidade. Nas análises de recomendação, foram aplicados filtros (ex: ratings_count > 100) para garantir que os insights refletissem o consenso da comunidade e não outliers.
Conclusão
Após as etapas de limpeza, tipagem robusta (uso de try_cast) e modelagem dimensional, os dados atingiram um nível de qualidade satisfatório. As inconsistências estruturais foram sanadas na camada Silver, permitindo que a camada Gold responda às perguntas de negócio com precisão e sem interrupções no processamento.
