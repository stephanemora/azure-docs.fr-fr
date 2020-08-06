---
title: Intégration d’Azure Stream Analytics avec Azure Machine Learning
description: Cet article explique comment intégrer une tâche Azure Stream Analytics avec des modèles Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: e2277e2088a8cb386d6f19799b235d96e08959b0
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543433"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Intégration d’Azure Stream Analytics avec Azure Machine Learning (préversion)

Vous pouvez implémenter des modèles Machine Learning en tant que fonctions définies par l’utilisateur dans vos tâches Azure Stream Analytics pour effectuer le scoring et les prédictions en temps réel sur vos données d’entrée de streaming. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) vous permet d’utiliser n’importe quel outil open source populaire, comme Tensorflow, scikit-learn ou PyTorch pour préparer, entraîner et déployer des modèles.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes suivantes avant d’ajouter un modèle de Machine Learning en tant que fonction à votre tâche Stream Analytics :

1. Utilisez Azure Machine Learning pour [déployer votre modèle en tant que service web](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Votre script de scoring doit avoir des [exemples d’entrée et de sortie](../machine-learning/how-to-deploy-and-where.md), qui seront utilisés par Azure Machine Learning pour générer une spécification de schéma. Stream Analytics utilise le schéma pour comprendre la signature de fonction de votre service web. Vous pouvez utiliser cet [exemple de définition de Swagger](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) comme référence pour vous assurer de l’avoir correctement configuré.

3. Assurez-vous que votre service web accepte et retourne les données sérialisées JSON.

4. Déployez votre modèle sur [Azure Kubernetes Service](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) pour les déploiements de production à grande échelle. Si le service web n’est pas en mesure de gérer le nombre de requêtes provenant de votre tâche, les performances de votre tâche Stream Analytics sont dégradées, ce qui a une incidence sur la latence. Les modèles déployés sur Azure Container Instances sont pris en charge uniquement lorsque vous utilisez le portail Azure. Actuellement, les modèles générés à l’aide du [concepteur Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer) ne sont pas pris en charge dans Stream Analytics.

## <a name="add-a-machine-learning-model-to-your-job"></a>Ajouter un modèle Machine Learning à votre tâche

Vous pouvez ajouter des fonctions Azure Machine Learning à votre tâche Stream Analytics directement à partir du portail Azure.

1. Accédez à votre tâche Stream Analytics dans le portail Azure, puis sélectionnez **Fonctions** sous **Topologie de la tâche**. Ensuite, sélectionnez **Service Azure ML** dans le menu déroulant **+ Ajouter**.

   ![Ajouter une FDU Azure ML](./media/machine-learning-udf/add-azureml-udf.png)

2. Renseignez le formulaire **Fonction Azure Machine Learning Service** avec les valeurs de propriété suivantes :

   ![Configurer une FDU Azure ML](./media/machine-learning-udf/configure-azureml-udf.png)

Le tableau suivant décrit chaque propriété des fonctions d’Azure ML Service dans Stream Analytics.

|Propriété|Description|
|--------|-----------|
|Alias de fonction|Entrez un nom pour appeler la fonction dans votre requête.|
|Abonnement|Votre abonnement Azure.|
|Espace de travail Azure ML|L’espace de travail Azure Machine Learning que vous avez utilisé pour déployer votre modèle en tant que service web.|
|Déploiements|Service web hébergeant votre modèle.|
|Signature de fonction|La signature de votre service web déduite de la spécification de schéma de l’API. Si le chargement de votre signature échoue, vérifiez que vous avez fourni les exemples d’entrée et de sortie dans votre script de scoring pour générer automatiquement le schéma.|
|Nombre de requêtes parallèles par partition|Il s’agit d’une configuration avancée pour optimiser le débit à grande échelle. Ce nombre représente les requêtes simultanées envoyées à partir de chaque partition de votre tâche au service web. Les tâches avec six unités de diffusion en continu (SU) et moins ont une partition. Les tâches avec 12 unités de sauvegarde ont deux partitions, celles avec 18 unités de diffusion en continu ont trois partitions, et ainsi de suite.<br><br> Par exemple, si votre tâche a deux partitions et que vous définissez ce paramètre sur quatre, il y aura huit requêtes simultanées de votre tâche à votre service web. À ce stade de la préversion publique, cette valeur est définie sur 20 par défaut et ne peut pas être mise à jour.|
|Nombre maximal de lots|Il s’agit d’une configuration avancée pour optimiser le débit à grande échelle. Ce nombre représente le nombre maximal d’événements regroupés en une seule requête envoyée à votre service web.|

## <a name="supported-input-parameters"></a>Paramètres d’entrée pris en charge

Lorsque votre requête Stream Analytics appelle une FDU Azure Machine Learning, la tâche crée une demande sérialisée JSON auprès du service web. La requête est basée sur un schéma spécifique au modèle. Vous devez fournir un exemple d’entrée et de sortie dans votre script de scoring pour [générer automatiquement un schéma](../machine-learning/how-to-deploy-and-where.md). Le schéma permet à Stream Analytics de construire la requête JSON sérialisée pour l’un des types de données pris en charge, comme numpy, pandas et PySpark. Plusieurs événements d’entrée peuvent être regroupés par lot dans une seule requête.

La requête Stream Analytics suivante est un exemple d’appel d’une FDU Azure Machine Learning :

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics prend en charge uniquement le passage d’un paramètre pour les fonctions Azure Machine Learning. Vous devrez peut-être préparer vos données avant de les transmettre en tant qu’entrée à la FDU Machine Learning.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passer plusieurs paramètres d’entrée à la FDU

Les exemples les plus courants d’entrée pour les modèles Machine Learning sont les tableaux numpy et les DataFrames. Vous pouvez créer un tableau à l’aide d’une FDU JavaScript et créer un DataFrame sérialisé en JSON à l’aide de la clause `WITH`.

### <a name="create-an-input-array"></a>Créer un tableau d’entrée

Vous pouvez créer une FDU JavaScript qui accepte un nombre *N* d’entrées et crée un tableau qui peut être utilisé comme entrée pour votre FDU Azure Machine Learning.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Une fois que vous avez ajouté la FDU JavaScript à votre tâche, vous pouvez appeler votre FDU Azure Machine Learning à l’aide de la requête suivante :

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

L’extrait JSON ci-dessous est un exemple de requête :

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Créer un tableau Pandas ou un DataFrame PySpark

Vous pouvez utiliser la clause `WITH` pour créer un DataFrame sérialisé JSON qui peut être transmis en tant qu’entrée à votre FDU Azure Machine Learning, comme indiqué ci-dessous.

La requête suivante crée un DataFrame en sélectionnant les champs nécessaires et utilise le DataFrame comme entrée pour la FDU Azure Machine Learning.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

Le code JSON suivant est un exemple de requête de la requête précédente :

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimiser les performances pour les fonctions définies par l’utilisateur (FDU) Azure Machine Learning

Lorsque vous déployez votre modèle sur le service Azure Kubernetes, vous pouvez [profiler votre modèle pour déterminer l’utilisation des ressources](../machine-learning/how-to-deploy-profile-model.md). Vous pouvez également [activer Application Insights pour vos déploiements](../machine-learning/how-to-enable-app-insights.md) pour comprendre les taux de demande, les temps de réponse et les taux d’échec.

Si vous avez un scénario avec un débit d’événements élevé, vous devrez peut-être modifier les paramètres suivants dans Stream Analytics pour obtenir des performances optimales avec des latences de bout en bout faibles :

1. Nombre maximal de lots.
2. Nombre de demandes parallèles par partition.

### <a name="determine-the-right-batch-size"></a>Déterminer la taille de lot appropriée

Une fois que vous avez déployé votre service web, vous envoyez l’exemple de requête avec des tailles de lot variables, en commençant à 50 et en incrémentant ce nombre par centaines. Par exemple, 200, 500, 1000, 2000 et ainsi de suite. Vous remarquerez qu’après une certaine taille de lot, la latence de la réponse augmente. Le nombre de lots maximal pour votre tâche est le point après lequel la latence de la réponse augmente.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Déterminer le nombre de requêtes parallèles par partition

Pour une mise à l’échelle optimale, votre tâche Stream Analytics doit être en mesure d’envoyer plusieurs requêtes parallèles à votre service web et d’obtenir une réponse dans un délai de quelques millisecondes. La latence de la réponse du service web peut avoir un impact direct sur la latence et les performances de votre tâche Stream Analytics. Si l’appel de votre tâche au service web prend beaucoup de temps, vous verrez probablement une augmentation du délai en filigrane et vous pourrez également constater une augmentation du nombre d’événements d’entrée en retard.

Pour éviter une telle latence, assurez-vous que votre cluster Azure Kubernetes Service (AKS) a été configuré avec [le bon nombre de nœuds et de réplicas](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Il est essentiel que votre service web soit hautement disponible et renvoie des réponses réussies. Si votre tâche reçoit une réponse de service non disponible (503) à partir de votre service web, il réessaie continuellement avec une interruption exponentielle. Toute réponse autre que succès (200) ou service non disponible (503) entraîne l’échec de votre tâche.

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning Studio (classique)](stream-analytics-scale-with-machine-learning-functions.md)

