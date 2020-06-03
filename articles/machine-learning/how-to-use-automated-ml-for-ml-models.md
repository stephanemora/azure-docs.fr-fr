---
title: Utiliser AutoML pour créer des modèles et les déployer
titleSuffix: Azure Machine Learning
description: Créez, examinez et déployez des modèles de machine learning automatisé avec Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 841d518c02dbc76a172890f6019d78d048f4e8bb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653848"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Créer, examiner et déployer des modèles de machine learning automatisé avec Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dans cet article, vous allez apprendre à créer, à explorer et à déployer des modèles de machine learning automatisé sans la moindre ligne de code dans l’interface du studio Azure Machine Learning. Le machine learning automatisé est un processus dans lequel le meilleur algorithme de machine learning à utiliser pour vos données spécifiques est automatiquement sélectionné. Ce processus vous permet de générer rapidement des modèles Machine Learning. [Apprenez-en davantage sur Machine Learning automatisé](concept-automated-ml.md).
 
Pour obtenir un exemple de bout en bout, consultez le [tutoriel sur la création d’un modèle de classification avec l’interface de ML automatisé d’Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 

Pour une expérience basée sur du code Python, [configurez vos expériences de machine learning automatisé](how-to-configure-auto-train.md) avec le SDK Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Un espace de travail Azure Machine Learning avec un type **Édition Entreprise**. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).  Pour mettre à niveau un espace de travail existant vers Édition Entreprise, consultez [Mise à jour vers Édition Entreprise](how-to-manage-workspace.md#upgrade).

## <a name="get-started"></a>Bien démarrer

1. Connectez-vous à Azure Machine Learning à l’adresse https://ml.azure.com. 

1. Sélectionnez votre abonnement et votre espace de travail. 

1. Accédez au volet gauche. Sélectionnez **Automated ML** (ML automatisé) sous la section **Author** (Auteur).

[![Volet de navigation d’Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 S’il s’agit de votre première expérience, vous verrez une liste vide et des liens vers la documentation. 

Dans le cas contraire, vous verrez une liste de vos expériences récentes Machine Learning automatisé, y compris celles créées avec le kit de développement logiciel (SDK). 

## <a name="create-and-run-experiment"></a>Créer et exécuter une expérience

1. Sélectionnez **+ Nouvelle exécution de ML automatisé** et remplissez le formulaire.

1. Sélectionnez un jeu de données à partir de votre conteneur de stockage ou créez un nouveau jeu de données. Les jeux de données peuvent être créés à partir de fichiers locaux, d’URL Web, de magasins de données ou de Azure Open Datasets. 

    >[!Important]
    > Configuration requise pour les données de formation :
    >* Les données doivent être sous forme tabulaire.
    >* La valeur que vous souhaitez prédire (colonne cible) doit être présente dans les données.

    1. Pour créer un jeu de données à partir d’un fichier sur votre ordinateur local, sélectionnez **Parcourir**, puis sélectionnez le fichier. 

    1. Donnez un nom unique à votre jeu de données et indiquez éventuellement une description. 

    1. Sélectionnez **Suivant** pour ouvrir le **formulaire Sélection d’un magasin de données et de fichiers**. Sur ce formulaire, vous sélectionnez l’emplacement où charger votre jeu de données : choisissez le conteneur de stockage par défaut qui est automatiquement créé avec votre espace de travail ou un conteneur de stockage que vous voulez utiliser pour l’expérience. 

    1. Vérifiez l’exactitude du formulaire **Settings and preview** (Paramètres et aperçu). Le formulaire est rempli intelligemment en fonction du type de fichier. 

        Champ| Description
        ----|----
        Format de fichier| Définit la disposition et le type des données stockées dans un fichier.
        Délimiteur| Un ou plusieurs caractères utilisés pour spécifier la limite entre des régions indépendantes et séparées dans du texte brut ou d’autres flux de données.
        Encodage| Identifie la table de schéma bits/caractères à utiliser pour lire votre jeu de données.
        En-têtes de colonne| Indique la façon dont les éventuels en-têtes du jeu de données sont traités.
        Ignorer les lignes | Indique le nombre éventuel de lignes ignorées dans le jeu de données.
    
        Sélectionnez **Suivant**.

    1. Le formulaire **Schéma** est rempli intelligemment en fonction des sélections effectuées dans le formulaire **Paramètres et aperçu**. Ici, configurez le type de données pour chaque colonne, passez en revue les noms des colonnes et sélectionnez celles à **ne pas inclure** dans votre expérience. 
            
        Sélectionnez **Next** (Suivant).

    1. Le formulaire **Confirmer les détails** est un résumé des informations précédemment renseignées sur les formulaires **Informations de base** et **Paramètres et aperçu**. Vous avez également la possibilité de créer un profil de données pour votre jeu de données à l’aide d’un calcul activé pour le profilage. En savoir plus sur le [profilage des données](#profile).

        Sélectionnez **Suivant**.
1. Sélectionnez votre jeu de données récemment créé une fois qu’il apparaît. Vous pouvez également afficher un aperçu du jeu de données et des exemples de statistiques. 

1. Dans le formulaire **Configurer l’exécution**, entrez un nom d’expérience unique.

1. Sélectionnez une colonne cible ; il s’agit de la colonne sur laquelle vous voulez effectuer des prédictions.

1. Sélectionnez un calcul pour le profilage de données et le travail de formation. Vos calculs existants sont disponibles dans la liste déroulante. Pour créer un calcul, suivez les instructions de l’étape 7.

1. Sélectionnez **Créer un nouveau calcul** afin de configurer votre contexte de calcul pour cette expérience.

    Champ|Description
    ---|---
    Nom du calcul| Entrez un nom unique qui identifie votre contexte de calcul.
    Taille de la machine virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul.
    Nœuds min./max. (dans les paramètres avancés)| Pour profiler des données, vous devez spécifier un ou plusieurs nœuds. Entrez le nombre maximal de nœuds pour votre calcul. La valeur par défaut est de 6 nœuds pour un calcul AML.
    
    Sélectionnez **Create** (Créer). La création d’un calcul peut prendre quelques minutes.

    >[!NOTE]
    > Le nom de votre calcul indique si le *profilage est activé* pour le calcul que vous sélectionnez/créez. (Pour plus d’informations, consultez la section sur le [profilage des données](#profile)).

    Sélectionnez **Suivant**.

1. Dans le formulaire **Type de tâche et paramètres**, sélectionnez le type de tâche : classification, régression ou prévision. Pour plus d’informations, consultez [Guide pratique pour définir des types de tâches](how-to-define-task-type.md).

    1. Pour la classification, vous pouvez également activer l’apprentissage profond qui est utilisé pour la caractérisation du texte.

    1. Pour les prévisions :
        1. Sélectionnez la colonne d'heure : cette colonne contient les données d'heure à utiliser.

        1. Sélectionnez l'horizon de prévision : Indiquez le nombre d’unités de temps (minutes/heures/jours/semaines/mois/années) que le modèle sera en mesure de prédire. Plus le modèle doit prédire dans un avenir lointain, moins il sera précis. [En savoir plus sur les prévisions et l'horizon de prévision](how-to-auto-train-forecast.md).

1. (Facultatif) Voir des paramètres de configuration supplémentaires : paramètres supplémentaires que vous pouvez utiliser pour mieux contrôler la tâche d’entraînement. Sinon, les valeurs par défaut sont appliquées en fonction de la sélection de l’expérience et des données. 

    Configurations supplémentaires|Description
    ------|------
    Métrique principale| Métrique principale utilisée pour évaluer votre modèle. [En savoir plus sur les métriques du modèle](how-to-configure-auto-train.md#explore-model-metrics).
    Caractérisation automatique| Activez ou désactivez le prétraitement effectué par Machine Learning automatisé. Le prétraitement comprend le nettoyage automatique des données, la préparation et la transformation pour générer des fonctionnalités synthétiques. N’est pas pris en charge pour le type de tâche prévision de séries chronologiques. [En savoir plus le prétraitement](#featurization). 
    Expliquer le meilleur modèle | Sélectionnez cette option pour activer ou désactiver l’affichage de l’explicabilité du meilleur modèle recommandé
    Algorithme bloqué| Sélectionnez les algorithmes que vous souhaitez exclure du travail de formation.
    Critère de sortie| Quand l’un de ces critères est satisfait, le travail d’entraînement s’arrête. <br> *Durée du travail de formation (heures)*  : Délai d'exécution du travail de formation. <br> *Seuil de score de métrique* :  Score de métrique minimal pour tous les pipelines. Ainsi, si vous avez défini une métrique cible que vous souhaitez atteindre, vous ne passez pas plus de temps sur le travail de formation que nécessaire.
    Validation| Sélectionnez une des options de validation croisée à utiliser dans le travail de formation. [En savoir plus sur la validation croisée](how-to-configure-auto-train.md).
    Accès concurrentiel| *Nombre maximal d'itérations simultanées* : Nombre maximal de pipelines (itérations) à tester dans le travail de formation. Le travail ne s'exécutera pas au-delà du nombre d’itérations spécifié.

1. (Facultatif) Voir les paramètres de personnalisation : si vous choisissez d’activer **Personnalisation automatique** dans le formulaire **Paramètres de configuration supplémentaires**, ce formulaire est l’emplacement où vous spécifiez les colonnes sur lesquelles effectuer ces personnalisations et où vous sélectionnez la valeur statistique à utiliser pour les imputations des valeurs manquantes.

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>Profilage des données et résumé des statistiques

Vous pouvez obtenir un vaste éventail de statistiques de synthèse dans votre jeu de données afin de vérifier si ce dernier est prêt pour le Machine Learning. Les colonnes non numériques incluent uniquement des statistiques de base telles que min, max et nombre d’erreurs. Les colonnes numériques vous permettent également de consulter les statistiques et quantiles estimés. Plus précisément, notre profil de données inclut :

>[!NOTE]
> Les entrées vides apparaissent pour les fonctionnalités avec types non pertinents.

Statistique|Description
------|------
Fonctionnalité| Nom de la colonne en cours de synthèse.
Profil| Visualisation en ligne en fonction du type déduit. Par exemple, les chaînes, valeurs booléennes et dates incluront des nombres de valeurs, et les décimales (valeurs numériques) des histogrammes approximatifs. Cela vous permet de vous faire une idée rapide de la distribution des données.
Distribution des types| Nombre de valeurs en ligne de types au sein d’une colonne. Les valeurs Null ont un type propre et dès lors, cette visualisation est utile pour détecter les valeurs impaires ou manquantes.
Type|Type déduit de la colonne. Les valeurs possibles incluent : chaînes, valeurs booléennes, dates et décimales.
Min| Valeur minimale de la colonne. Les entrées vides apparaissent pour les fonctionnalités dont le type n’a pas d'ordre inhérent (valeurs booléennes, par exemple).
Max| Valeur maximale de la colonne. 
Count| Nombre total d’entrées manquantes et non manquantes de la colonne.
Non manquant| Nombre d’entrées dans la colonne qui ne sont pas manquantes. Les chaînes vides et les erreurs sont traitées en tant que valeurs et donc n’entrent pas dans « Non manquant ».
Quantiles| Valeurs approximatives à chaque quantile pour donner une idée de la distribution des données.
Moyenne| Moyenne arithmétique ou moyenne de la colonne.
Écart standard| Mesure de la dispersion ou de la variation des données de cette colonne.
Variance| Mesure jusqu’où les données de cette colonne sont déployées par rapport à leur valeur moyenne. 
Asymétrie| Mesure de la différence entre les données de cette colonne et une distribution normale.
Kurtosis| Mesure de la latéralité des données de cette colonne par rapport à une distribution normale.

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>Options avancées de caractérisation

Le Machine Learning automatisé offre automatiquement un prétraitement et des garde-fous des données pour pouvoir identifier et gérer les problèmes potentiels liés à vos données, tels que [le surajustement et les données déséquilibrées](concept-manage-ml-pitfalls.md#prevent-over-fitting). 

### <a name="preprocessing"></a>Prétraitement

> [!NOTE]
> Si vous envisagez d’exporter vos modèles créés de ML automatisé vers un [modèle ONNX](concept-onnx.md), seules les options de caractérisation marquées d’un astérisque (*) sont prises en charge dans le format ONNX. Apprenez-en davantage sur la [conversion de modèles au format ONNX](concept-automated-ml.md#use-with-onnx). 

|Étapes de &nbsp;prétraitement| Description |
| ------------- | ------------- |
|Supprimer les caractéristiques de cardinalité élevée ou d’absence de variance* |Supprimez ces éléments des jeux de formation et de validation, y compris les fonctionnalités dont toutes les valeurs sont manquantes, ayant la même valeur dans toutes les lignes ou présentant une cardinalité très élevée (par exemple des hachages, des ID ou des GUID).|
|Imputer des valeurs manquantes* |Pour les fonctionnalités numériques, remplacement par la moyenne des valeurs dans la colonne.<br/><br/>Pour les fonctionnalités catégorielles, remplacement par la valeur la plus fréquente.|
|Générer des caractéristiques supplémentaires* |Pour les caractéristiques de type date/heure : Année, Mois, Jour, Jour de la semaine, Jour de l’année, Trimestre, Semaine de l’année, Heure, Minute, Seconde.<br/><br/>Pour les caractéristiques de type texte : Fréquence des termes basée sur les unigrammes, les bigrammes et les trigrammes.|
|Transformer et encoder*|Les fonctionnalités numériques avec très peu de valeurs uniques sont transformées en fonctionnalités catégorielles.<br/><br/>Un encodage à chaud est effectué pour les catégories de faible cardinalité et un encodage avec hachage à chaud pour les catégories à cardinalité élevée.|
|Incorporations de mots|Caractériseur de texte convertissant les vecteurs de jetons de texte en vecteurs de phrase à l’aide d’un modèle déjà formé. Le vecteur d’incorporation de chaque mot d’un document est agrégé pour produire un vecteur de fonctionnalité de document.|
|Encodages cibles|Pour les fonctionnalités catégorielles, mappe chaque catégorie avec la valeur cible moyenne pour les problèmes de régression, et à la probabilité de chaque classe pour les problèmes de classification. Une pondération basée sur la fréquence et une validation croisée par échantillons (« k-fold ») sont appliquées pour réduire l’ajustement du mappage et le bruit provoqué par les catégories de données éparses.|
|Encodage de texte cible|Pour l'entrée de texte, un modèle linéaire empilé avec « bag-of-words » est utilisé afin de générer la probabilité de chaque classe.|
|WoE (Weight of Evidence)|Calcule la valeur WoE en tant que mesure de corrélation des colonnes catégorielles vers la colonne cible. Elle est calculée en tant qu'enregistrement du ratio de probabilités à l'intérieur et à l'extérieur de la classe. Cette étape génère une colonne de fonctionnalités numériques par classe et évite d'avoir à imputer les valeurs manquantes et le traitement de valeur hors norme.|
|Distance de cluster|Effectue l’apprentissage d’un modèle de clustering k-moyennes sur toutes les colonnes numériques.  Génère de nouvelles fonctionnalités k, une nouvelle fonctionnalité numérique par cluster, contenant la distance de chaque échantillon par rapport au centroïde de chaque cluster.|

### <a name="data-guardrails"></a>Garde-fous des données

Les garde-fous des données sont appliqués quand la caractérisation automatique est activée ou que la validation est définie sur automatique. Les garde-fous des données vous aident à identifier les problèmes potentiels liés à vos données (par exemple les valeurs manquantes ou le déséquilibre des classes) et à entreprendre des actions correctives afin d’améliorer les résultats. 

Les utilisateurs peuvent consulter les garde-fous des données dans le studio sous l’onglet **Garde-fous des données** d’une exécution du ML automatisé ou en définissant ```show_output=True``` lors de l’envoi d’une expérience à l’aide du Kit de développement logiciel (SDK) Python. 

#### <a name="data-guardrail-states"></a>États des garde-fous des données

Les garde-fous des données affichent l’un des trois états suivants : **Réussi**, **Terminé** ou **Alerté**.

State| Description
----|----
Passed| Aucun problème de données n’a été détecté et aucune action de l’utilisateur n’est requise. 
Terminé| Des modifications ont été appliquées à vos données. Nous encourageons les utilisateurs à passer en revue les actions correctives prises par le ML automatisé pour vérifier que les modifications s’alignent sur les résultats attendus. 
Alerté| Un problème de données qui n’a pas pu être résolu a été détecté. Nous encourageons les utilisateurs à examiner et à résoudre le problème. 

>[!NOTE]
> Les versions précédentes des expériences de ML automatisé affichaient un quatrième État : **Corrigé**. Les expériences plus récentes n’affichent pas cet état, et tous les garde-fous qui affichaient l’état **Corrigé** affichent désormais l’état **Terminé**.   

Le tableau suivant décrit les garde-fous des données pris en charge ainsi que les états associés que les utilisateurs peuvent rencontrer lors de l’envoi de leur expérience.

Garde-fou|Statut|Condition&nbsp;pour&nbsp;le déclencheur
---|---|---
Imputation des valeurs de caractéristique manquantes |**Passed** <br><br><br> **Done**| Aucune valeur de caractéristique manquante n’a été détectée dans vos données d’entraînement. Découvrez plus d’informations sur l’[imputation d’une valeur manquante](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options). <br><br> Des valeurs de caractéristique manquantes ont été détectées dans vos données d’entraînement et imputées.
Gestion des caractéristiques à cardinalité élevée |**Passed** <br><br><br> **Done**| Aucune caractéristique à cardinalité élevée n’a été détectée après analyse de vos entrées. Découvrez-en plus sur la [détection de caractéristiques à cardinalité élevée](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options). <br><br> Des caractéristiques à cardinalité élevée ont été détectées dans vos entrées et ont été gérées.
Gestion du fractionnement de la validation |**Done**| *La configuration de la validation a été définie sur « auto » et les données d’entraînement contenaient **moins** de 20 000 lignes.* <br> Chaque itération du modèle entraîné a été validée à l’aide de la validation croisée. Découvrez-en plus sur les [données de validation](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> *La configuration de la validation a été définie sur « auto » et les données d’entraînement contenaient **plus** de 20 000 lignes.* <br> Les données d’entrée ont été divisées en un jeu de données d’entraînement et un jeu de données de validation pour la validation du modèle.
Détection de l’équilibrage des classes |**Passed** <br><br><br><br> **Alerted** | Vos entrées ont été analysées et toutes les classes sont équilibrées dans vos données d’entraînement. Un jeu de données est considéré comme équilibré si chaque classe a une bonne représentation dans le jeu de données, telle que mesurée par le nombre et le ratio des échantillons. <br><br><br> Des classes déséquilibrées ont été détectées dans vos entrées. Pour corriger le biais du modèle, résolvez le problème d’équilibrage. Découvrez-en plus sur les [données déséquilibrées](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).
Détection des problèmes de mémoire |**Passed** <br><br><br><br> **Done** |<br> Aucun problème potentiel d’insuffisance de mémoire n’a été détecté après analyse des valeurs {horizon, décalage, fenêtre dynamique} sélectionnées. Découvrez-en plus sur les [configurations de prévision](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) de série chronologique. <br><br><br>Les valeurs {horizon, décalage, fenêtre dynamique} sélectionnées ont été analysées et peuvent entraîner une insuffisance de mémoire dans votre expérience. Les configurations de la fenêtre dynamique ou de décalage ont été désactivées.
Détection de la fréquence |**Passed** <br><br><br><br> **Done** |<br> La série chronologique a été analysée et tous les points de données sont alignés sur la fréquence détectée. <br> <br> La série chronologique a été analysée et les points de données qui ne sont pas alignés sur la fréquence détectée ont été détectés. Ces points de données ont été supprimés du jeu de données. Découvrez-en plus sur la [préparation des données pour la prévision de série chronologique](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="run-experiment-and-view-results"></a>Exécuter une expérience et afficher les résultats

Sélectionnez **Terminer** pour exécuter votre expérience. Le processus de préparation de l’expérience peut prendre jusqu’à 10 minutes. Les travaux de formation peuvent prendre 2 à 3 minutes supplémentaires pour que chaque pipeline se termine.

### <a name="view-experiment-details"></a>Afficher les détails de l'expérience

L’écran **Détails de l’exécution** ouvre l’onglet **Détails**. Cet écran vous montre un récapitulatif de l’exécution de l’expérience, notamment une barre d’état en haut à côté du numéro de l’exécution. 

L’onglet **Modèles** contient une liste des modèles créés affichés selon leur score de métrique. Par défaut, le modèle qui obtient la valeur la plus élevée d’après la métrique choisie figure en haut de la liste. À mesure que le travail de formation essaie plus de modèles, ceux-ci sont ajoutés à la liste. Utilisez cela pour obtenir une comparaison rapide des métriques des modèles déjà produits.

[![Tableau de bord des détails de l’exécution](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Afficher les détails de l'exécution de formation

Explorez les modèles terminés pour voir les détails de l’exécution de la formation, par exemple l’exécution des métriques sous l’onglet **Détails du modèle** ou les graphiques de performances sous l’onglet **Visualisations**. [En savoir plus sur les graphiques](how-to-understand-automated-ml.md).

[![Détails de l’itération](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Déployer votre modèle

Une fois le meilleur modèle disponible, déployez-le en tant que service web pour prédire de nouvelles données.

Machine Learning automatisé vous aide à déployer le modèle sans écrire de code :

1. Vous disposez de plusieurs options pour le déploiement. 

    + Option 1 : Pour déployer le meilleur modèle (selon les critères de métrique que vous avez définis), sélectionnez le bouton **Déployer le meilleur modèle** sous l’onglet **Détails**.

    + Option n°2 : Pour déployer une itération de modèle spécifique à partir de cette expérience, explorez le modèle pour ouvrir son onglet **Détails du modèle**, puis sélectionnez **Déployer le modèle**.

1. Renseignez le volet **Déployer le modèle**.

    Champ| Valeur
    ----|----
    Nom| Entrez un nom unique pour votre déploiement.
    Description| Entrez une description pour mieux identifier le but de ce déploiement.
    Type de capacité de calcul| Sélectionnez le type de point de terminaison que vous voulez déployer : *Azure Kubernetes Service (AKS)* ou *Azure Container Instance (ACI)* .
    Nom du calcul| *S’applique uniquement à AKS :* Sélectionnez le nom du cluster AKS sur lequel vous voulez effectuer le déploiement.
    Activer l’authentification | Sélectionnez cette option pour l’authentification basée sur des jetons ou sur des clés.
    Utiliser les ressources d’un déploiement personnalisé| Activez cette fonctionnalité si vous voulez télécharger votre propre script de scoring et votre propre fichier d’environnement. [Découvrez plus d’informations sur les scripts de scoring](how-to-deploy-and-where.md#script).

    >[!Important]
    > Les noms de fichiers sont limités à 32 caractères et doivent commencer et se terminer par des caractères alphanumériques. Ils peuvent inclure des tirets, des traits de soulignement, des points et des caractères alphanumériques. Les espaces ne sont pas autorisés.

    Le menu *Avancé* offre des fonctionnalités de déploiement par défaut, comme la [collecte de données](how-to-enable-app-insights.md) et des paramètres d’utilisation des ressources. Si vous souhaitez remplacer ces valeurs par défaut, faites-le dans ce menu.

1. Sélectionnez **Déployer**. Le déploiement peut prendre environ 20 minutes.

Vous disposez maintenant d’un service web opérationnel pour générer des prédictions ! Vous pouvez tester les prédictions en interrogeant le service à partir du [support Azure Machine Learning intégré de Power BI](how-to-consume-web-service.md#consume-the-service-from-power-bi).

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur l'utilisation d'un service web](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service).
* [Comprendre les résultats du Machine Learning](how-to-understand-automated-ml.md).
* [En savoir plus sur Machine Learning automatisé](concept-automated-ml.md) et Azure Machine Learning.
