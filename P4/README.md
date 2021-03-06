# Projeto Classificação por SVM de lesões de substância branca causadas por LES
# Project SVM classification of white matter lesions caused by SLE

# Apresentação

O presente projeto foi originado no contexto das atividades da disciplina de pós-graduação [*Ciência e Visualização de Dados em Saúde*](https://ds4h.org), oferecida no primeiro semestre de 2022, na Unicamp.

| Nome                       | RA     | Especialização |
| -------------------------- | ------ | -------------- |
| Cristiano Sampaio Pinheiro | 256352 | Computação     |
| Jhonatan Cléto             | 256444 | Computação     |
| Mylena Roberta dos Santos  | 222687 | Computação     |


# Introdução

Este projeto objetiva-se responder à seguinte pergunta de pesquisa da Dra. Simone Appenzeller, Professora Associada de Reumatologia da Faculdade de Ciências Médicas (FCM) da Unicamp:

> Qual é a etiologia mais provável das lesões de substância branca presentes em pacientes com Lúpus Eritematoso Sistêmico (LES)?

A fim de tentarmos encontrar uma resposta satisfatória, treinamos um classificador SVM (*Support Vector Machine*) para diferenciar lesões isquêmicas e desmielinizantes e, por conseguinte, predizer a etiologia mais provável das lesões de substância branca presentes nos indivíduos com LES.

O treinamento mencionado foi realizado com imagens de ressonância magnética do cérebro de pacientes que sofreram Acidente Vascular Cerebral (AVC) ou têm Esclerose Múltipla (EM). Sendo que AVC e EM são capazes de causar, respectivamente, lesões de substância branca denominadas isquêmicas e desmielinizantes.


## Ferramentas

* `Python` 
    * `cv2`  
    * `glob` 
    * `glrlm` 
    * `matplotlib`
    * `numpy`
    * `pandas`
    * `PIL`
    * `scikit-learn`
    * `scipy`
    * `skimage` 
    * `seaborn` 
* `Google Colab` 
* `Google Drive`  


## Preparo e uso dos dados

Para a construção do modelo de classificação, nos foram disponibilizados três conjuntos de imagens, sendo eles:

* AVC: conjunto com 1475 imagens de cortes de ressonância magnética do cérebro de pacientes que tiveram AVC;
* EM: conjunto com 2344 imagens de cortes de ressonância magnética do cérebro de pacientes que têm Esclerose Múltipla;
* LES: conjunto de 697 imagens de cortes de ressonância magnética do cérebro de pacientes que têm Lúpus Eritematoso Sistêmico.

Nesses conjuntos de imagens, existem ainda imagens auxiliares a alguns dos cortes, que delimitam as regiões do cérebro onde as lesões se encontram, utilizando a técnica de máscara binária     [1], sendo 581 máscaras para o conjunto AVC, 630 para o conjunto EM e 697 para o conjunto LES.

Durante a fase de preparação dos conjuntos de dados, adotamos o *pipeline* ilustrado na Figura 1. Vale ressaltar que cada etapa presente no *pipeline* foi realizada em um *notebook* independente.

![data_flow](assets/data_flow.png)

_**Figura 1:** Pipeline aplicado para a classificação das imagens._

* **Seleção de imagens:** durante nossa análise inicial do conjunto de dados, notamos que algumas imagens não possuíam máscaras auxiliares ou as máscaras apresentavam delimitações claramente incorretas das lesões. Com isso, optamos por aplicar alguns critérios de seleção para definir os conjuntos de imagens que julgamos mais adequados para usar em nosso modelo.

* **Extração de atributos:** após a seleção de imagens, aplicamos algumas técnicas de extração de *features* das imagens, de modo a construir *datasets*, com informações que julgamos importantes para a fase de classificação das imagens dos conjuntos.

Na etapa de pré-processamento das imagens, há uma infinidade de outras técnicas que poderiam ser utilizadas, como normalização, transformações nas intensidades dos pixels e seleção de diversas outras *features* que poderiam ser utilizadas para o treinamento de um classificador. Em nosso *pipeline*, no entanto, optamos por não aplicar nenhuma dessas técnicas, uma vez que, a partir das atividades auxiliares a esse projeto, percebemos que elas não agregariam significância equivalente ao custo de sua aplicação nos conjuntos de imagens fornecidos.

Especificamente sobre a normalização em imagens de ressonância magnética, sua má aplicação pode levar a uma perda de acurácia nos resultados obtidos, enquanto que, quando utilizada de maneira correta os ganhos são mínimos [2]. Portanto, considerando a inexperiência da equipe com o domínio explorado, optamos por não usar nenhuma técnica de normalização nesse projeto. 

### Seleção de imagens

Em nossa abordagem de pré-processamento, optamos por utilizar apenas as imagens do conjunto fornecido para treinamento que possuíam máscaras delimitando as regiões do cérebro com lesões. Escolhemos proceder dessa maneira, pois acreditamos que obteremos melhores resultados no preditor utilizando um recorte nas regiões de lesões, do que utilizando a imagem completa do cérebro.

Durante o processo de seleção das máscaras, observamos que algumas delas estavam extremamente imprecisas, chegando, em alguns casos, a ocupar mais de 50% da imagem. Essas máscaras aparentam destoar bastante em relação ao tamanho das lesões nas imagens originais. Na Figura 2 apresentamos uma máscara “negativa”, onde a região da lesão é a única não demarcada, em preto.

![mask_error](assets/mask_error.png)  

_**Figura 2:** Exemplo de imagem descartada durante o processo de seleção._

Indo mais a fundo nessa questão, investigamos a distribuição dos pixels para todas as máscaras contidas nos conjuntos de imagens fornecido. Essas investigações, bem como o processo de seleção de imagens, estão codificados no *notebook* [image_selection.ipynb](notebooks/image_selection.ipynb).

As Figuras 3, 4 e 5, apresentam os *boxplots* das distribuições de pixels nos conjuntos AVC, EM e LES, respectivamente. Dos resultados obtidos, notamos a existência de muitos *outliers* localizados em valores superiores ao máximo da distribuição em todos os conjuntos.

Assim, consideramos que remover da seleção de imagens, as máscaras que apresentassem mais de 50000 pixels seria um limiar razoável, já que ele está por volta de 25% do tamanho da maioria das imagens.

![boxplot_avc](assets/boxplot_AVC.png) 

_**Figura 3:** Boxplot da distribuição de pixels das máscaras do conjunto AVC._

![boxplot_em](assets/boxplot_EM.png) 

_**Figura 4:** Boxplot da distribuição de pixels das máscaras do conjunto EM._

![boxplot_les](assets/boxplot_LES.png)

_**Figura 5:** Boxplot da distribuição de pixels das máscaras do conjunto LES._  

Aplicando os critérios de seleção de imagens citados anteriormente, reduzimos nosso conjunto de imagens aos seguintes valores: 

* AVC: 548 imagens selecionadas, cerca de 37.6% do conjunto original;
* EM: 623 imagens selecionadas, cerca de 26.6% do conjunto original;
* LES: 694 imagens selecionadas, cerca de 99.6% do conjunto original;

Desses resultados, notamos que perdemos uma fração significativa do número de imagens que nos foram fornecidas para treino - conjuntos AVC e EM -. Desse fato, concluímos que, embora o nosso processo de seleção de imagens fornece conjuntos mais robustos para treino, ele enfraquece a capacidade de aprendizado do modelo por reduzir drasticamente o conjunto de treino, diminuindo a robustez do classificador.

Partindo dos conjuntos de imagens selecionados, utilizando as máscaras foi possível determinar as coordenadas limites das lesões verificando os pixels com cores diferentes de preto. Com essas coordenadas, após a aplicação das máscaras, usando uma simples multiplicação de matrizes, foi possível cortar as imagens de ressonância que apresentavam lesões, obtendo assim somente a região de interesse (ROI) para a extração de *features*.


![mask_application](assets/mask_application.png)  

_**Figura 6:** Aplicação da máscara em uma lesão de EM._
 
### Extração de atributos

Ainda que a quantidade de pixels pretos nas imagens tenha diminuído após a aplicação das máscaras nas imagens, executamos os métodos de extração de *features* de maneira a não considerar esses pixels. Esse procedimento foi adotado visando a aplicação de descritores de textura, e, uma vez que as lesões estão concentradas próximas à cor branca, a princípio, acreditamos que os pixels pretos não agregarão na identificação e classificação dessas lesões. Ainda, no caso de múltiplas lesões com um grande espaçamento, o *background* pode corresponder a uma área muito maior que as lesões, o que pode gerar ruído nos dados obtidos, como podemos observar na Figura 7.

![mask_background](assets/mask_background.png)  

_Figura 7: Lesões de LES com grande espaçamento entre si._

Nessa etapa foram extraídos atributos provenientes do histograma e da textura das imagens, sendo que para essa última fizemos uso da Matriz de Coocorrência (GLCM), Matriz de Comprimento de Corrida (GLRLM) e Local Binary Pattern (LBP). Para todos esses descritores aplicamos modificações para desconsiderar os pixels pretos, sendo que todo o processo de extração dos atributos está detalhado no *notebook* [image_processing.ipynb](notebooks/image_processing.ipynb). 

Ainda, visando um descritor de forma, adicionamos uma *feature* correspondente a área da lesão  - isto é, a quantidade de pixels brancos -, extraída na etapa de seleção das imagens. Por fim, vale destacar que em alguns casos a GLCM pode variar de acordo com o ângulo, no entanto, nesse projeto esse fenômeno não deve ocorrer, assim, optamos por obter esse atributo de maneira cumulativa, utilizando de todos os ângulos possíveis.

Ao final do processo foram gerados três conjuntos de dados de *features*:  um destinado a lesões isquêmicas, outro para desmielinizantes e um último contendo as lesões de pacientes com LES. Além de trazer os atributos já mencionados, esses *datasets* também têm o identificador do paciente e, para as lesões isquêmicas e desmielinizantes, um rótulo indicando a doença causadora, utilizado para o treinamento do classificador. Todos os atributos extraídos e as suas respectivas descrições encontram-se na Tabela 1.

|Atributo|Descrição|
|---|---|
|patient| Identificador do paciente que originou a imagem|
|min| Menor valor encontrado no histograma|
|max| Maior valor encontrado no histograma|
|mean| Média aritmética dos valores presentes no histograma|
|variance| Variância dos valores encontrados no histograma|
|skewness| Assimetria ou obliquidade do histograma|
|kurtosis| Curtose, medida da forma que caracteriza a cauda do histograma|
|contrast_sum| Soma do contraste, diferença entre os tons de cinza|
|correlation_sum| Soma da correlação, dependência linear dos tons de cinza|
|energy_sum| Soma da energia, uniformidade da textura|
|homogeneity_sum| Soma da homogeneidade, medida da variação da textura|
|sre| Short Run Emphasis, ênfase em corrida curta|
|lre| Long Run Emphasis, ênfase em corrida longa|
|glu| Grey Level Uniformity, uniformidade de tom de cinza|
|rlu| Run Length Uniformity, uniformidade de tamanho de corrida|
|rpc| Run Percentage, percentagem de corrida, mede predominância dos tamanhos de corrida|
|min_LBP| Menor valor encontrado no histograma da LBP|
|max_LBP| Maior valor encontrado no histograma da LBP|
|mean_LBP| Média aritmética dos valores presentes no histograma da LBP|
|variance_LBP| Variância dos valores encontrado no histograma da LBP|
|skewness_LBP| Assimetria ou obliquidade do histograma da LBP|
|kurtosis_LBP| Curtose, medida da forma que caracteriza a cauda do histograma da LBP|
|area| Número de pixels com valor maior que zero presentes nas imagem|
|target| Grupo ao qual o paciente pertence (AVC ou EM)|

_**Tabela 1:** Atributos extraídos do conjunto de imagens._


# Metodologia

Como mencionado anteriormente, optamos por utilizar um classificador SVM (*Support Vector Machine*) [3][4] de modo a buscar a resposta para pergunta de pesquisa levantada. O código empregado para todo o desenvolvimento do classificador está no *notebook* [svm_classification.ipynb](notebooks/svm_classification.ipynb).

Em relação ao treinamento do modelo, os dados empregados resultaram da concatenação dos *datasets* de atributos extraídos das imagens de ressonância magnética de pacientes com lesões de substância branca isquêmicas ([avc_features.csv](data/processed/avc_features.csv)) e desmielinizantes ([em_features.csv](data/processed/em_features.csv)).

Em relação a predição, por sua vez, os dados oferecidos ao classificador resultaram do *dataset* de atributos extraídos das imagens de ressonância magnética de pacientes com LES ([les_features.csv](data/processed/les_features.csv)).

É válido ressaltar ainda que, para ambos os conjuntos de treinamento e predição, foi necessário aplicar um pequeno processo de preparação para o uso. Citando os pontos essenciais, foi preciso separar os atributos dos conjuntos em variáveis adequadas para o uso no classificador e também uniformizar os valores das *features* para que o preditor funcionasse adequadamente [5].

Para escolher os hiperparâmetros que definem o modelo, utilizamos de um estimador (GridSearchCV) que realiza uma pesquisa exaustiva sobre valores de parâmetros especificados a ele [6][7][8]. Optamos pelo uso de uma gama ampla de parâmetros, dada a falta de profundidade no conhecimento que temos sobre o domínio do problema e a pouca experiência em desenvolvimento de algoritmos de classificadores.

Ademais, junto ao estimador de hiperparâmetros mencionado, empregamos uma variante do iterador KFold para realizar a validação cruzada dos dados de treinamento do modelo [9]. Essa variante (GroupKFold), de acordo com a documentação, garante que um mesmo grupo não esteja nos conjuntos de teste e treinamento [10][11]. Isto é, no caso do nosso modelo, garante que os cortes associados a um mesmo paciente não estão em ambos os conjuntos de teste e treinamento.

A Tabela 2 apresenta os resultados mais relevantes do treinamento do modelo.

| Rótulo | Resultado |
| -- | -- |
| param_C | 1 |
| param_degree| 1 |
| param_gamma | 0.1 |
| param_kernel | rbf |
| mean_test_score | 0.943324 |
| std_test_score | 0.030098 |
| mean_train_score | 0.961087 |
| std_train_score | 0.00256 |

_T**abela 2:** Principais resultados do treinamento do classificador SVM._

Ao fim, geramos um arquivo CSV ([les_classification.csv](data/processed/les_classification.csv)) que o associa cada predição de etiologia da lesão por LES ao seu respectivo paciente. Esse arquivo serviu como base para analisarmos e tentarmos compreender os resultados obtidos pelo classificador SVM.


# Resultados Obtidos e Discussão

As análises realizadas sobre os resultados do classificador estão contidas no *notebook* [results_analysis.ipynb](notebook/results_analysis.ipynb). Como base para essas análises, tomamos os *datasets* [avc_features.csv](data/processed/avc_features.csv), [em_features.csv](data/processed/em_features.csv), [les_features.csv](data/processed/les_features.csv) e [les_classification.csv](data/processed/les_classification.csv).

Analisando a distribuição das predições geradas pelo classificador SVM, segue que, aparentemente, a etiologia mais provável das lesões de substância branca presentes em pacientes com LES é isquêmica. Uma vez que cerca de 75% dos cortes de lesões que foram testados no classificador receberam o *target* AVC como caracterização.

Todavia, verificamos que uma parcela significativa dos pacientes não tiveram todos os cortes de suas lesões classificadas com a mesma etiologia. Observando especificamente os cortes das lesões causadas por LES do paciente 634, segue que seis deles passaram pelo nosso *pipeline*. Tínhamos a expectativa de que todos, ou ao menos a grande maioria, das ROIs seriam classificadas de uma mesma maneira para os pacientes. Entretanto, como mostram as Figuras 8 e 9, temos que metade dos cortes do paciente 634 foram rotulados como AVC e outra metade como EM. Portanto, não podemos afirmar com convicção qual é a etiologia mais provável dessas lesões de substância branca. 

![patient_634_cuts_avc](assets/patient_634_cuts_avc.png) 

_**Figura 8:** ROIs dos cortes das lesões do paciente 634 que foram classificados como AVC._

![patient_634_cuts_em](assets/patient_634_cuts_em.png) 

_**Figura 9:** ROIs dos cortes das lesões do paciente 634 que foram classificados como EM._

De modo a verificar as distribuições das classificações dos cortes das lesões dos pacientes de LES, separamos os indivíduos em três *clusters*:

* AVC: pacientes que tiveram 70% ou mais dos cortes classificados como AVC.
* EM: pacientes que tiveram 70% ou mais dos cortes classificados como EM.
* INCERTO: pacientes não incluídos nos grupos AVC ou EM.

Segmentados os *clusters*, notamos que cerca de 32% dos pacientes estão em INCERTO e aproximadamente 5% estão em EM. Logo, segue que a maioria dos pacientes (quase 63%) está de fato em AVC, porém há uma relevante parcela de indivíduos com lesões nas quais o classificador não definiu com convicção suficiente, de acordo com os nossos critérios, a etiologia mais provável.

Fazendo uma análise geral sobre os resultados obtidos, levantamos a hipótese que a área era um dos atributos com maior influência na classificação. De maneira que fomos surpreendidos ao verificar que tanto as imagens com as 10 maiores áreas quanto as com as 10 menores áreas foram classificadas como isquêmicas, mais detalhes das features desses cortes com áreas extremas pode ser encontrado no *notebook* [results_analysis.ipynb](notebook/results_analysis.ipynb) . Possivelmente estávamos com um olhar enviesado uma vez que já sabíamos que lesões de EM e de LES tendem a ser menores. De qualquer modo, acreditamos que a área tem grande influência em todo o processo de maneira indireta, uma vez que estamos trabalhando com features de textura desconsiderando o background.

Outra hipótese levantada para a dificuldade de geração de resultados consistentes para um mesmo paciente pode estar relacionada ao fato das lesões por LES serem majoritariamente menores. Por consequência das menores áreas, as *features* extraídas dessas imagens apresentam uma menor riqueza de detalhes, o que pode acarretar em dados ruidosos a respeitos das lesões causadas por essa doença.

Diante do que obtivemos nas análises dos resultados da predição do classificador SVM, levantamos três hipóteses distintas:

* O preditor pode estar enviesado para classificar lesões como isquêmicas, o que pode explicar a maior quantidade de cortes classificados como AVC. 

    Isto é, pode ser que a nossa engenharia de atributos não tenha produzido os melhores resultados praticáveis. Além disso, uma vez que boa parte das *features* de textura têm relação com a área da lesão, supomos que a correlação entre alguns dos atributos possa ter gerado *overfitting* do modelo.

    Por exemplo, ao plotarmos Área x RLU para os *clusters* de pacientes com LES, como pode ser observado na Figura 10, é notável uma relação linear e diretamente proporcional entre essas *features*. Assim, verificamos que há uma correlação entre elas.

    ![area_vs_rlu](assets/area_vs_rlu.png) 

_**Figura 10:** Área x RLU utilizando as *features* dos pacientes com LES._

* A etiologia mais provável das lesões de substância branca presentes em pacientes com Lúpus Eritematoso Sistêmico é de fato isquêmica.

    Dado que estamos tentando responder uma pergunta de pesquisa real, ainda não existe uma explicação concreta para o que este projeto está tentando solucionar. Dessa forma, pode ser que os resultados que obtivemos neste projeto estejam corretos.

* A etiologia das lesões de substância branca presentes em pacientes com Lúpus Eritematoso Sistêmico é de um tipo ainda não definido pela medicina.

    Também é possível que a real etiologia das lesões de LES combine características de lesões isquêmicas e desmielinizantes. Ou seja, essas lesões podem, na realidade, ter uma etiologia única de acordo com as definições estabelecidas pela medicina.



# Conclusão

Para alcançar os resultados expostos na seção anterior passamos por diversas dificuldades, a começar pela construção do *pipeline*. Por ser o primeiro contato da equipe com o processamento de imagens, houveram dificuldades em determinar como e quando aplicar cada etapa do processamento. 

Ainda, o tempo de execução dos códigos foi consideravelmente maior neste projeto. Durante alguns testes, certas etapas levaram mais de uma hora para processar, algo que foi amenizado fragmentando o processamento. Finalmente, a análise dos resultados obtidos se mostrou o maior desafio deste projeto, sendo que acreditamos que essa dificuldade seja resultante de vários fatores. Alguns deles são a insegurança no processo proposto por nós, a novidade quanto ao tipo dos dados utilizados e a aplicação de tudo isso a um problema real, envolvendo uma pergunta ainda sem resposta.

Após a execução do *pipeline* apresentado e uma análise sobre os resultados obtidos, foi difícil determinar uma etiologia para as lesões de LES. Visto que, apesar do classificador gerado indicar que tais lesões seriam majoritariamente isquêmicas, há a possibilidade de um viés em seu treinamento. Essa hipótese foi levantada pela falta de familiaridade, por parte da equipe, com o domínio tratado e, também, com a metodologia aplicada. Ademais, é possível que a não aplicação de um seletor de *features* tenha ocasionado em muitos atributos correlacionados, o que pode levar a um *overfitting* do classificador. 

Para afirmar com convicção que alguma das três hipóteses levantadas no tópico anterior se ressalta perante as outras, o *pipeline* utilizado precisa ser aprimorado. Acreditamos que a etapa de seleção de imagens pode ser melhor trabalhada, aplicando um *threshold* mais robusto ou desenvolvendo um método de seleção baseado em outras características além da área. Acrescido a isso, a aplicação de algum tipo de normalização pode ser viável, visando homogeneizar o conjunto de imagens fornecidas, assim, seria interessante estudar e testar as técnicas mais apropriadas buscando a melhor acurácia possível.

Ainda, algum tipo de dilatação poderia ser aplicado às máscaras de modo a minimizar algum erro humano que possa ter ocorrido durante sua delimitação. Esse processo ainda poderia ser realizado de modo a englobar as bordas das lesões, que podem conter informações importantes para auxiliar na distinção entre suas etiologias. Por fim, um PCA (*Principal Component Analysis*)[12] poderia ser aplicado para eliminar as *features* correlacionadas e selecionar aquelas de maior valia para o treinamento do classificador, reduzindo seu possível viés. 

É válido ressaltar que as diferentes melhorias que mencionamos anteriormente foram consideradas durante a execução do projeto, todavia, não houve tempo hábil para que colocássemos tudo em prática. Assim, citamos aqui as escolhas que acreditamos que poderiam fazer a diferença ao serem aplicadas em nosso *pipeline* original e que adicionamos em nossa bagagem para projetos futuros.

Mesmo com certas ressalvas quanto aos resultados obtidos, esse projeto se mostrou essencial para nosso aprendizado, tanto por ser uma primeira experiência usando imagens quanto por se tratar de um problema de pesquisa real que ainda não tem resposta bem definida. Durante a sua execução, aprendemos não só a extrair informações de imagens de diversas maneiras, mas a como desenvolver um *pipeline* para a melhor exploração dessas informações. Além disso, enfrentamos as dificuldades envolvidas no processo e tentamos minimizá-las da melhor forma possível. Assim, acreditamos que esse projeto não só nos embasou para a exploração de um novo tipo de dado, mas nos forneceu senso crítico para lidar com problemas desse gênero no futuro. 



# Referências Bibliográficas

[[1](https://homepages.inf.ed.ac.uk/rbf/HIPR2/mask.htm)] Binary Mask

[[2](https://drive.google.com/file/d/1A1ViXtvPXjEajQtdpNuzzsfoGM_Nm0U4/view)] Influence of MR image intensity normalization on texture-based classification of brain white matter lesions

[[3](https://scikit-learn.org/stable/modules/svm)] Support Vector Machines - scikit-learn 1.1.1 documentation

[[4](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC)] sklearn.svm.SVC - scikit-learn 1.1.1 documentation

[[5](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler)] sklearn.preprocessing.StandardScaler - scikit-learn 1.1.1 documentation

[[6](https://tatianaesc.medium.com/implementando-um-modelo-de-classifica%C3%A7%C3%A3o-no-scikit-learn-6206d684b377)] Implementando um Modelo de Classificação no Scikit-Learn | by Tatiana Escovedo | Medium

[[7](https://scikit-learn.org/stable/modules/grid_search)] Tuning the hyper-parameters of an estimator - scikit-learn 1.1.1 documentation

[[8](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV)] sklearn.model_selection.GridSearchCV - scikit-learn 1.1.1 documentation

[[9](https://scikit-learn.org/stable/modules/cross_validation)] Cross-validation: evaluating estimator performance - scikit-learn 1.1.1 documentation

[[10](https://scikit-learn.org/stable/tutorial/statistical_inference/model_selection.html)] Model selection: choosing estimators and their parameters - scikit-learn 1.1.1 documentation

[[11](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GroupKFold)] sklearn.model_selection.GroupKFold - scikit-learn 1.1.1 documentation

[[12](https://www.geeksforgeeks.org/ml-principal-component-analysispca/)] ML | Principal Component Analysis (PCA)
