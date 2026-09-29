# Stealth AAV Predictor 

## Objectif
Ce projet vise à prédire les scores de sélection virale (viabilité d'assemblage) de variants de capsides du virus adéno-associé (AAV). Il utilise le modèle de langage protéique ESM-2 pour extraire les embeddings des séquences en acides aminés, couplé à un modèle de régression (SVR) pour évaluer la viabilité des mutations de la boucle VP3.

L'objectif à terme est de réaliser une évolution dirigée in silico en générant des mutations intelligentes dont on peut anticiper la viabilité avant tout test en laboratoire.



## Contexte

L'ingénierie des capsides est un enjeu majeur pour le ciblage tissulaire et l'échappement immunitaire en thérapie génique. Développé comme une initiative indépendante à l'intersection de l'immunologie, de la virologie structurale et de la bio-informatique, ce projet explore l'intégration concrète de l'IA (modèles de langage protéiques) pour accélérer la découverte de nouveaux vecteurs viraux. Il constitue une preuve de concept de l'application du Machine Learning à des problématiques biologiques complexes.


## Fonctionnement

Le pipeline d'analyse est modulaire et se divise en quatre grandes phases :

1. Préparation des données : Insertion de boucles variables mutées (28 AA) dans la séquence sauvage (WT) de la protéine VP1 de l'AAV2.

2. Vectorisation (Embeddings) : Utilisation d'ESM-2 pour convertir les séquences protéiques en vecteurs mathématiques de 320 dimensions.

3. Apprentissage Supervisé : Entraînement d'un modèle SVR (Support Vector Regression) pour relier ces dimensions spatiales à un score continu d'assemblage viral.

4. Sauvegarde & Inférence : Sérialisation des matrices et du modèle pour permettre des prédictions rapides sur de nouvelles séquences sans nécessiter un ré-entraînement complet. Les matrices d'entraînement allégées (run de 50k) et le modèle pré-entraîné sont disponibles sur le dépôt. Pour les utiliser, il suffit de les placer à la racine du dossier contenant le notebook.

### Technologie :

Modèle de Langage Protéique : ESM-2 (fair-esm)

Machine Learning : Scikit-learn (SVR), PyTorch

Manipulation de données : Pandas, NumPy

Optimisation matérielle : Support de l'accélération MPS (Apple Silicon) et CUDA.



## Reproductibilité

L'entraînement repose sur le jeu de données issu des recherches de l'équipe de George M. Church (Deep diversification of an AAV capsid protein by machine learning).

Dataset d'origine : Le fichier complet (allseqs_20191230.csv.zip) de près de 300 000 séquences est disponible sur le [dépôt GitHub de l'étude.](https://github.com/alibashir/aav?utm)

Séquence de référence VP3 : NCBI QDH44321.1

⚠️ Note : Pour des raisons de taille et de performance, le dataset complet n'est pas hébergé sur ce dépôt. Un fichier Variants_echantillon.csv comprenant 10 lignes au hasard est fourni pour comprendre la structure attendue (colonnes : sequence, viral_selection, etc.) et tester le code localement.


### Exemple de run : 
Un premier entraînement a été validé sur un échantillon de 50 000 séquences.

Modèle ESM-2 utilisé : esm2_t6_8M_UR50D (8 millions de paramètres).

Temps d'exécution : Environ 1h30 sur une puce Apple Silicon M1 Pro pour l'extraction et l'entraînement.


Test d'inférence (Séquences inédites) :
Le modèle montre une précision remarquable (erreur absolue < 0.6) sur les séquences modérément mutées. Sur les variants très atypiques ou présentant de larges délétions/insertions, on observe un "biais pessimiste" (le modèle prédit un score négatif même si la séquence est viable), attribuable à la petite taille du modèle ESM-2 testé et à la forte proportion de séquences non-viables dans l'échantillon d'apprentissage.

Séquence (boucle)Score RéelScore PréditDifférence absolueAEEEIRTTNPVATEQYGSVStTlNqQqTnQqGtNvTe1.1544301.1469700.007460DEEEIRTTNPVATEQYGSVsTvNqNnQqATtTnVaTe1.3481701.3640280.015858DEEEIRTTNPVATEQYGCVcSTELnQeGNsNQ1.1932421.1707730.022469DEEEIRTTNPVpATEQYGSVSPNLQRGNR-4.678155-4.7293610.051206DEEGIRTTNPVATEQYGSVSTNLQyRGNR-3.885840-3.8345700.051270DEEEIRTTNPVATEQYGSVcSpTNgLERnDdTlEs1.1163481.0054190.110929DEEEIRTTNPVATEQYGSVSTdNgLdQeMGnNGg0.9978881.1175350.119648StEtAEIATTNPVAYEpPWGSVSnAnQPDtMnPeSeNw-5.253893-5.4152450.161352DEEEICTTNPVATEQYGSVSTdNgGQQGNhN0.8625181.1074790.244961DEEEIRTTNPVATEQYGqCVcSnTgEdHnAeFfGsDnQs0.8163940.5223540.294040DEEEIRTTNPVATEQYGSQcCaLlMnAeEpAqDeDYd-1.882617-1.5738700.308747AEEEIRTTNPVATEQYGSvVTTNLQlGGNG1.0127041.9478070.935103DEEEIACTNPVATEQYGVVSTNLQQGNV1.2551230.2944330.960690AEEWIRTTNPIATEQYQSVSTNLQRGNL-4.471093-3.4450901.026003SEVEITCTNPVATEHYGGViSaEgDPpEiQHvTlF-4.233914-5.2684751.034561VEEEIVCTYPVATEFYGSVmSYnEQQHGqNgEg-6.651062-5.5961341.054928DEEEIRTTNPVATEQYGCVATNLQTPeNR1.8469620.7623051.084657DEEEIRTTNPVATpEQYGSVSTNLQRGvNR-4.386056-3.2883471.097709DEEEIRTTNPVATEQYGSVdSTNLQRMmNR0.411793-1.7694862.181279TEEEIfTTTNPVmAYEEYGqCtIqSvTqNLsAHGgNEi-7.337230-5.1383272.198903StEtAEIACTNPVAYEQWGgCCATNLQeAaEgSnEe-5.779138-3.4715102.307627NDEEIGQTNPAATEIYGSTSENLQRGNM-4.214133-0.8606403.353493DEEEIRTQNPVATEQYGSVSTNLQQGNN-3.6865421.3192815.005822


## Pistes d'améliorations : 

[ ] Génération in silico (Partie A) : Implémenter un algorithme appliquant une loi de probabilité (ex: distribution de Poisson pour 3 à 5 mutations en moyenne) afin de générer intelligemment de nouvelles bibliothèques de séquences à tester.

[ ] Scale-up du modèle : Exploiter la version à 650M de paramètres (esm2_t33_650M_UR50D) sur l'intégralité du dataset (300 000 lignes) pour réduire, voire supprimer les biais statistiques actuels.

[ ] Optimisation de la mémoire : Affiner le traitement par lots (batching) pour éviter la saturation de la mémoire unifiée lors de l'extraction sur des datasets massifs.



## Bibliographie

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


 
