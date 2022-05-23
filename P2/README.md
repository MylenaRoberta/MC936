# Projeto Predizendo Prognóstico de Mortalidade por COVID-19 com Dados Sintéticos
# Project Predicting COVID-19 Mortality Prognosis with Syntetic Data

# Apresentação
O presente projeto foi originado no contexto das atividades da disciplina de pós-graduação [*Ciência e Visualização de Dados em Saúde*](https://ds4h.org), oferecida no primeiro semestre de 2022, na Unicamp.

| Nome                       | RA     | Especialização |
| -------------------------- | ------ | -------------- |
| Cristiano Sampaio Pinheiro | 256352 | Computação     |
| Jhonatan Cléto             | 256444 | Computação     |
| Mylena Roberta dos Santos  | 222687 | Computação     |


# Contextualização da Proposta
A proposta de predição adotada foi a seguinte:

> Dado um paciente com COVID-19, qual é a probabilidade dele morrer em até 30 dias após a data do diagnóstico?

A escolha específica pela doença causada pelo novo coronavírus (SARS-CoV-2) foi motivada pela pandemia em curso enfrentada por todo o mundo. O contexto que estamos vivenciando atualmente deu destaque para essa enfermidade, incentivando a realização e a ampla divulgação de pesquisas acadêmicas relacionadas a ela. 

Por conseguinte, informações sobre diversos aspectos da COVID-19 - o que fazer em caso de exposição, sintomas associados e grupos de risco, por exemplo -, podem ser encontradas com relativa facilidade em fontes confiáveis, como sites de organizações competentes, autoridades da área de saúde e jornais.

A Organização Mundial da Saúde (OMS), ou World Health Organization (WHO), define em seu Q&A (Questions and Answers) [1], atualizado pela última vez em 13 de março de 2021, que o tempo desde a exposição ao vírus até a manifestação dos sintomas é, em média, de 5-6 dias, podendo variar de 1-14 dias. Ademais, a OMS aconselha que pessoas expostas ao SARS-CoV-2 fiquem isoladas por 14 dias a fim de evitar a propagação do vírus.

Desse modo, considerando o tempo máximo desde a exposição até a manifestação dos sintomas e o tempo indicado de isolamento em caso de exposição, optamos por analisar a morte de pacientes até 30 dias após a data do diagnóstico.


## Ferramentas
* `Python 3` [2] - linguagem de programação utilizada para desenvolver os códigos para exploração, tratamento de dados e análise dos resultados.
    * `pandas` [3] - biblioteca do Python usada para manipular e analisar as bases dados e os resultados.
* `Google Colab` - empregado para desenvolver e executar os códigos em Python em notebooks.
* `Google Drive` - utilizado para armazenar os notebooks do Colab e os arquivos CSV com as bases de dados e os resultados.
* `Orange` [4] - empregado no desenvolvimento e análise dos resultados dos modelos de predição.


# Metodologia
> Abordagem adotada pelo projeto na predição.
> Justificar as escolhas e (opcionalmente) apresentar fundamentos teóricos.


## Bases Adotadas para o Estudo
* scenario01
* scenario02
* scenario03


# Resultados Obtidos
> Esta seção pode opcionalmente ser apresentada em conjunto com a metodologia, intercalando método e resultados.
>
> Descreva etapas para obtenção do modelo, incluindo tratamento de dados, se houve.
>
> Apresente o seu modelo de predição e resultados alcançados.
> Para cada modelo, apresente no mínimo:
> * quais os dados sobre o paciente que serão usados para a predição;
> * qual a abordagem/modelo adotado;
> * resultados do preditor (apresente da forma mais rica possível, usando tabelas e gráficos - métricas e curva ROC são bem vindos);
> * breve discussão sobre os resultados obtidos.
>
> Nesta seção, lembre-se das sugestões de análise:
> * analisar diferentes composições de treinamento e análise do modelo:
>   * um modelo treinado/validado no cenário 1 e testado no cenário 2 e vice-versa;
>   * um modelo treinado e validado com os dados dos dois cenários;
>   * nos modelos dos dois itens anteriores:
>     * houve diferença de resultados?
>     * como analisar e interpretar as diferenças?
> * testar diferentes composições de dados sobre o paciente para a predição (por exemplo, quantidade diversificadas de número de itens).


# Evolução do Projeto
> Seção opcional se houver histórico de mudanças e evolução relevantes.
> Relate aqui a evolução do projeto: possíveis problemas enfrentados e possíveis mudanças de trajetória. Relatar o processo para se alcançar os resultados é tão importante quanto os resultados.


# Discussão
> Fazer um breve debate sobre os resultados alcançados. Aqui pode ser feita a análise dos possíveis motivos que certos resultados foram alcançados. Por exemplo:
> * por que seu modelo alcançou (ou não) um bom resultado?
> * por que o modelo de um cenário não se desempenhou bem em outro?
>
> A discussão dos resultados também pode ser feita opcionalmente na seção de Resultados, na medida em que os resultados são apresentados. Aspectos importantes a serem discutidos: É possível tirar conclusões dos resultados? Quais? Há indicações de direções para estudo? São necessários trabalhos mais profundos?


# Conclusão
> Destacar as principais conclusões obtidas no desenvolvimento do projeto.
>
> Destacar os principais desafios enfrentados.
>
> Principais lições aprendidas.
>
> Trabalhos Futuros:
> * o que poderia ser melhorado se houvesse mais tempo?


# Referências Bibliográficas
[[1]](https://www.who.int/emergencies/diseases/novel-coronavirus-2019/question-and-answers-hub/q-a-detail/coronavirus-disease-covid-19) World Health Organization (WHO) - COVID-19 Q&A.

[[2]](https://docs.python.org/3/) Python Documentation.

[[3]](https://pandas.pydata.org/docs/) Pandas Documentation.

[[4]](https://orangedatamining.com/docs/) Orange Data Mining Documentation.

[[5]](https://github.com/synthetichealth/synthea/wiki) Synthea Wiki.

[[6]](https://github.com/synthetichealth/synthea/wiki/CSV-File-Data-Dictionary) Synthea Wiki - CSV File Data Dictionary.

[[7]](https://doi.org/10.1016/j.ekir.2020.05.007) Komaru Y, Yoshida T, et al. Hierarchical Clustering Analysis for Predicting 1-Year Mortality After Starting Hemodialysis.

[[8]](https://en.wikipedia.org/wiki/Silhouette_(clustering)) Silhouette Score Method - Wikipedia.

[[9]](https://en.wikipedia.org/wiki/Kaplan%E2%80%93Meier_estimator) Kaplan-Meier Estimator - Wikipedia.
