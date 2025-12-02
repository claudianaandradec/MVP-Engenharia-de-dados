# MVP-Engenharia-de-dados
MVP - Engenharia de dados
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
