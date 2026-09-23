# Stealth AAV Predictor 

## Objectif

Ce projet est vise à prédire les scores de sélection virale de variants de capsides d'AAV (Adeno-Associated Virus). Il utilise le modèle de langage protéique ESM-2 (développé par Meta) pour extraire les embeddings des séquences, couplé à un modèle de régression pour évaluer la viabilité des mutations.



## Technologies utilisées

Modèle de Langage Protéique : ESM-2 (fair-esm)

Machine Learning : Scikit-learn (SVR), PyTorch

Manipulation de données : Pandas, NumPy

Optimisation matérielle : Support de l'accélération MPS (Apple Silicon) et CUDA.



## Provenance des données et Reproductibilité

Les données complètes utilisées pour l'entraînement original de ce modèle sont issues de recherches spécifiques.

### Liens vers les études lues dans le cadre du développement : 

AAV vectors: The Rubik’s cube of human gene therapy
_Amaury Pupo,1 Audry Fernández,1 Siew Hui Low,1 Achille François,2 Lester Suárez-Amarán,1 and Richard Jude Samulski1,3_


[Deep diversification of an AAV capsid protein by machine learning : 
_Drew H. Bryant, Ali Bashir, Sam Sinai, Nina K. Jain, Pierce J. Ogden, Patrick F. Riley, George M. Church, Lucy J. Colwell & Eric D. Kelsic_](https://www.nature.com/articles/s41587-020-00793-4)


[ArtificialIntelligence-BasedApproachesforAAVVector Engineering
_FangzhiTan,*YueDong,JieyuQi,WenwuYu,*andRenjieChai*_](https://www.biorxiv.org/content/10.1101/2021.04.16.440236v1.full#sec-7)



AAV Vector Immunogenicity in Humans: A Long Journey to Successful Gene Transfer
_Helena Costa Verdera,1,2 Klaudia Kuranda,3 and Federico Mingozzi1,3_ 


[Humoral Immune Response to AAV
_Roberto Calcedo, James M. Wilson_](https://www.frontiersin.org/journals/immunology/articles/10.3389/fimmu.2013.00341/full)


Note : Pour des raisons de confidentialité, le dataset complet n'est pas hébergé sur ce dépôt. Vous trouverez cependant le fichier _exemple.csv_ qui reprend la même structure que le fichier d'origine avec les 10 premières lignes (le fichier d'origine en compte un peu moins de 300 000). 

Le fichier d'origine est trouvable [ici](https://github.com/alibashir/aav?utm_source=gemini) (vers le dépôt github Deep diversification of an AAV capsid protein by machine learning )
 

La première piste envisagée provient du fichier .csv trouvé sur le dépôt github de l'étude "Deep diversification of an AAV capsid protein by machine learning", dans le dossier Data
https://github.com/churchlab/Deep_diversification_AAV

[Autre piste de dataset intéressante à explorer](https://huggingface.co/datasets/weiskenyon/aav2_capsid_viability) 


Comme indiqué, l'entraînement a été fait sur la base d'un fichier de presque 300 000 lignes comportant les variations de la séquence de la boucle VP3 : [major coat protein VP3, Adeno-associated virus](https://www.ncbi.nlm.nih.gov/protein/QDH44321.1report=genbank&log$=protalign&blast_rank=2&RID=B4JRVSW1016) 


Pour tester le code localement, assurez-vous de fournir un fichier CSV respectant la structure attendue par le script (colonnes : sequence, viral_selection, etc...)



# État du projet (Work in Progress )

Ce code a été initié de manière indépendante pour explorer l'intégration de modèles ML à l'ingénierie des capsides virales.

⚠️ Note : Le développement est actuellement en pause. Le notebook présente l'architecture globale, mais certains bugs d'optimisation (notamment sur la gestion de la mémoire lors du mean pooling) subsistent. La pipeline d'évaluation doit également être renforcée avant de pouvoir interpréter les prédictions finales à grande échelle.



## Pistes d'amélioration futures

[ ] Optimiser le traitement par lots (batching) pour éviter les saturations mémoire sur de grands datasets.

[ ] Nettoyer le Jupyter Notebook en extrayant les fonctions clés dans des scripts Python modulaires (.py).

[ ] Implémenter des métriques d'évaluation rigoureuses (corrélations de Spearman/Pearson) sur un jeu de validation dédié.
