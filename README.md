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
   
10. Quais editoras possuem melhor média de avaliação?
11. Existe concentração de livros mal avaliados em determinadas editoras ou autores?

Sobre recomendação

13. Quais são os livros mais recomendados para novos usuários (modelo baseado em popularidade)?
14. É possível sugerir livros semelhantes com base no histórico de avaliações (modelo item-item)?
15. Quais recomendações diferem entre faixas etárias e gêneros?
    
Sobre qualidade dos dados

17. Existem inconsistências nas avaliações (valores nulos, fora do intervalo ou duplicados)?
18. Existem livros ou usuários com informações incompletas no dataset?

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

Fonte dos Dados e Processo de Coleta

Os dados utilizados neste projeto foram obtidos de fontes abertas e públicas, eliminando riscos relacionados à confidencialidade das informações. A base principal reúne metadados de livros, avaliações e preferências de usuários, compondo o insumo necessário para o desenvolvimento de um sistema de recomendação. Foram coletados dados entre os anos de 1998 e 2024, conforme a disponibilidade de cada fonte.

Tabela Fato – Interações Usuário–Livro

A tabela fato do projeto, denominada fato_interacoes_usuarios_gold, foi construída a partir de registros de interações (ratings, reviews e marcações) coletados do portal Goodreads por meio de datasets disponibilizados publicamente no Kaggle.

🔗 Fonte principal:
Goodreads Books Dataset – Kaggle
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

🧑‍💻 Dimensão Usuários (dim_usuarios_gold)

Como as bases públicas de recomendação não incluem dados pessoais, somente IDs anônimos, utilizamos os identificadores do próprio dataset:

🔗 Fonte:
Goodreads Interactions Dataset (ratings.csv / interactions.csv)
Contém: user_id, book_id, rating e timestamp.

Por motivos de privacidade, nenhuma informação sensível é incluída, mantendo o dataset totalmente anonimizado e compatível com LGPD.

🏷️ Dimensão Gêneros Literários (dim_generos_gold)

As informações de gêneros foram extraídas de forma complementar a partir da API pública do Google Books, utilizada apenas para enriquecer metadata faltante em alguns livros.

🔗 Fonte:
Google Books API – consulta automatizada para gêneros e categorias literárias.

Os dados foram coletados em formato JSON e transformados em CSV para carga no pipeline.

🌐 Outras Tabelas Dimensão Criadas Manualmente

Algumas tabelas possuíam poucas linhas e foram facilmente criadas manualmente em CSV, com separação por “;”, utilizando um editor de texto. Essas tabelas incluem classificações auxiliares utilizadas no modelo de recomendação:

Popularidade (popularidade_gold)

Faixa de Ano de Publicação (faixa_ano_gold)

Categorias Simplificadas (categoria_simplificada_gold)

Essas tabelas foram construídas com base na estrutura do próprio dataset e projetadas para auxiliar no enriquecimento do processo analítico.
