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

5. Tests et evaluation des résultats, génération d'un fichier rapport en .txt


S'ajoute ensuite la partie génération de nouvelles capsides : 

Elle intègre un bloc d'initialisation, qui permet de charger un modèle déjà créé (via le premier programme)
Ensuite, on retrouve le même bloc de test et d'évaluation afin de s'assurer que l'initialisation a bien fonctionné. 

En développement : 
Génération aléatoire de mutations, qui passeront dans le modèle de prédiction SVR afin de découvrir les capsides les plus efficaces 

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


En revanche, vous pouvez télécharger [ici](https://huggingface.co/MaloBSL/Stealth_AAV_Predictor) (vers Hugging Face) le modèle pré-entraîné sur 50 000 séquences de capsides d'AAV.


### Exemple de run : 
Un premier entraînement a été validé sur un échantillon de 50 000 séquences.

Modèle ESM-2 utilisé : esm2_t6_8M_UR50D (8 millions de paramètres).

Temps d'exécution : Environ 1h30 sur une puce Apple Silicon M1 Pro pour l'extraction et l'entraînement.


Test d'inférence (Séquences inédites) :
Le modèle montre une précision remarquable (erreur absolue < 0.6) sur les séquences modérément mutées. Sur les variants très atypiques ou présentant de larges délétions/insertions, on observe un "biais pessimiste" (le modèle prédit un score négatif même si la séquence est viable), attribuable à la petite taille du modèle ESM-2 testé et à la forte proportion de séquences non-viables dans l'échantillon d'apprentissage.

Le modèle montre une précision remarquable (erreur absolue < 0.6) sur les séquences modérément mutées. Sur les variants très atypiques ou présentant de larges délétions/insertions, on observe un "biais pessimiste" (le modèle prédit un score négatif même si la séquence est viable), attribuable à la petite taille du modèle ESM-2 testé et à la forte proportion de séquences non-viables dans l'échantillon d'apprentissage.

| Séquence (boucle) | Score Réel | Score Prédit | Différence absolue |
| :--- | :---: | :---: | :---: |
| `AEEEIRTTNPVATEQYGSVStTlNqQqTnQqGtNvTe` | 1.154430 | 1.146970 | 0.007460 |
| `DEEEIRTTNPVATEQYGSVsTvNqNnQqATtTnVaTe` | 1.348170 | 1.364028 | 0.015858 |
| `DEEEIRTTNPVATEQYGCVcSTELnQeGNsNQ` | 1.193242 | 1.170773 | 0.022469 |
| `DEEEIRTTNPVpATEQYGSVSPNLQRGNR` | -4.678155 | -4.729361 | 0.051206 |
| `DEEGIRTTNPVATEQYGSVSTNLQyRGNR` | -3.885840 | -3.834570 | 0.051270 |
| `DEEEIRTTNPVATEQYGSVcSpTNgLERnDdTlEs` | 1.116348 | 1.005419 | 0.110929 |
| `DEEEIRTTNPVATEQYGSVSTdNgLdQeMGnNGg` | 0.997888 | 1.117535 | 0.119648 |
| `StEtAEIATTNPVAYEpPWGSVSnAnQPDtMnPeSeNw` | -5.253893 | -5.415245 | 0.161352 |
| `DEEEICTTNPVATEQYGSVSTdNgGQQGNhN` | 0.862518 | 1.107479 | 0.244961 |
| `DEEEIRTTNPVATEQYGqCVcSnTgEdHnAeFfGsDnQs` | 0.816394 | 0.522354 | 0.294040 |
| `DEEEIRTTNPVATEQYGSQcCaLlMnAeEpAqDeDYd` | -1.882617 | -1.573870 | 0.308747 |
| `AEEEIRTTNPVATEQYGSvVTTNLQlGGNG` | 1.012704 | 1.947807 | 0.935103 |
| `DEEEIACTNPVATEQYGVVSTNLQQGNV` | 1.255123 | 0.294433 | 0.960690 |
| `AEEWIRTTNPIATEQYQSVSTNLQRGNL` | -4.471093 | -3.445090 | 1.026003 |
| `SEVEITCTNPVATEHYGGViSaEgDPpEiQHvTlF` | -4.233914 | -5.268475 | 1.034561 |
| `VEEEIVCTYPVATEFYGSVmSYnEQQHGqNgEg` | -6.651062 | -5.596134 | 1.054928 |
| `DEEEIRTTNPVATEQYGCVATNLQTPeNR` | 1.846962 | 0.762305 | 1.084657 |
| `DEEEIRTTNPVATpEQYGSVSTNLQRGvNR` | -4.386056 | -3.288347 | 1.097709 |
| `DEEEIRTTNPVATEQYGSVdSTNLQRMmNR` | 0.411793 | -1.769486 | 2.181279 |
| `TEEEIfTTTNPVmAYEEYGqCtIqSvTqNLsAHGgNEi` | -7.337230 | -5.138327 | 2.198903 |
| `StEtAEIACTNPVAYEQWGgCCATNLQeAaEgSnEe` | -5.779138 | -3.471510 | 2.307627 |
| `NDEEIGQTNPAATEIYGSTSENLQRGNM` | -4.214133 | -0.860640 | 3.353493 |
| `DEEEIRTQNPVATEQYGSVSTNLQQGNN` | -3.686542 | 1.319281 | 5.005822 |



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


 
