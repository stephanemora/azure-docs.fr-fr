---
title: 'Démarrage rapide : Transformer des données à l’aide d’une définition de travail Apache Spark'
description: Ce tutoriel fournit des instructions détaillées concernant l’utilisation d’Azure Synapse Analytics pour transformer des données à l’aide d’une définition de travail Apache Spark.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2021
ms.openlocfilehash: f7a44ae2033d75c81f80bc9a37e17ed8fe2786b2
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110501196"
---
# <a name="quickstart-transform-data-using-apache-spark-job-definition"></a>Démarrage rapide : Transformer des données à l’aide d’une définition de travail Apache Spark

Dans ce guide de démarrage rapide, vous vous servez d’Azure Synapse Analytics pour créer un pipeline au moyen d’une définition de travail Apache Spark.

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Espace de travail Azure Synapse** : créez un espace de travail Synapse à l’aide du portail Azure en suivant les instructions fournies dans [Démarrage rapide : Créer un espace de travail Synapse](quickstart-create-workspace.md).
* **Définition de travail Apache Spark** : créez une définition de travail Apache Spark dans l’espace de travail Synapse en suivant les instructions du [Tutoriel : Créer une définition de travail Apache Spark dans Synapse Studio](spark/apache-spark-job-definitions.md).


### <a name="navigate-to-the-synapse-studio"></a>Accéder à Synapse Studio

Après avoir créé votre espace de travail Azure Synapse, vous pouvez ouvrir Synapse Studio de deux manières :

* Ouvrez votre espace de travail Synapse dans le [Portail Azure](https://ms.portal.azure.com/#home). Sélectionnez **Ouvrir** sur la carte Ouvrir Synapse Studio dans la section Démarrage.
* Ouvrez [Azure Synapse Analytics](https://web.azuresynapse.net/) et connectez-vous à votre espace de travail.

Dans ce guide de démarrage rapide, nous utilisons l’espace de travail nommé « sampletest » comme exemple. Vous accéderez automatiquement à la page d’accueil de Synapse Studio.

![page d’accueil de Synapse Studio](media/quickstart-transform-data-using-spark-job-definition/synapse-studio-home.png)

## <a name="create-a-pipeline-with-an-apache-spark-job-definition"></a>Créer un pipeline avec une définition de travail Apache Spark

Un pipeline contient le flux logique pour l’exécution d’un ensemble d’activités. Dans cette section, vous allez créer un pipeline qui contient une activité de définition de travail Apache Spark.

1. Accédez à l’onglet **Intégrer**. Sélectionnez l’icône + à côté de l’en-tête Pipelines, puis sélectionnez Pipeline.

     ![Créer un pipeline](media/doc-common-process/new-pipeline.png)

2. Dans la page des paramètres **Propriétés** du pipeline, entrez **demo** en guise de **Nom**.

3. Sous *Synapse*, dans le volet *Activités*, faites glisser la **Définition de travail Spark** jusqu’au canevas du pipeline.

     ![faire glisser la définition de travail Spark](media/quickstart-transform-data-using-spark-job-definition/drag-spark-job-definition.png)


## <a name="set-apache-spark-job-definition-canvas"></a>Définir le canevas de définition de travail Apache Spark

Après avoir créé votre définition de travail Apache Spark, vous accédez automatiquement au canevas de définition de travail Spark.

### <a name="general-settings"></a>Paramètres généraux :

1. Sélectionnez le module de définition de travail Spark dans le canevas.

2. Dans l’onglet Général, entrez **sample** en guise de **Nom**.

3. (Facultatif) Vous pouvez également indiquer une description.

4. Délai : durée maximale pendant laquelle une activité peut s’exécuter. La valeur par défaut est de sept jours, ce qui correspond également à la durée maximale autorisée. Le format est J.HH:MM:SS.

5. Réessayer : nombre maximal de nouvelles tentatives.

6. Intervalle avant nouvelle tentative : nombre de secondes entre les nouvelles tentatives.

7. Sortie sécurisée : lorsque cette option est cochée, la sortie de l’activité n’est pas capturée dans la journalisation.

8. Entrée sécurisée : lorsque cette option est cochée, l’entrée de l’activité n’est pas capturée dans la journalisation.

     ![définition de travail Spark – général](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-general.png)

### <a name="settings-tab"></a>Onglet Paramètres 

Dans ce panneau, vous pouvez référencer la définition de travail Spark à exécuter.

* Développez la liste des définitions de travail Spark, vous pouvez choisir une définition de travail Apache Spark existante. Vous pouvez également créer une définition de travail Apache Spark en cliquant sur le bouton Nouveau pour référencer la définition de travail Spark à exécuter.

* Vous pouvez ajouter des arguments de ligne de commande en cliquant sur le bouton **Nouveau**. Notez que les arguments de ligne de commande définis par la définition de travail Spark seront alors remplacés. <br> *Exemple : `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result`* <br>

     ![paramètres du pipeline de définition de travail Spark](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-pipline-settings.png)

* Vous pouvez ajouter du contenu dynamique en cliquant sur le bouton **Ajouter du contenu dynamique** ou en appuyant sur la touche de raccourci <kbd>Alt</kbd>+<kbd>Maj</kbd>+<kbd>D</kbd>. Dans la page **Ajouter du contenu dynamique**, vous pouvez utiliser n’importe quelle combinaison d’expressions, de fonctions et de variables système à ajouter au contenu dynamique.

     ![ajouter du contenu dynamique](media/quickstart-transform-data-using-spark-job-definition/add-dynamic-content.png)

### <a name="user-properties-tab"></a>Onglet Propriétés de l’utilisateur

Dans ce panneau, vous pouvez ajouter des propriétés pour une activité de définition de travail Apache Spark.

![propriétés de l’utilisateur](media/quickstart-transform-data-using-spark-job-definition/user-properties.png)

## <a name="next-steps"></a>Étapes suivantes

Lisez les articles suivant pour en savoir plus sur la prise en charge d’Azure Synapse Analytics :

> [!div class="nextstepaction"]
> [Pipelines et activités](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [Présentation du flux de données de mappage](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [Langage d’expression du flux de données](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)