Stealth AAV Predictor 

Objectif

Ce projet est une preuve de concept visant à prédire les scores de sélection virale de variants de capsides d'AAV (Adeno-Associated Virus). Il utilise le modèle de langage protéique ESM-2 (développé par Meta) pour extraire les embeddings des séquences, couplé à un modèle de régression (SVR) pour évaluer la viabilité des mutations.

Technologies utilisées

Modèle de Langage Protéique : ESM-2 (fair-esm)

Machine Learning : Scikit-learn (SVR), PyTorch

Manipulation de données : Pandas, NumPy

Optimisation matérielle : Support de l'accélération MPS (Apple Silicon) et CUDA.

Provenance des données et Reproductibilité

Les données complètes utilisées pour l'entraînement original de ce modèle sont issues de recherches spécifiques.

[Lien vers la publication scientifique / le dataset d'origine] (Remplace ceci par ton lien)

Note : Pour des raisons de confidentialité ou de taille, le dataset complet n'est pas hébergé sur ce dépôt.

Pour tester le code localement, assurez-vous de fournir un fichier CSV respectant la structure attendue par le script (colonnes : sequence, viral_selection, etc.).

État du projet (Work in Progress )

Ce code a été initié de manière indépendante pour explorer l'intégration de modèles ML à l'ingénierie des capsides virales.

⚠️ Note : Le développement est actuellement en pause. Le notebook présente l'architecture globale, mais certains bugs d'optimisation (notamment sur la gestion de la mémoire lors du mean pooling) subsistent. La pipeline d'évaluation doit également être renforcée avant de pouvoir interpréter les prédictions finales à grande échelle.

Pistes d'amélioration futures

[ ] Optimiser le traitement par lots (batching) pour éviter les saturations mémoire sur de grands datasets.

[ ] Nettoyer le Jupyter Notebook en extrayant les fonctions clés dans des scripts Python modulaires (.py).

[ ] Implémenter des métriques d'évaluation rigoureuses (corrélations de Spearman/Pearson) sur un jeu de validation dédié.
