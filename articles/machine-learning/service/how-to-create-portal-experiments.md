---
title: Utiliser l’interface de ML automatisé d’Azure pour entraîner et déployer des modèles
titleSuffix: Azure Machine Learning service
description: Créez, gérez et déployez des expériences de Machine Learning automatisé dans la page d’accueil de l’espace de travail d’Azure Machine Learning (préversion).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 3ee15b5485f4fc0f81788107ce2378c65085e000
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910415"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learnings-workspace-landing-page-preview"></a>Créer, explorer et déployer des expériences de Machine Learning automatisé avec la page d’accueil de l’espace de travail d’Azure Machine Learning (préversion)

 Dans cet article, vous allez apprendre à créer, explorer et déployer des expériences de Machine Learning automatisé dans la page d’accueil de l’espace de travail Azure Machine Learning, sans la moindre ligne de code. Le Machine Learning automatisé automatise le processus de sélection de l'algorithme le plus adapté à vos données, et vous permet de générer rapidement un modèle Machine Learning. [Apprenez-en davantage sur Machine Learning automatisé](concept-automated-ml.md).

 Si vous préférez une expérience basée sur du code, vous pouvez également [configurer vos expériences Machine Learning dans Python](how-to-configure-auto-train.md) avec le [kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Un espace de travail de service Microsoft Azure Machine Learning. Consultez [Créer un espace de travail pour Azure Machine Learning service](how-to-manage-workspace.md).

## <a name="get-started"></a>Prise en main


1. Connectez-vous à la [page d’accueil de l’espace de travail](https://ml.azure.com/workspaceportal/). 

1. Sélectionnez votre abonnement et votre espace de travail. 

1. Accédez au volet gauche. Sélectionnez **Automated ML** (ML automatisé) sous la section **Authoring** (Création).

[![Volet de navigation du portail Azure](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 S’il s’agit de vos premières expériences, l’écran **Get started** (Démarrer) s’affiche. 

Sinon, votre tableau de bord **Machine Learning automatisé** s’affiche, offrant une vue d’ensemble de toutes vos expériences Machine Learning automatisé, dont celles créées avec le kit SDK. Là, vous pouvez filtrer et explorer vos exécutions par date, nom d'expérience et état d’exécution.

## <a name="create-and-run-experiment"></a>Créer et exécuter une expérience

1. Sélectionnez **Create Experiment** (Créer une expérience) et remplissez le formulaire.

1. Entrez un nom d’expérience unique.

1. Sélectionnez un calcul pour le profilage de données et le travail de formation. Vos calculs existants sont disponibles dans la liste déroulante. Pour créer un calcul, suivez les instructions de l’étape 4.

1. Sélectionnez **Créer un nouveau calcul** afin de configurer votre contexte de calcul pour cette expérience.

    Champ|Description
    ---|---
    Nom du calcul| Entrez un nom unique qui identifie votre contexte de calcul.
    Taille de la machine virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul.
    Paramètres supplémentaires| *Nœuds min* : Entrez le nombre minimal de nœuds pour votre calcul. Le nombre minimal de nœuds pour un calcul AML est 0. Pour activer le profilage des données, vous devez disposer d'un nœud minimum. <br> *Nœuds max* : Entrez le nombre maximal de nœuds pour votre calcul. La valeur par défaut est de 6 nœuds pour un calcul AML.
    
    Sélectionnez **Create** (Créer). La création d’un calcul peut prendre quelques minutes.

    >[!NOTE]
    > Le nom de votre calcul indique si le *profilage est activé* pour le calcul que vous sélectionnez/créez. (Pour plus d’informations, consultez la section sur le [profilage des données](#profile)).

1. Sélectionnez un jeu de données de votre conteneur de stockage ou créez-en un en chargeant un fichier de votre ordinateur local sur le conteneur. La préversion publique prend uniquement en charge les chargements de fichiers locaux et les comptes de stockage d’objets blob Azure.

    >[!Important]
    > Configuration requise pour les données de formation :
    >* Les données doivent être sous forme tabulaire.
    >* La valeur que vous souhaitez prédire (colonne cible) doit être présente dans les données.

    1. Pour créer un jeu de données à partir d’un fichier sur votre ordinateur local, sélectionnez **Browse** (Parcourir), puis sélectionnez le fichier. 

    1. Donnez un nom unique à votre jeu de données et indiquez éventuellement une description. 

    1. Sélectionnez **Next** (Suivant) pour le charger dans le conteneur de stockage par défaut qui est automatiquement créé avec votre espace de travail ou choisissez un conteneur de stockage à utiliser pour l’expérience. 

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

1. Sélectionnez le type de travail de formation : classification, régression ou prévisions.

1. Sélectionnez la colonne cible ; il s’agit de la colonne sur laquelle vous voulez effectuer des prédictions.

1. Pour les prévisions :
    1. Sélectionnez la colonne d'heure : cette colonne contient les données d'heure à utiliser.

    1. Sélectionnez l'horizon de prévision : Indiquez le nombre d’unités de temps (minutes/heures/jours/semaines/mois/années) que le modèle sera en mesure de prédire. Plus le modèle doit prédire dans un avenir lointain, moins il sera précis. [En savoir plus sur les prévisions et l'horizon de prévision](how-to-auto-train-forecast.md).

1. (Facultatif) Paramètres avancés : paramètres supplémentaires que vous pouvez utiliser pour mieux contrôler le travail de formation. Sinon, les valeurs par défaut sont appliquées en fonction de la sélection de l’expérience et des données. 

    Paramètres avancés|Description
    ------|------
    Métrique principale| Métrique principale utilisée pour évaluer votre modèle. [En savoir plus sur les métriques du modèle](how-to-configure-auto-train.md#explore-model-metrics).
    Critères de sortie| Lorsqu’un de ces critères est satisfait, le travail de formation se termine avant la fin. <br> *Délai du travail de formation (minutes)*  : Délai d'exécution du travail de formation.  <br> *Nombre maximal d’itérations* : Nombre maximal de pipelines (itérations) à tester dans le travail de formation. Le travail ne s'exécutera pas au-delà du nombre d’itérations spécifié. <br> *Seuil de score de métrique* :  Score de métrique minimal pour tous les pipelines. Ainsi, si vous avez défini une métrique cible que vous souhaitez atteindre, vous ne passez pas plus de temps sur le travail de formation que nécessaire.
    Prétraitement| Activez ou désactivez le prétraitement effectué par Machine Learning automatisé. Le prétraitement comprend le nettoyage automatique des données, la préparation et la transformation pour générer des fonctionnalités synthétiques. [En savoir plus le prétraitement](#preprocess).
    Validation| Sélectionnez une des options de validation croisée à utiliser dans le travail de formation. [En savoir plus sur la validation croisée](how-to-configure-auto-train.md).
    Accès concurrentiel| Sélectionnez les limites de cœurs multiples à utiliser lors d'un calcul multicœur.
    Algorithme bloqué| Sélectionnez les algorithmes que vous souhaitez exclure du travail de formation.

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
max| Valeur maximale de la colonne. 
Count| Nombre total d’entrées manquantes et non manquantes de la colonne.
Non manquant| Nombre d’entrées dans la colonne qui ne sont pas manquantes. Les chaînes vides et les erreurs sont traitées en tant que valeurs et donc n’entrent pas dans « Non manquant ».
Quantiles| Valeurs approximatives à chaque quantile pour donner une idée de la distribution des données.
Moyenne| Moyenne arithmétique ou moyenne de la colonne.
Écart standard| Mesure de la dispersion ou de la variation des données de cette colonne.
Variance| Mesure jusqu’où les données de cette colonne sont déployées par rapport à leur valeur moyenne. 
Asymétrie| Mesure de la différence entre les données de cette colonne et une distribution normale.
Kurtosis| Mesure de la latéralité des données de cette colonne par rapport à une distribution normale.

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>Options de prétraitement avancé

Lorsque vous configurez vos expériences, vous pouvez activer le paramètre avancé `Preprocess`. Ainsi, les étapes de prétraitement et de personnalisation des données sont effectuées automatiquement.

|Étapes de &nbsp;prétraitement| Description |
| ------------- | ------------- |
|Suppression des cardinalités élevées ou aucune caractéristique de variance|Supprimez ces éléments des jeux de formation et de validation, y compris les fonctionnalités dont toutes les valeurs sont manquantes, ayant la même valeur dans toutes les lignes ou présentant une cardinalité très élevée (par exemple des hachages, des ID ou des GUID).|
|Imputer des valeurs manquantes|Pour les fonctionnalités numériques, remplacement par la moyenne des valeurs dans la colonne.<br/><br/>Pour les fonctionnalités catégorielles, remplacement par la valeur la plus fréquente.|
|Générer des caractéristiques supplémentaires|Pour les caractéristiques de type date/heure : Année, Mois, Jour, Jour de la semaine, Jour de l’année, Trimestre, Semaine de l’année, Heure, Minute, Seconde.<br/><br/>Pour les caractéristiques de type texte : Fréquence des termes basée sur les unigrammes, les bigrammes et les trigrammes.|
|Transformer et encoder |Les fonctionnalités numériques avec très peu de valeurs uniques sont transformées en fonctionnalités catégorielles.<br/><br/>Un encodage à chaud est effectué pour les catégories de faible cardinalité et un encodage avec hachage à chaud pour les catégories à cardinalité élevée.|
|Incorporations de mots|Caractériseur de texte convertissant les vecteurs de jetons de texte en vecteurs de phrase à l’aide d’un modèle déjà formé. Le vecteur d'incorporation de chaque mot d'un document est agrégé pour produire un vecteur de fonctionnalité de document.|
|Encodages cibles|Pour les fonctionnalités catégorielles, mappe chaque catégorie avec la valeur cible moyenne pour les problèmes de régression, et à la probabilité de chaque classe pour les problèmes de classification. Une pondération basée sur la fréquence et une validation croisée par échantillons (« k-fold ») sont appliquées pour réduire l’ajustement du mappage et le bruit provoqué par les catégories de données éparses.|
|Encodage de texte cible|Pour l'entrée de texte, un modèle linéaire empilé avec « bag-of-words » est utilisé afin de générer la probabilité de chaque classe.|
|WoE (Weight of Evidence)|Calcule la valeur WoE en tant que mesure de corrélation des colonnes catégorielles vers la colonne cible. Elle est calculée en tant qu'enregistrement du ratio de probabilités à l'intérieur et à l'extérieur de la classe. Cette étape génère une colonne de fonctionnalités numériques par classe et évite d'avoir à imputer les valeurs manquantes et le traitement de valeur hors norme.|
|Distance de cluster|Effectue l’apprentissage d’un modèle de clustering k-moyennes sur toutes les colonnes numériques.  Génère de nouvelles fonctionnalités k, une nouvelle fonctionnalité numérique par cluster, contenant la distance de chaque échantillon par rapport au centroïde de chaque cluster.|

## <a name="run-experiment-and-view-results"></a>Exécuter une expérience et afficher les résultats

Sélectionnez **Démarrer** pour exécuter votre expérience. Le processus de préparation de l'expérience prend quelques minutes.

### <a name="view-experiment-details"></a>Afficher les détails de l'expérience

Une fois la phase de préparation de l’expérience terminée, vous voyez l’écran Détails de l’exécution qui commence à se remplir. Cet écran vous donne une liste complète des modèles créés. Par défaut, le modèle qui obtient la valeur la plus élevée d’après la métrique choisie figure en haut de la liste. À mesure que le travail de formation essaie plus de modèles, ceux-ci sont ajoutés à la liste d’itérations et au graphique. Utilisez le graphique des itérations pour une comparaison rapide des métriques des modèles déjà produits.

Dans le cadre des travaux de formation, l'exécution de chaque pipeline peut nécessiter un certain temps.

[![Tableau de bord des détails de l’exécution](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>Afficher les détails de l'exécution de formation

Explorez les modèles de sortie pour afficher les détails de l’exécution, tels que les métriques de performances et les graphiques de distribution. [En savoir plus sur les graphiques](how-to-understand-automated-ml.md).

[![Détails de l’itération](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>Déployer votre modèle

Une fois le meilleur modèle disponible, déployez-le en tant que service web pour prédire de nouvelles données.

Machine Learning automatisé vous aide à déployer le modèle sans écrire de code :

1. Vous disposez de plusieurs options pour le déploiement. 

    + Option 1 : Pour déployer le meilleur modèle selon les critères de métrique que vous avez définis, sélectionnez Déployer le meilleur modèle dans la page Détails de l’exécution.

    + Option 2 : Pour déployer une itération de modèle spécifique à partir de cette expérience, explorez le modèle pour ouvrir sa page Détails de l’exécution, puis sélectionnez Déployer le modèle.

1. Renseignez le volet **Deploy Model** (Déployer le modèle).

    Champ| Valeur
    ----|----
    Nom du déploiement| Entrez un nom unique pour votre déploiement.
    Description du déploiement| Entrez une description pour mieux identifier le but de ce déploiement.
    Script de scoring| Générez automatiquement ou chargez votre propre fichier de scoring. [En savoir plus sur le script de scoring](how-to-deploy-and-where.md#script)
    Script d’environnement| Générez automatiquement ou chargez votre propre fichier d’environnement.
    >[!Important]
    > Les noms de fichiers sont limités à 32 caractères et doivent commencer et se terminer par des caractères alphanumériques. Ils peuvent inclure des tirets, des traits de soulignement, des points et des caractères alphanumériques. Les espaces ne sont pas autorisés.

1. Sélectionnez **Déployer**. Le déploiement peut prendre environ 20 minutes.

    Le message suivant s’affiche une fois le déploiement terminé.

    ![Déploiement terminé](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

Vous disposez maintenant d’un service web opérationnel pour générer des prédictions !

## <a name="next-steps"></a>Étapes suivantes

* Essayez le [tutoriel de bout en bout pour créer votre première expérience ML automatisé avec Azure Machine Learning](tutorial-first-experiment-automated-ml.md). 
* [En savoir plus sur Machine Learning automatisé](concept-automated-ml.md) et Azure Machine Learning.
* [Comprendre les résultats du Machine Learning](how-to-understand-automated-ml.md).
* [En savoir plus sur l'utilisation d'un service web](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service).
