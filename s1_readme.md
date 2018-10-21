
# Programa Nanodegree Data Science for Business

## by Rafael Tomaz, Identify Fraud from Enron Email

#### Visão Geral do Projeto

Em 2000, Enron era uma das maiores empresas dos Estados Unidos. Já em 2002, ela colapsou e quebrou devido a uma fraude que envolveu grande parte da corporação. Resultando em uma investigação federal, muitos dados que são normalmente confidenciais, se tornaram públicos, incluindo dezenas de milhares de e-mails e detalhes financeiros para os executivos dos mais altos níveis da empresa. Neste projeto, você irá bancar o detetive, e colocar suas habilidades na construção de um modelo preditivo que visará determinar se um funcionário é ou não um funcionário de interesse (POI). Um funcionário de interesse é um funcionário que participou do escândalo da empresa Enron. Para te auxiliar neste trabalho de detetive, nós combinamos os dados financeiros e sobre e-mails dos funcionários investigados neste caso de fraude, o que significa que eles foram indiciados, fecharam acordos com o governo, ou testemunharam em troca de imunidade no processo.
 

### Summarize for us the goal of this project and how machine learning is useful in trying to accomplish it. As part of your answer, give some background on the dataset and how it can be used to answer the project question. Were there any outliers in the data when you got it, and how did you handle those?  
    [relevant rubric items: “data exploration”, “outlier investigation”]

 O objetivo deste projeto era utilizar os dados financeiros e e-mail da Enron para construir um modelo preditivo para determinar se um indivíduo poderia ser considerado uma "pessoa de interesse" (POI) ou não, pessoa causadora de fraude na Enron., como o conjunto de dados continha dados rotulados as pessoas culpáveis ​​já estavam listadas como POIs.

O Conjunto de dados possui 146 registros, com 21 características disponíveis, sendo 14 financeiras (pagamentos e investimentos), 6 de e-mail e 1 rôtulos (se é um POI), dos 146 registros possuem 18 (12% dos dados) POIs e 128(88%) non-POIs, durante a análise identifiquei que 44.29% dos dados estavam faltantes com 'NaN'.

__Analise dos Dados__:

- Ajustes de dados:
    - BELFER ROBERT estavam com dados deslocados a direita, atualizei os dados conforme o PDF
    - BHATNAGAR SANJAY, estavam com os dados deslocados a esquerda, realizei o mesmo procedimento.
    
    
- Removidos:
    - 'THE TRAVEL AGENCY IN THE PARK' : agencia de viagem, empresa co-irmã da Enron e o valor não é tão alto e não tem muita correlação com outros dados.
    - 'TOTAL' : o dado é um outlier avaliado pelo grafico
    - 'LOCKHART EUGENE E' : todos os valores estão como NaN
    
    
- POIs que não possuem email de / Para:
    - FASTOW ANDREW S
    - HIRKO JOSEPH
    - KOPPER MICHAEL J
    - YEAGER F SCOTT

Após ajustes e remover os outliers, o dataset ficou com 143 registros,  com 18 (__13%__) POIs e 125 (__87%__)non-POIs

### What features did you end up using in your POI identifier, and what selection process did you use to pick them? Did you have to do any scaling? Why or why not? 
    As part of the assignment, you should attempt to engineer your own feature that does not come ready-made in the dataset -- explain what feature you tried to make, and the rationale behind it. (You do not necessarily have to use it in the final analysis, only engineer and test it.) In your feature selection step, if you used an algorithm like a decision tree, please also give the feature importances of the features that you use, and if you used an automated feature selection function like SelectKBest, please report the feature scores and reasons for your choice of parameter values.  [relevant rubric items: “create new features”, “intelligently select features”, “properly scale features”]

Para otimizar e selecionar as características mais importantes do dataset utilizei a função de seleção de recursos automatizado SelectKBest do scikit-learn para selecionar as 8 características mais influentes do dataset, realizei um teste com mais de 8 características porém os algorítmos escolhidos não performaram muito bem, apenas com a quantidade escolhida que responderam melhor, no qual foram selecionadas as características conforme abaixo:


Característica | Pontuação          
-|-
total_stock_value | 22.510
exercised_stock_options |22.348
bonus | 20.792  
salary| 18.289  
deferred_income| 11.424  
long_term_incentive| 9.922  
total_payments| 9.283  
restricted_stock| 8.825  


A abordagem do __SelectKBest__ é um algoritmo de seleção de recurso uni-variado automatizado, e sendo assim as melhores variáveis enumeradas não levaram em conta as variáveis relacionadas com e-mail, devido a isso criei os recursos a seguir.

Na fase de feature engeneering, criei 5 novos recursos para auxiliar na análise e enriquecer os dados:

'sum_total_poi_messages',
                'bonus_over_total_salary',
                'salary_over_total_salary',
                'shared_receipt_over_from_messages',
                'shared_receipt_over_to_messages']

- bonus_over_total_salary: 
    - com o quociente da divisão do bonus pelo total do salário para saber quanto o bonus representa pelo total pago ao funcionário
    
- salary_over_total_salary:
    - com o quociente da divisão do salário pelo total do salário para saber quanto o bonus representa pelo total pago ao funcionário

- sum_total_poi_messages :
    - Com a somatória de todos as correspondências com POIs, com as variáveis "from_poi_to_this_person" e "agg_total_poi_correspondence"
    
- shared_receipt_over_from_messages:
    - com o quociente dos emails compartilhados entre o usuário e um POI e as mensagens enviadas deste usuário

- shared_receipt_over_to_messages:
    - com o quociente dos emails compartilhados entre o usuário e um POI e as mensagens recebidas para este usuário
    
Sendo assim, fiquei com um total de 13 características para avaliar as amostras + a característica de target __poi__, devido ter dados de diferentes escalas (unidades), devido ter dados de somatória de e-mail junto com dados de valores financeiros, devo realizar a técnica de escalonamento de características, para normalizar os dados de forma que garanta para os classificadores que as grandezas dos valores não fiquem muito dispersos um dos outros quando forem trantados, como no exemplo do curso quando foi indicar a camiseta de cameron com base na altura e peso, para isso dimensionei os dados utilizando __MinMaxScaler__ do scikit-learn.

### What algorithm did you end up using? What other one(s) did you try? How did model performance differ between algorithms?  
    [relevant rubric item: “pick an algorithm”]

Eu avaliei os algoritmos _Naive Bayes Gaussian, Decision Tree Classifier e Decision Tree Regressor_ porém acabei escolhendo o modelo de classificação __Naive Bayes Gaussian__  pois possui os melhores resultados de validação como (acuracia "**_0,83_**"", precisão "**_0,35_**"" e recall "**_0,33_**"") e que acabou sendo a opção final.

### What does it mean to tune the parameters of an algorithm, and what can happen if you don’t do this well?  How did you tune the parameters of your particular algorithm? What parameters did you tune?
    _(Some algorithms do not have parameters that you need to tune -- if this is the case for the one you picked, identify and briefly explain how you would have done it for the model that was not your final choice or a different model that does utilize parameter tuning, e.g. a decision tree classifier).  [relevant rubric items: “discuss parameter tuning”, “tune the algorithm”]_


Para ajustar os parâmetros de um algoritmo, signica conseguir uma combinação que possa trazer um melhor resultado para o modelo, eu fiz isso fornecendo uma matriz com todos os parametros possíveis que eu queria parametrizar para escolha para o GridSearchCV, se não fizer isso bem corre risco de ter overfitting, ocorre com frequencia dos parametros fornecidos para as previsões sobre os dados de treinamento tenham ótimo desempenho, com desempenhos muito altos devem ser observados, e os dados de teste não sejam tão bons assim, dessa forma estou utilizando a precisão e recall para medir o desempenho, as respectivas formulas são:

    Precisão = Verdadeiros Positivos / (Verdadeiros Positivos + Falsos Positivos)

    Recall = Verdadeiros Positivos / (Verdadeiros Positivos + Falsos Negativos)


A precisão é a relação da frequência que identificamos corretamente os POIs que identificamos como POI, se a precisão é baixa quer dizer que temos falsos positivos demais, portanto as pessoas que estão marcadas como POIs quando na verdade não são.

O recall ou abrangência é uma relação entre a frequência para identificarmos corretamente os POIs e o número total de POIs no conjunto de dados, incluindo os que perdemos, que não conseguimos identificar (foram marcados como não POIs). Se o recall for alto, podemos ter certeza de que, se uma pessoa for um POI, vamos marcá-lo como tal. Se o recall for baixo, significa que estamos perdendo muitos POIs, ou seja, marcando-os como não-POI.

Para avaliar a performance do melhor algoritmo, eu avaliei os dados de verificação como acurácia, precisão e recall em todos os modelos e altorítmos utilizando o função "_test_classifier_" disponível no tester.py. Para entender a evolução dos cenários avaliei em 3 pontos o código:

> Antes de realizar o escalonamento de características, e sem os novos atributos

Algoritmo | Acuracia | Precisão| Recall | F1 | F2 | Total de Predições | Verdadeiro Positivo | Falso Positivo | Falso Negativo | Verdadeiro Negativo
- |- | - | - | - | - | - | - | - | - | -
Naive Bayes Gaussian | 0,763 | 0,246 | 0,373 | 0,296 | 0,338 | 15000 | 747 | 2294 | 1253 | 10706
Decision Tree Classifier | 0,797 | 0,223 | 0,211 | 0,217 | 0,213 | 15000 | 422 | 1466 | 1578 | 11534
Decision Tree Regressor | 0,797 | 0,230 | 0,222 | 0,226 | 0,223 | 15000 | 444 | 1487 | 1556 | 11513

Obs.: Conforme analise acima o melhor algoritmo que melhor performa em questão de recall e precisão é o Naive Bayes, ele só não está muito melhor no quesito de acurácia.

> Após realizar o escalonamento de características e com os 3 novos atributos

Algoritmo | Acuracia | Precisão| Recall | F1 | F2 | Total de Predições | Verdadeiro Positivo | Falso Positivo | Falso Negativo | Verdadeiro NEgativo
- |- | - | - | - | - | - | - | - | - | -
Naive Bayes Gaussian | 0,831 | 0,356 | 0,330, | 0,342 | 0,335 | 15000 | 661 | 1194 | 1339 | 11806
Decision Tree Classifier | 0,798 | 0,252 | 0,261 | 0,256 | 0,259 | 15000 | 522 | 1543 | 1478 | 11457
Decision Tree Regressor | 0,800 | 0,257 | 0,262 | 0,260 | 0,261 | 15000 | 525 | 1511 | 1475 | 11489

Obs.: Neste ponto apos a seleção de caracteríticas, feature engeneering e escalonamento de dimensionalidade o algoritmo teve um ganho considerável nos 3 quesitos de performance 

> Com o Tuning ajustando os parâmetros das variâveis dos modelos com o GridSearchCV
    Cada Algoritmo foi ajustados os parâmetros conforme suas definições em suas documentações

- Naive Bayes Gaussian
    Não Possui Tunning para configuração apenas se posso realizar alteração alteração de parametros
    
- Decision Tree Classifier
    - criterion : gini e entropy
    - splitter : best
    - max_depth: 15 e 20

A melhor estimativa do algoritmo utilizando o __GridSearchCV__ foi:

DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=20,
            max_features=None, max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, presort=False, random_state=None,
            splitter='best')
            
 conforme o estimador o percentual de importância para este estimador foi de:
 
Variável | Percentual de Importância
 - | -
 salary | 0
total_payments | 0,15145099
bonus | 0,13893595
total_stock_value | 0,19530496
exercised_stock_options | 0,0466411
deferred_income | 0
restricted_stock | 0,06904377
long_term_incentive | 0
sum_total_poi_messages | 0,05248177
bonus_over_total_salary | 0
salary_over_total_salary | 0,18709394
shared_receipt_over_from_messages | 0,
shared_receipt_over_to_messages | 0,15904753

> As variáveis de foram criadas nno feature engeneering que tiveram mais relevancia neste processamento do algoritmo foram salary_over_total_salary com 18,7% e shared_receipt_over_to_messages com quase 16%
 
- Decision Tree Regressor 
    - criterion: mse
    - splitter:  best e random
    - presort: True e False
    - min_samples_split: 40 e 60
    - random_state: 20 e 40

A melhor estimativa do algoritmo utilizando o __GridSearchCV__ foi:

DecisionTreeRegressor(criterion='mse', max_depth=None, max_features=None,
           max_leaf_nodes=None, min_impurity_decrease=0.0,
           min_impurity_split=None, min_samples_leaf=1,
           min_samples_split=60, min_weight_fraction_leaf=0.0,
           presort=True, random_state=20, splitter='random')
            
 conforme o estimador o percentual de importância para este estimador foi de:
 
Variável | Percentual de Importância
 - | -
 salary | 0
total_payments | 0
bonus | 0
total_stock_value | 0
exercised_stock_options | 0,61656732
deferred_income | 0,05821713
restricted_stock | 0,04523335
long_term_incentive | 0
sum_total_poi_messages | 0
bonus_over_total_salary | 0
salary_over_total_salary | 0
shared_receipt_over_from_messages | 0,10747079
shared_receipt_over_to_messages | 0,17251141

> As variáveis de foram criadas no feature engeneering que tiveram mais relevancia neste processamento do algoritmo foram shared_receipt_over_from_messages com 10% e shared_receipt_over_to_messages 17%

 **_após o tunning obtive os seguintes resultados:_**

Algoritmo | Acuracia | Precisão| Recall | F1 | F2 | Total de Predições | Verdadeiro Positivo | Falso Positivo | Falso Negativo | Verdadeiro NEgativo
- |- | - | - | - | - | - | - | - | - | -
Naive Bayes Gaussian |- | - | - | - | - | - | - | - | - |
Decision Tree Classifier |0,801 | 0,250 | 0,245 | 0,247 | 0,246 | 15000 | 491 | 1472 | 1509 | 11528
Decision Tree Regressor | 0,923 | 0,434 | 0,161 | 0,235 | 0,184 | 845 | 10 | 13 | 52 | 770

Obs.: A arvore de regressão apesar de estar com os 3 fatores de performance muito bem qualificado como acurácia, precisão e recall o total de predições esta muito baixo portanto deve ter perdido informqção desta forma será descartado e será utilizado o Naive Bayes Gaussian pois atendeu ao requisito msolicitado

Conforme avaliação de acuracia, precisão e recall o melhor algoritmo foi o Naive Bayes Gaussian, e a estimativa solicitada como precisão e recall seja ao menos 0.3.


### What is validation, and what’s a classic mistake you can make if you do it wrong? How did you validate your analysis?  
    [relevant rubric items: “discuss validation”, “validation strategy”]


Validação é o processo de verificar seu modelo em um dado de treinamennto e depois testa a previsão do seu modelo em relação aos em um dado diferente, um erro classico que ocorre é o __"overfitting"__ que ocorre quando se treina e testa um algoritmo com todos os mesmo dados disponíveis em vez de dividi-los, tendo assim um desempenho muito bom. O overfitting acaba fazendo com que o modelo memorize a classificação e não _aprenda_ a generalizar e aplique essa informação a novos conjuntos de dados.

No pacote do scikit-learn há um método auxiliar chamado cross_validation.train_test_split que faz exatamente isso.

Para validar minha analise eu utilizei a função test_classifier dentro do tester.py pois utiliza a função StratifiedShuffleSplit que utiliza a estratégia k-fold, que foi utilizado para dividir os dados pelos parametros de treinamento e teste e selecionar os parametros que melhor desempenharam segregando em 1000 divisões, essa estratificação é nnecessária pois temos muito pouco POIs em comparação a não POIs, e não queremos treinar o conjunto de dados que innclua  apenas membros que estão classificados como não POIs.

### Give at least 2 evaluation metrics and your average performance for each of them.  Explain an interpretation of your metrics that says something human-understandable about your algorithm’s performance. 
    [relevant rubric item: “usage of evaluation metrics”]

A precisão do meu algoritmo foi de 0,35, isso quer dizer que quando ele informou que alguém era um POI, era verdade 35% do tempo.

O recall era 0,33. Isso indica que onde todos os POIs que o algoritmo viu ele identificou corretamente 33% deles como POI (e perdeu 67%).

### Referências:

Durante o trabalho eu consultei os seguintes materiais:

- Documentação Scikit learn
- Documentação do Python e Pandas

- Exemplos e arquivos do curso

- Apostilas do curso de MBA em Machine Learning e Inteligencia artifical da Universidade Fiap:
    - Estatistica para Inteligência artifical
    - Python para Inteligência artifical
    - Modelos Machine Learning
    
- Stack Overflow para mensagens de erro durante o projeto.