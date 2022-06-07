# Projeto 3 - Reproduzindo o Experimento de um Artigo Científico

# Apresentação

O presente projeto foi originado no contexto das atividades da disciplina de pós-graduação [*Ciência e Visualização de Dados em Saúde*](https://ds4h.org), oferecida no primeiro semestre de 2022, na Unicamp.

| Nome                       | RA     | Especialização |
| -------------------------- | ------ | -------------- |
| Cristiano Sampaio Pinheiro | 256352 | Computação     |
| Jhonatan Cléto             | 256444 | Computação     |
| Mylena Roberta dos Santos  | 222687 | Computação     |


# Referência bibliográfica do artigo lido

Azuaje, F.J., Rodius, S., Zhang, L. *et al.* Information encoded in a network of inflammation proteins predicts clinical outcome after myocardial infarction. *BMC Med Genomics* **4**, 59 (2011). https://doi.org/10.1186/1755-8794-4-59


# Resumo

Visto que a inflamação é de grande importância para o reparo cardíaco após o infarto do miocárdio (IM), o artigo descreve uma investigação da interação entre as proteínas relacionadas à inflamação, seguida da avaliação do conhecimento derivado de uma rede complexa para dar suporte às decisões clínicas após o IM.

O grafo mencionado foi batizado como My-Inflamome, sendo que ele consiste em uma rede de interação proteína-proteína (IPP) relacionada à inflamação e ao diagnóstico da doença em questão. Nesse grafo, os autores estabeleceram associações entre as propriedades da rede, a biologia da doença e a capacidade de distinção entre as categorias prognósticas.

Como conclusão, segue que o estudo relatado pelo artigo eleva o entendimento da rede de IPP que executa as respostas inflamatórias no IM humano. Além disso, seguindo o foco principal do artigo, as informações codificadas na rede complexa podem ser traduzidas em conhecimento com potencial aplicação prognóstica para pacientes após o infarto do miocárdio.


# Breve descrição do experimento/análise do artigo que foi replicado

Tomando a lista de interações entre proteínas que caracteriza a rede complexa como a entrada - sendo que transformamos a página `My-Inflamome` do *dataset* em um arquivo CSV pensado para a construção do grafo no `Cytoscape` ([My-Inflamome_ppi_data.csv](data/interim/My-Inflamome_ppi_data.csv)) -, reproduzimos as análises descritas no artigo que foram aplicadas nas fases iniciais de desenvolvimento do My-Inflamome.

É válido ressaltar que o *dataset* da rede complexa em questão resultou de um longo processo aplicado pelos autores. Primeiramente, foram listados os bioindicadores conhecidos no contexto de inflamação pós-IM. Em seguida, essa lista foi expandida em um conjunto de sementes, adicionando genes relacionados funcionalmente com os biomarcadores, que foi usado para compor *queries* em diversas bases de dados de IPP. Portanto, por considerarmos que esse processamento não faz parte do foco desse projeto, além de não possuirmos conhecimentos suficientes para tal, não reproduzimos a montagem do conjunto de dados que define o My-Inflamome.

As tais análises descritas no artigo consistiram, inicialmente, no levantamento de características topológicas da rede no nível dos nós, sendo elas *node degree* e *traffic*. De modo que *node degree* refere-se ao grau do nó, isto é, ao número de arestas que estão ligadas a um nó. *traffic*, ou *betweenness centrality*, por sua vez, refere-se ao número de caminhos mínimos que passam por um nó e que ligam dois outros nós na rede.

Em seguida, foi analisada a *network modularity*, ou modularidade da rede, que pode ser definida como a análise das sub-redes altamente interconectadas que compõem o My-Inflamome. Os módulos da rede complexa descritos no artigo foram identificados a partir de um algoritmo guloso que maximiza o *score* de modularidade.

Como saída das análises replicadas (*node degree*, *traffic* e *network modularity*) no `Cystocape`, obtivemos uma tabela em que cada registro associa um nó às suas características topológicas e a um módulo específico ([My-Inflamome_analyzed_network.csv](data/processed/My-Inflamome_analyzed_network.csv)).


## Dados usados como entrada

| Dataset | Endereço na Web | Resumo descritivo |
| ------- | --------------- | ----------------- |
| 12920_2011_252_MOESM1_ESM | https://static-content.springer.com/esm/art%3A10.1186%2F1755-8794-4-59/MediaObjects/12920_2011_252_MOESM1_ESM.XLS | Arquivo XLS que contém uma relação entre os biomarcadores e suas sementes (`Seeds`), a lista de arestas da rede complexa (`My-Inflamome`) e a descrição de cada um dos módulos identificados na rede (`module1`, …, `module21`).



# Método

> Método usado para a análise -- adaptações feitas, ferramentas utilizadas, abordagens de análise adotadas e respectivos algoritmos.
> Etapas do processo reproduzido.


# Resultados

> Apresente os resultados obtidos pela sua adaptação.
> Confronte os seus resultados com aqueles do artigo.
> Esta seção opcionalmente pode ser apresentada em conjunto com o método.
