---
title: Analyser et surveiller la dérive des données sur les jeux de données (préversion)
titleSuffix: Azure Machine Learning
description: Créez des superviseurs de jeux de données Azure Machine Learning (préversion), surveillez la dérive des données dans les jeux de données et configurez des alertes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 4efdc47e65f0f29f74f1477b02efdc6b8767ffb2
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264761"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Détecter une dérive de données (préversion) sur des jeux de données
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous découvrez comment créer des superviseurs de jeu de données Azure Machine Learning (préversion), comment surveiller la dérive des données et les modifications statistiques dans les jeux de données, et comment configurer des alertes.

Avec les analyses de jeux de données Azure Machine Learning, vous pouvez :
* **Analyser la dérive de vos données** pour comprendre comment elles évoluent au fil du temps.
* **Surveiller les données de modèle** pour identifier les différences entre les jeux de données de formation et de service.
* **Surveiller les nouvelles données** pour identifier les différences entre le jeu de données de référence et le jeu de données cible.
* **Profiler les caractéristiques dans les données** pour suivre la façon dont les propriétés statistiques évoluent au fil du temps.
* **Configurer des alertes sur la dérive des données** pour les premiers avertissements relatifs aux problèmes potentiels. 

Les métriques et insights sont disponibles par le biais de la ressource [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associée à l’espace de travail Azure Machine Learning.

> [!Important]
> Notez que la surveillance de la dérive des données avec le SDK est disponible dans toutes les éditions, tandis que la surveillance de la dérive des données via le studio sur le web est disponible uniquement dans l’édition Entreprise.

## <a name="prerequisites"></a>Conditions préalables requises

Pour créer et utiliser des analyses de jeux de données, vous avez besoin des éléments suivants :
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.
* Un [espace de travail Azure Machine Learning](how-to-manage-workspace.md).
* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.
* Données structurées (tabulaires) avec un horodatage spécifié dans le chemin d’accès du fichier, le nom de fichier ou la colonne dans les données.

## <a name="what-is-data-drift"></a>Qu’est-ce qu’une dérive de données ?

Dans le contexte de l’apprentissage automatique, la dérive de données est la modification des données d’entrée du modèle qui entraîne une dégradation des performances du modèle. C’est l’une des principales raisons pour lesquelles la précision du modèle se dégrade au fil du temps, de sorte que la surveillance de la dérive des données permet de détecter les problèmes de performance du modèle.

Les causes de la dérive des données sont les suivantes : 

- Modifications de processus en amont, comme le remplacement d’un capteur, lequel ne prend plus les mesures en pouces mais en centimètres. 
- Problèmes de qualité des données, par exemple un capteur cassé indique toujours une valeur nulle.
- Dérive naturelle des données, comme la variation de la température moyenne au fil des saisons.
- Modification de la relation entre caractéristiques ou écart de covariable. 

Les analyseurs de jeux de données Azure Machine Learning permettent de configurer des alertes facilitant la détection de la dérive des données dans les jeux de données au fil du temps. 

### <a name="dataset-monitors"></a>Analyses de jeu de données 

Vous pouvez créer une analyse de jeu de données pour détecter la dérive des données sur les nouvelles données d’un jeu de données, produire des alertes en conséquence, analyser les données d’historique révélant la dérive des données et profiler les nouvelles données au fil du temps. L’algorithme de dérive des données fournit une mesure globale des modifications apportées aux données et indique les caractéristiques qui sont responsables de l’investigation. Les superviseurs de jeu de données produisent un certain nombre d’autres métriques en profilant les nouvelles données dans le jeu de données `timeseries`. Des alertes personnalisées peuvent être configurées sur toutes les métriques générées par le superviseur via [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Les superviseurs de jeu de données permettent de détecter rapidement les problèmes des données et de réduire le temps nécessaire au débogage du problème en identifiant ses causes probables.  

Conceptuellement, il existe trois scénarios principaux pour la configuration des analyses de jeux de données dans Azure Machine Learning.

Scénario | Description
---|---
Surveillance des données de service d’un modèle afin de détecter toute dérive des données d’apprentissage du modèle | Les résultats de ce scénario sont le fruit de la surveillance d’un proxy en vue de vérifier l’exactitude du modèle, étant donné que celle-ci se dégrade si les données de service dérivent des données d’apprentissage.
Analyse d’un jeu de données de série chronologique pour la dérive d’une période précédente. | Ce scénario est plus général et peut être utilisé pour analyser les jeux de données impliqués en amont ou en aval de la génération de modèles.  Le jeu de données cible doit posséder une colonne d'horodatage, tandis que le jeu de données de base peut être n’importe quel jeu de données tabulaires ayant des caractéristiques en commun avec le jeu de données cible.
Exécution de l’analyse des données passées. | Ce scénario peut permettre de comprendre les données historiques et éclairer les décisions de définition des paramètres des moniteurs de jeu de données.

## <a name="how-dataset-can-monitor-data"></a>Comment un jeu de données peut superviser les données

Avec Azure Machine Learning, la dérive de données est supervisée par le biais de jeux de données. Pour superviser la dérive des données, un jeu de données de référence (généralement le jeu de données d’entraînement d’un modèle) est spécifié. Un jeu de données cible (généralement constitué de données d’entrée de modèle) est comparé à votre jeu de données de référence au fil du temps. Cette comparaison signifie que votre jeu de données cible doit comporter une colonne d’horodatage (timestamp).

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Définir la caractéristique `timeseries` dans le jeu de données cible

Le jeu de données cible doit avoir la caractéristique `timeseries` définie sur cette valeur en spécifiant la colonne timestamp à partir d’une colonne de données ou d’une colonne virtuelle dérivée du modèle de chemin d’accès des fichiers. Pour ce faire, vous pouvez utiliser le SDK Python ou Azure Machine Learning Studio. Une colonne représentant un horodatage « précis » doit être spécifiée pour ajouter une caractéristique `timeseries` au jeu de données. Si vos données sont partitionnées dans une structure de dossiers avec des informations temporelles, telles que « {yyyyyy/MM/dd} », vous pouvez créer une colonne virtuelle via le paramètre de modèle de chemin d’accès et la définir comme horodatage « grossier » afin de mettre l’accent sur les séries temporelles. 

#### <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

Dans la classe [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-), la méthode [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) définit la colonne horodatage du jeu de données. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Pour obtenir un exemple complet d’utilisation de la caractéristique `timeseries` de jeux de données, consultez l’[exemple de notebook](https://aka.ms/azureml-tsd-notebook) ou la [documentation du SDK des jeux de données](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Si vous créez votre jeu de données à l’aide de Azure Machine Learning Studio, assurez-vous que le chemin d’accès à vos données contient des informations d’horodatage, incluez tous les sous-dossiers avec des données et définissez le format de partition. 

Dans l’exemple suivant, toutes les données sous le sous-dossier *NoaaIsdFlorida/2019* extraites, tandis que le format de partition spécifie l’année, le mois et le jour de l’horodatage. 

[![Format de partition](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Dans les paramètres **Schéma**, spécifiez la colonne Timestamp d’une colonne virtuelle ou réelle dans le jeu de données spécifié :

![Timestamp](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Paramètres d’analyse du jeu de données

Après avoir créé votre jeu de données avec les paramètres d’horodatage spécifiés, vous êtes prêt à configurer votre analyse du jeu de données.

Les différents paramètres d’analyse de jeu de données sont répartis en trois groupes : **Informations de base, paramètres d’analyse** et **paramètres de renvoi**.

### <a name="basic-info"></a>Informations de base

Cette table contient les paramètres de base utilisés pour l’analyse du jeu de données.

| Paramètre | Description | Conseils | Mutable | 
| ------- | ----------- | ---- | ------- | 
| Name | Nom de l’analyse du jeu de données | | Non |
| Jeu de données de référence | Jeu de données tabulaires qui sera utilisé comme ligne de base pour la comparaison du jeu de données cible au fil du temps. | Le jeu de données de référence doit avoir des caractéristiques communes avec le jeu de données cible. En règle générale, la ligne de base doit être définie sur le jeu de données de formation d’un modèle ou sur un segment du jeu de données cible. | Non |
| Jeu de données cible | Jeu de données tabulaires avec colonne timestamp qui sera analysé pour la dérive des données. | Le jeu de données cible doit avoir des caractéristiques en commun avec le jeu de données de base et il doit s’agir d’un jeu de données `timeseries` auquel de nouvelles données sont ajoutées. Les données d’historique peuvent être analysées dans le jeu de données cible ou de nouvelles données peuvent être surveillées. | Non | 
| Fréquence | Fréquence qui permettra de planifier le travail de pipeline et d’analyser les données d’historique si vous exécutez un renvoi. Les options disponibles sont quotidienne, hebdomadaire ou mensuelle. | Ajustez ce paramètre pour inclure une taille comparable de données à la ligne de base. | Non | 
| Fonctionnalités | Liste des caractéristiques qui seront analysées pour la dérive des données au fil du temps. | À définir sur la (ou les) caractéristique(s) dont vous souhaitez mesurer la dérive de concept. N’incluez pas les caractéristiques qui dérivent naturellement au fil du le temps (mois, année, index, etc.). Vous pouvez effectuer un renvoi et démarrer une analyse de dérive de données existant après avoir ajusté la liste des fonctionnalités. | Oui | 
| Cible de calcul | Cible de calcul Azure Machine Learning pour exécuter les travaux de l’analyse de jeu de données. | | Oui | 

### <a name="monitor-settings"></a>Paramètres d’analyse

Ces paramètres sont associés au pipeline de moniteur de jeu de données planifié qui va être créé. 

| Paramètre | Description | Conseils | Mutable | 
| ------- | ----------- | ---- | ------- |
| Activer | Activer ou désactiver la planification du pipeline de l’analyse de jeu de données | Désactivez la planification pour analyser les données d’historique avec le paramètre de renvoi. Elle peut être activée après la création de l’analyse de jeu de données. | Oui | 
| Latence | Durée (en heures) nécessaire pour que les données parviennent au jeu de données. Par exemple, s’il faut trois jours pour que les données parviennent dans la base de données SQL encapsulée par le jeu de données, définissez la latence sur 72. | N’est plus modifiable après la création de l’analyse de jeu de données | Non | 
| Adresses e-mail | Adresses de messagerie pour les alertes en fonction de la violation du seuil de pourcentage de dérive des données. | Les e-mails sont envoyés via Azure Monitor. | Oui | 
| Seuil | Seuil du pourcentage de dérive des données pour les alertes envoyées par e-mail. | Il est possible de définir d’autres alertes et événements sur de nombreuses autres métriques dans la ressource Application Insights associée de l’espace de travail. | Oui | 

### <a name="backfill-settings"></a>Paramètres de renvoi

Ces paramètres servent à exécuter un renvoi sur des données passées pour les métriques de dérive de données.

| Paramètre | Description | Conseils |
| ------- | ----------- | ---- |
| Date de début | Date de début du travail de renvoi. | | 
| Date de fin | Date de fin du travail de renvoi. | Cette date de fin ne peut pas dépasser 31*unités de fréquence temporelle à partir de la date de début. Sur une analyse de jeu de données existante, les métriques peuvent être renvoyées pour analyser les données historiques ou remplacer les métriques par des paramètres mis à jour. |

## <a name="create-dataset-monitors"></a>Créer des analyses de jeu de données 

Créez des analyses de jeu de données pour détecter la dérive des données d’un nouveau jeu de données et en informer les utilisateurs à l’aide d’Azure Machine Learning Studio ou du kit de développement logiciel (SDK) Python. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Pour configurer des alertes sur votre analyse de jeu de données, l’espace de travail qui contient le jeu de données pour lequel vous souhaitez créer une analyse doit disposer des fonctionnalités Enterprise Edition. 

Après avoir vérifié que l’espace de travail est fonctionnel, accédez à la page d’accueil de Studio, puis sélectionnez l’onglet Jeux de données sur la gauche. Sélectionnez Analyses de jeu de données.

![Liste des analyses](./media/how-to-monitor-datasets/monitor-list.png)

Cliquez sur le bouton **+Créer une analyse**, puis passez à la prochaine étape de l’assistant en cliquant sur **Suivant**.

![Assistant](./media/how-to-monitor-datasets/wizard.png)

L’analyse du jeu de données ainsi définie s’affiche dans la liste. Sélectionnez-la pour accéder à sa page d’informations détaillées.

### <a name="from-python-sdk"></a>À partir du kit de développement logiciel Python

La [documentation de référence du SDK python sur la dérive des données](/python/api/azureml-datadrift/azureml.datadrift) fournir des informations complètes sur le sujet. 

Voici un exemple de création d’un moniteur analyse de jeu de données à l’aide du SDK Python

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Pour obtenir un exemple complet de configuration d’un jeu de données et d’un détecteur de dérive de données `timeseries`, consultez notre [exemple de notebook](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Comprendre les résultats de la dérive des données

L’analyse de données produit deux groupes de résultats : Vue d’ensemble de la dérive et détails de la caractéristique. L’animation suivante montre les graphiques d’analyse de dérive disponibles selon la caractéristique et la métrique sélectionnées. 

![Vidéo de démonstration](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Vue d’ensemble de la dérive

La section **Vue d’ensemble de la dérive** comporte des informations générales sur l’ampleur de la dérive de données et sur les caractéristiques qui doivent faire l’objet d’une analyse plus poussée. 

| Métrique | Description | Conseils | 
| ------ | ----------- | ---- | 
| Ampleur de la dérive de données | Indiqué sous la forme d’un pourcentage entre la ligne de base et le jeu de données cible au fil du temps. Comprise entre 0 et 100, où 0 indique des jeux de données identiques et 100 indique que la fonctionnalité de dérive des données Azure Machine Learning peut distinguer complètement les deux jeux de données. | Le bruit est pris en compte dans le calcul du pourcentage de précision en raison des techniques Machine Learning utilisées pour cette amplitude. | 
| Contribution de dérive par caractéristique | Contribution de chaque caractéristique du jeu de données cible à l’ampleur de la dérive mesurée. |  En raison de l’évolution de la covariable, la distribution sous-jacente d’une fonctionnalité n’a pas nécessairement besoin d’être modifiée pour avoir une importance relativement élevée. | 

L’illustration suivante représente un exemple de graphiques des résultats **Vue d’ensemble de la dérive** dans Azure Machine Learning Studio, consécutifs à un renvoi de données météorologiques [NOAA Integrated Surface Data](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Les données ont été échantillonnées pour `stationName contains 'FLORIDA'`, janvier 2019 étant utilisé comme jeu de données de référence et toutes les données de 2019 comme cible.
 
![Vue d’ensemble de la dérive](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Détails de la caractéristique

La section **Détails de la caractéristique** contient un aperçu de la modification de la distribution de l'élément sélectionné, ainsi que d'autres statistiques, au fil du temps. 

Le jeu de données cible est également profilé au fil du temps. La distance statistique entre la distribution de ligne de base de chaque caractéristique est comparée au jeu de données cible au fil du temps, qui est conceptuellement similaire à l’amplitude de la dérive des données, à l’exception du fait qu’il s’agit de la distance statistique d’une caractéristique individuelle. Les valeurs minimum, maximum et moyenne sont également disponibles. 

Dans Azure Machine Learning Studio, si vous cliquez sur un point de données dans le graphique, la distribution de la caractéristique affichée s’ajuste en conséquence. Par défaut, il affiche la distribution du jeu de données de référence et la distribution de la même caractéristique issue de la dernière exécution. 

Ces métriques peuvent également être récupérées dans le SDK Python par le biais de la méthode `get_metrics()` sur un objet `DataDriftDetector`. 

#### <a name="numeric-features"></a>Caractéristiques numériques 

Les caractéristiques numériques sont profilées à chaque exécution de l’analyse de jeu de données. Les éléments suivants sont exposés dans Azure Machine Learning Studio. La densité de probabilité est indiquée pour la distribution.

| Métrique | Description |  
| ------ | ----------- |  
| Distance Wasserstein | Quantité minimale de travail nécessaire à la transformation de la distribution de référence en distribution cible. |
| Valeur moyenne | Valeur moyenne de la caractéristique. |
| Valeur minimale | Valeur minimale de la caractéristique. |
| Valeur maximale | Valeur maximale de la caractéristique. |

![Détails de la caractéristique numérique](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Caractéristiques par catégorie 

Les caractéristiques numériques sont profilées à chaque exécution de l’analyse de jeu de données. Les éléments suivants sont exposés dans Azure Machine Learning Studio. Un histogramme représente la distribution.

| Métrique | Description |  
| ------ | ----------- |  
| Distance euclidienne | Distance géométrique entre la ligne de base et les distributions cibles. |
| Valeurs uniques | Nombre de valeurs uniques (cardinalité) de la caractéristique. |


![Détails de la caractéristique par catégorie](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Métriques, alertes et événements

Les métriques peuvent être interrogées dans la ressource [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associée à votre espace de travail Machine Learning, ce qui donne accès à toutes les fonctionnalités d’Application Insights, notamment la configuration de règles d’alerte personnalisées et de groupes d’actions pour déclencher une action, comme une notification E-mail/SMS/Push/Voix ou une fonction Azure. Pour plus d’informations, reportez-vous à la documentation complète d’Application Insights. 

Pour commencer, accédez au portail Azure et sélectionnez la page **Vue d’ensemble** de votre espace de travail.  La ressource Application Insights associée se trouve à l’extrême droite :

[![Vue d’ensemble du portail Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Sélectionnez Journaux (analytique) sous Supervision dans le volet gauche :

![Vue d’ensemble d’Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Les métriques du superviseur de jeu de données sont stockées en tant que `customMetrics`. Vous pouvez écrire et exécuter une requête après avoir configuré un moniteur de jeu de données pour les visualiser :

[![Requête Log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Après avoir identifié les métriques pour configurer des règles d’alerte, créez une règle d’alerte :

![Nouvelle règle d’alerte](./media/how-to-monitor-datasets/alert-rule.png)

Vous pouvez utiliser un groupe d’actions existant ou en créer un pour définir l’action à entreprendre quand les conditions définies sont remplies :

![Nouveau groupe d’actions](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Dépannage

Limitations et problèmes connus :

* La plage de temps des travaux de renvoi est limitée à 31 intervalles du paramètre de fréquence de l’analyse. 
* Limitation à 200 caractéristiques sauf si aucune liste de caractéristiques n’est spécifiée (toutes les caractéristiques sont utilisées).
* La taille de calcul doit être suffisamment grande pour gérer les données. 
* Vérifiez que votre jeu de données contient des données comprises entre la date de début et la date de fin pour l’exécution d’un superviseur donné.
* Les analyses de jeu de données ne fonctionnent que sur les jeux de données qui contiennent 50 lignes ou plus. 

Dans le jeu de données, les colonnes, ou caractéristiques, sont classées comme catégories ou comme nombres, en fonction des conditions du tableau suivant. Si la caractéristique ne respecte pas ces conditions (par exemple, une colonne de type String avec plus de 100 valeurs uniques), elle est supprimée de notre algorithme de dérive des données, mais elle est toujours profilée. 

| Type de caractéristique | Type de données | Condition | Limites | 
| ------------ | --------- | --------- | ----------- |
| Par catégorie | string, bool, int, float | Le nombre de valeurs uniques dans la caractéristique est inférieur à 100 et inférieur à 5 % du nombre de lignes. | La valeur null est traitée comme sa propre catégorie. | 
| Numérique | int, float | Les valeurs de la caractéristique sont d’un type de données numérique et ne répondent pas aux conditions d’une caractéristique par catégorie. | Caractéristique supprimée si moins de 15 % des valeurs ont la valeur null. | 

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer une analyse de jeu de données, accédez à [Azure Machine Learning studio](https://ml.azure.com) ou au [notebook Python](https://aka.ms/datadrift-notebook).
* Découvrez comment configurer la dérive de données sur les [modèles déployés sur Azure Kubernetes Service](how-to-monitor-data-drift.md).
* Configurez des superviseurs de dérive de jeu de données avec [Event Grid](how-to-use-event-grid.md). 
