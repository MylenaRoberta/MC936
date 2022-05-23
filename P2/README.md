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

> Dado um paciente com COVID-19, qual é a probabilidade dele morrer, em decorrência dessa doença, em até 30 dias após a data do diagnóstico?

A escolha específica pela doença causada pelo novo coronavírus (SARS-CoV-2) foi motivada pela pandemia em curso enfrentada por todo o mundo. O contexto que estamos vivenciando desde a disseminação do vírus deu destaque a essa enfermidade, incentivando a realização e a ampla divulgação de pesquisas acadêmicas relacionadas a ela.

Por conseguinte, informações sobre diversos aspectos da COVID-19 - por exemplo, o que fazer em caso de exposição, sintomas associados e grupos de risco -, podem ser encontradas com relativa facilidade em fontes confiáveis, como sites de organizações competentes, canais de comunicação de autoridades da área de saúde e jornais.

A Organização Mundial da Saúde (OMS), ou World Health Organization (WHO), definiu em seu Q&A (Questions and Answers) [1], atualizado pela última vez em 13 de março de 2021, que o tempo desde a exposição ao vírus até a manifestação dos sintomas é, em média, de 5-6 dias, podendo variar de 1-14 dias. Ademais, a OMS aconselha que pessoas expostas ao SARS-CoV-2 fiquem isoladas por 14 dias a fim de evitar a propagação do vírus.

Desse modo, considerando o tempo máximo dentre a exposição ao vírus e a manifestação dos sintomas e também o tempo indicado de isolamento em caso de exposição, optamos por analisar a morte de pacientes pela COVID-19 em até 30 dias (um mês) após a data do diagnóstico.


## Ferramentas
* `Python` [2] - linguagem utilizada para desenvolver os códigos para exploração dos dados, preparação dos dados e análise dos resultados dos modelos de predição desenvolvidos. 
    * `pandas` [3] - biblioteca do Python usada para manipular e analisar as bases de dados e os resultados dos modelos.
* `Google Colab` - empregado para desenvolver e executar os códigos Python em notebooks.
* `Google Drive` - utilizado para armazenar os notebooks do Colab e os arquivos CSV com as bases de dados e os resultados.
* `Orange` [4] - kit de ferramentas empregado no desenvolvimento e análise dos resultados dos modelos de predição.


# Metodologia
Ao definirmos a proposta de predição, partimos para o estudo dos conjuntos de dados disponibilizados para a execução do projeto. Tais conjuntos são resultantes do Synthea (Synthetic Patient Population Simulator) [5], um gerador de pacientes sintéticos que modela o histórico médico desses pacientes. O projeto desse gerador tem como objetivo produzir dados sintéticos, realistas (mas não reais), de pacientes e registros de saúde associados em vários formatos. Sendo que os dados gerados por ele são livres de custo, privacidade e restrições de segurança.

Ao todo foram disponibilizados quatro cenários (`scenario01`, `scenario02`, `scenario03` e `scenario04`) de históricos médicos de pacientes sintéticos. Todavia, optamos por trabalhar com somente três deles ([`scenario01`](data/raw/scenario01.zip), [`scenario02`](data/raw/scenario02.zip) e [`scenario03`](data/raw/scenario03.zip)), pois o cenário restante conta com um volume de dados incompatível com poder de processamento das ferramentas com as quais escolhemos trabalhar (vide a Tabela 1).

| Cenário | Número de Pacientes | Intervalo Temporal dos Históricos Médicos | Estado de Residência dos Pacientes | Cidade de Residência dos Pacientes |
| ---------- | -----  | ------- | ------------- | -------- |
| scenario01 | 1174   | 10 anos | Massachusetts | Diversas |
| scenario02 | 1121   | 10 anos | Alaska        | Juneau   |
| scenario03 | 11573  | 10 anos | Washington    | Diversas |
| scenario04 | 117531 | 10 anos | Massachusetts | Diversas |

*Tabela 1 - Informações sobre os cenários gerados pelo Synthea que foram disponibilizados.*

Antes de definirmos quais dados dos cenários adotados seriam empregados no projeto, determinamos como seriam desenvolvidos os modelos de predição. Sendo que optamos pelo uso de técnicas de aprendizagem de máquina não supervisionada para tal, em específico, escolhemos a Clusterização Hierárquica e também a Clusterização K-means. 

Nosso objetivo ao eleger essa abordagem é analisar os *clusters* gerados, identificando os grupos que contêm os pacientes com os conjuntos de características que aumentam a mortalidade por COVID-19 e quantificando as mortalidades associadas a esses grupos no período definido na proposta. Ou seja, temos como propósito levantar as características de um paciente que tendem, estatisticamente, a levá-lo a óbito pela doença em questão, auxiliando na definição do prognóstico de mortalidade do indivíduo.

Decidimos adotar as duas técnicas de clusterização citadas a fim de comparar os resultados produzidos por cada abordagem, verificando as diferenças e semelhanças entre os *clusters* gerados por elas. Ademais, em decorrência da comparação, desejamos definir se uma das técnicas é mais adequada para a proposta ou se ambas produzem resultados com a mesma qualidade.

Voltando aos cenários de históricos médicos, segue que cada um conta com os dados fragmentados em 18 tabelas no formato de arquivos CSV, sendo que todas estão descritas no dicionário de dados dos arquivos CSV do Synthea [6]. De acordo com a nossa proposta e com os objetivos que levantamos para o projeto, decidimos adotar as bases descritas na Tabela 2.

| Arquivo             | Descrição                                              |
| ------------------- | ------------------------------------------------------ |
| `conditions.csv`    | Condições ou diagnósticos do paciente                  |
| `encounters.csv`    | Dados de encontro do paciente                          |
| `immunizations.csv` | Dados de imunização do paciente                        |
| `patients.csv`      | Dados demográficos do paciente                         |
| `procedures.csv`    | Dados de procedimento do paciente, incluindo cirurgias |

*Tabela 2 - Descrições dos arquivos CSV das tabelas adotadas como base para o estudo.*

Com as bases de estudo definidas, avançamos para a preparação de dados que pode ser encontrada no notebook [`data_processing_COVID_19_patients.ipynb`](notebooks/data_processing_COVID_19_patients.ipynb). De início, partindo das tabelas de `conditions`, selecionamos os `IDs` dos pacientes que foram diagnosticados com COVID-19, chegando a 86, 96 e 950 indivíduos com essa condição em, respectivamente, `scenario01`, `scenario02` e `scenario03`. Tomando esses identificadores, filtramos os dados das demais tabelas, reduzindo o escopo da análise somente aos pacientes que tiveram COVID-19.

Considerando a proposta adotada para o projeto, decidimos que algumas das variáveis contidas nas tabelas apresentavam baixa relevância no contexto da abordagem adotada para o desenvolvimento dos modelos de predição. Portanto, após removermos as colunas correspondentes a essas variáveis, segue que, para cada cenário:

* as tabelas `conditions`, `immunizations` e `procedures` têm somente duas colunas, `PATIENT` e `DESCRIPTION`;
* a tabela de `encounters` tem as colunas: `START`, `PATIENT` e `DESCRIPTION`;
* e a tabela de `patients` tem as colunas: `Id`, `BIRTHDATE`, `DEATHDATE`, `RACE`, `ETHNICITY`, `GENDER`, `CITY` e `STATE`.

Ademais, julgamos que seria útil adicionar alguns dados na tabela `patients` para serem utilizados pelos modelos de predição. Assim, geramos as seguintes colunas:

* `AGE` - idade do paciente.
* `COVID DIAGNOSIS DATE` - data do diagnóstico de COVID-19.
* `DIED IN ONE MONTH` - booleano que identifica se o paciente morreu (1) ou não (0) por COVID-19 em até 30 dias após o diagnóstico da doença.
* `LAST ENCOUNTERS OR DIED DATE` - data da morte por COVID-19 ou data do último encontro com o paciente em até 30 dias após o diagnóstico da doença.
* `TIME (DAYS)` - diferença entre `LAST ENCOUNTERS OR DIED DATE` e `COVID DIAGNOSIS DATE`.

Sendo que as colunas `LAST ENCOUNTERS OR DIED DATE` e `TIME (DAYS)` foram concebidas visando a aplicação de uma análise de sobrevivência sobre os resultados dos modelos. Em específico, os dados de `TIME (DAYS)` foram pensados para ser usados como tempo de sobrevivência ou censura.

Após a remoção das variáveis menos relevantes e a adição de dados que seriam utilizados pelos modelos de predição, visando a maneira como os dados são consumidos pelo Orange, concatenamos, para cada cenário, os dados de todos pacientes diagnosticados com COVID-19  em uma tabela. De modo que a concatenação citada resultou do *merge* entre as tabelas `patients`, `conditions`, `immunizations` e `procedures`.

Isto é, ao final da preparação de dados, geramos as tabelas: links das tabelas resultantes. Cada linha das tabelas corresponde a um determinado paciente e cada coluna corresponde a uma variável relacionada ao paciente em questão, podendo representar dados textuais, numéricos ou booleanos. Especificamente sobre as colunas com dados booleanos, elas correspondem a uma descrição (`DESCRIPTION`) de `conditions`, `immunizations` ou `procedures` e apresentam `1` se o paciente tem a condição tratada, foi imunizado pela vacina específica ou passou pelo procedimento em questão.


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
