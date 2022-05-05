# ADT-Pater-sermons
Il s'agit de mener une analyse des données textuelles des sermons du Pater chez Augustin et Césaire d'Arles.

```ruby
library(FactoMineR)
library(R.temis)
library(tm)
library(RColorBrewer)
library(stylo)
library(stopwords)

```

Objectif : création d'un nuage de mots du corpus de sermons lemmatisés et sans les stopwords typiques en latin (à partir de la liste fournie par perseus)

```ruby
corpus=SimpleCorpus(DirSource("~/Documents/Documents/Stylometry/Sermons_Pater/"))
#on enlève tous les chiffres qui suivent les mots issus de la lemmatisation de Deucalion
corpus1=tm_map(corpus, removeNumbers)
#on enlève tous les stopwords de la liste latin perseus du corpus de textes
corpus_stopwords=tm_map(corpus1, removeWords, stopwords("la", source = "perseus"))
#création de la matrice TermDocument
dtm=R.temis::build_dtm(corpus_stopwords)
inspect(dtm)
#création d'un nuage de mots du corpus
word_cloud(dtm,colors=brewer.pal(8, "Dark2"), n=100, min.freq=20)

```

On crééé deux tableaux, l'un qui contient les 100 termes les plus fréquents et un autre, le tableau lexical des termes du corpus. Enfin, on mesure les termes spécifiques des textes du corpus.

```ruby
df=frequent_terms(dtm, variable = NULL, n = 100)

dtm=R.temis::build_dtm(corpus_stopwords)
inspect(dtm)

term_freq(dtm, 'dimitto')
term_freq(dtm, 'noster')
term_freq(dtm, 'tuus')
term_freq(dtm, 'sanctifico')
term_freq(dtm, 'peto')
term_freq(dtm, 'pater')
term_freq(dtm, 'semper')
term_freq(dtm, 'uerbum')
term_freq(dtm, 'sol')

```

On recherche des coocurrences de termes au sein du corpus, ici "noster" qui appartient au champ lexical de la prière du Notre Père. Ensuite, on détermine les concordances existant entre les lemmes. Enfin, on dresse un graphe des lemmes du corpus dont la taille des arrêtes est proportionnelle au niveau de leur coocurrence.


```ruby
cut=split_documents(corpus_stopwords, 1) #découpage du corpus en paragraphes
dtm_cut=build_dtm(cut)
cooc_terms(dtm_cut, "noster")
# Recherche de concordances entre "noster" et le lemme qui coocurre le plus avec lui, "debitor"
concordances(corpus_stopwords,dtm,c("noster","debitor"))
#graphe de mots sur le DTM ou Analyse de données relationnelles (SNA)
terms_graph(dtm_cut, vertex.label.cex = 0.5)

```

On réalise une analyse factorielle de correspondance sur les sermons du corpus. On évalue les trois documents qui contribuent le plus à l'axe 1 de l'AFC.


```ruby
AC=corpus_ca(corpus_stopwords, dtm) 
explor(AC)
contributive_docs(corpus_stopwords,AC, 1, ndocs=4)

```

On applique la méthode Reinert, 1983, grâce au package Rainette (The Reinert Method for Textual Data Clustering).

```ruby
library(rainette)
library(quanteda)
dfm <- as.dfm(dtm)
resrai <- rainette(dfm, k = 2, min_segment_size = 0, min_split_members = 3)
rainette_explor(resrai, dfm, corpus_src = dfm)
```
