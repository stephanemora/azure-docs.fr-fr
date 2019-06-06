---
title: Étendre Azure IoT Central avec analytique personnalisée | Microsoft Docs
description: En tant que développeur de solutions, configurer une application IoT Central pour effectuer des visualisations et analytique personnalisée. Cette solution utilise Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743439"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Étendre Azure IoT Central avec analytique personnalisée

Ce guide vous montre, en tant que développeur de solutions, comment étendre votre application IoT Central avec analytique personnalisée et des visualisations. L’exemple utilise un [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) espace de travail pour analyser le flux de données de télémétrie IoT Central et pour générer des visualisations telles que [zone tracés](https://wikipedia.org/wiki/Box_plot).

Ce guide vous montre comment étendre IoT Central au-delà de ce qu’il peut déjà faire le [outils d’analytique intégrée](howto-create-analytics.md).

Dans ce guide, vous apprendrez comment :

* Données de télémétrie à partir d’une application IoT Central à l’aide de Stream *exportation continue de données*.
* Créer un environnement Azure Databricks pour analyser et tracer la télémétrie d’appareil.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

### <a name="iot-central-application"></a>Application IoT Central

Créer une application IoT Central à partir de la [Azure IoT Central - mes applications](https://aka.ms/iotcentral) page avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Plan de paiement | Pay-As-You-Go |
| Modèle d’application | Exemple Contoso |
| Nom de l’application | Acceptez la valeur par défaut ou choisissez votre propre nom |
| URL | Acceptez la valeur par défaut ou choisissez votre propre préfixe d’URL unique |
| Répertoire | Votre client Azure Active Directory |
| Abonnement Azure | Votre abonnement Azure |
| Région | USA Est |

Les exemples et les captures d’écran dans cet article utilisent le **est des États-Unis** région. Choisissez un emplacement proche de vous et vérifiez que vous créez toutes vos ressources dans la même région.

### <a name="resource-group"></a>Groupe de ressources

Utilisez le [portail Azure pour créer un groupe de ressources](https://portal.azure.com/#create/Microsoft.ResourceGroup) appelé **IoTCentralAnalysis** pour contenir les autres ressources que vous créez. Créez vos ressources Azure dans le même emplacement que votre application IoT Central.

### <a name="event-hubs-namespace"></a>Espace de noms Event Hubs

Utilisez le [portail Azure pour créer un espace de noms Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom    | Choisissez le nom de votre espace de noms |
| Niveau tarifaire | De base |
| Abonnement | Votre abonnement |
| Groupe de ressources | IoTCentralAnalysis |
| Lieu | USA Est |
| Unités de débit | 1 |

### <a name="azure-databricks-workspace"></a>Espace de travail Azure Databricks

Utilisez le [portail Azure pour créer un Service Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) avec les paramètres suivants :

| Paramètre | Valeur |
| ------- | ----- |
| Nom de l’espace de travail    | Choisissez le nom de votre espace de travail |
| Abonnement | Votre abonnement |
| Groupe de ressources | IoTCentralAnalysis |
| Lieu | USA Est |
| Niveau de tarification | standard |

Lorsque vous avez créé les ressources nécessaires, votre **IoTCentralAnalysis** groupe de ressources ressemble à la capture d’écran suivante :

![Groupe de ressources d’analyse IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Vous pouvez configurer une application IoT Central exporter en continu des données de télémétrie à un concentrateur d’événements. Dans cette section, vous créez un concentrateur d’événements pour recevoir des données de télémétrie à partir de votre application IoT Central. Le concentrateur d’événements fournit les données de télémétrie à votre travail Stream Analytique pour le traitement.

1. Dans le portail Azure, accédez à votre espace de noms Event Hubs et sélectionnez **+ Event Hub**.
1. Nommez votre concentrateur d’événements **centralexport**, puis sélectionnez **créer**.
1. Dans la liste des concentrateurs d’événements dans votre espace de noms, sélectionnez **centralexport**. Puis choisissez **stratégies d’accès partagé**.
1. Sélectionnez **+ ajouter**. Créer une stratégie nommée **écouter** avec la **écouter** de revendication.
1. Lorsque la stratégie est prête, sélectionnez-le dans la liste, puis copiez le **la clé primaire de la chaîne de connexion** valeur.
1. Prenez note de cette chaîne de connexion, utilisez-la ultérieurement lorsque vous configurez votre bloc-notes Databricks pour lire à partir du hub d’événements.

Votre espace de noms Event Hubs se présente comme la capture d’écran suivante :

![Espace de noms Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configurer l’exportation dans IoT Central

Accédez à la [application IoT Central](https://aka.ms/iotcentral) vous avez créé à partir du modèle de Contoso. Dans cette section, vous configurez l’application pour diffuser les données de télémétrie à partir de ses appareils simulés à votre concentrateur d’événements. Pour configurer l’exportation :

1. Accédez à la **exportation de données continue** page, sélectionnez **+ nouveau**, puis **Azure Event Hubs**.
1. Utilisez les paramètres suivants pour configurer l’exportation, puis sélectionnez **enregistrer**:

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom d’affichage | Exporter vers Event Hubs |
    | activé | Il en va |
    | Espace de noms Event Hubs | Nom de votre espace de noms Event Hubs |
    | Event Hub | centralexport |
    | Mesures | Il en va |
    | Appareils | Off |
    | Modèles d’appareil | Off |

![Configuration de l’exportation continue des données](media/howto-create-custom-analytics/cde-configuration.png)

Attendez que l’état de l’exportation est **en cours d’exécution** avant de continuer.

## <a name="configure-databricks-workspace"></a>Configurer l’espace de travail Databricks

Dans le portail Azure, accédez à votre service Azure Databricks et sélectionnez **lancer un espace de travail**. Un nouvel onglet s’ouvre dans votre navigateur et vous connecte à votre espace de travail.

### <a name="create-a-cluster"></a>Créer un cluster

Sur le **Azure Databricks** page, sous la liste des tâches courantes, sélectionnez **nouveau Cluster**.

Utilisez les informations dans le tableau suivant pour créer votre cluster :

| Paramètre | Valeur |
| ------- | ----- |
| Nom du cluster | centralanalysis |
| Mode de cluster | standard |
| Version du Runtime Databricks | 5.3 (Scala 2.11, Spark : 2.4.0) |
| Version de Python | 3 |
| Activer la mise à l’échelle automatique | Non |
| Terminer après quelques minutes d’inactivité | 30 |
| Type de travail | Standard_DS3_v2 |
| Workers | 1 |
| Type de pilote | Identique à worker |

Création d’un cluster peut prendre plusieurs minutes, attendez que la création du cluster à effectuer avant de continuer.

### <a name="install-libraries"></a>Installation des bibliothèques

Sur le **Clusters** page, attendez que l’état du cluster soit **en cours d’exécution**.

Les étapes suivantes vous montrent comment importer la bibliothèque a besoin de votre exemple dans le cluster :

1. Sur le **Clusters** page, attendez que l’état de la **centralanalysis** cluster interactif est **en cours d’exécution**.

1. Sélectionnez le cluster, puis choisissez le **bibliothèques** onglet.

1. Sur le **bibliothèques** , choisir **installer de nouveaux**.

1. Sur le **installer la bibliothèque** page, choisissez **Maven** en tant que source de la bibliothèque.

1. Dans le **coordonne** zone de texte, entrez la valeur suivante : `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Choisissez **installer** pour installer la bibliothèque sur le cluster.

1. L’état de la bibliothèque est désormais **installé**:

    ![Bibliothèque installée](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importer une instance Databricks notebook

Utilisez les étapes suivantes pour importer un bloc-notes Databricks qui contient le code Python pour analyser et visualiser vos données de télémétrie IoT Central :

1. Accédez à la **espace de travail** page dans votre environnement de Databricks. Sélectionnez la liste déroulante en regard du nom de votre compte, puis choisissez **importation**.

1. Choisissez cette option pour importer à partir d’une URL et entrez l’adresse suivante : [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Pour importer le bloc-notes, choisissez **importer**.

1. Sélectionnez le **espace de travail** pour afficher le bloc-notes importé :

    ![Bloc-notes importés](media/howto-create-custom-analytics/import-notebook.png)

1. Modifiez le code dans la première cellule de Python pour ajouter la chaîne de connexion Event Hubs que vous avez enregistré précédemment :

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Exécuter l’analyse

Pour exécuter l’analyse, vous devez attacher le bloc-notes au cluster :

1. Sélectionnez **Detached** , puis sélectionnez le **centralanalysis** cluster.
1. Si le cluster n’est pas en cours d’exécution, démarrez-le.
1. Pour démarrer le bloc-notes, sélectionnez le bouton d’exécution.

Vous pouvez rencontrer une erreur dans la dernière cellule. Dans ce cas, vérifiez que les cellules précédentes sont en cours d’exécution, patientez une minute pour des données à écrire dans le stockage, puis exécutez à nouveau la dernière cellule.

### <a name="view-smoothed-data"></a>Afficher les données lissées

Dans le bloc-notes, faites défiler la cellule 14 pour voir un tracé de l’humidité moyenne propagée par type de périphérique. Ce traçage en permanence mises à jour arrivent de diffusion en continu des données de télémétrie :

![Lissé tracé de télémétrie](media/howto-create-custom-analytics/telemetry-plot.png)

Vous pouvez redimensionner le graphique dans le bloc-notes.

### <a name="view-box-plots"></a>Vue diagramme à surfaces

Dans le bloc-notes, faites défiler jusqu'à la cellule 20 pour voir les [zone tracés](https://en.wikipedia.org/wiki/Box_plot). Les tracés de zone sont basés sur les données statiques et pour les mettre à jour, vous devez réexécuter la cellule :

![Diagramme à surfaces](media/howto-create-custom-analytics/box-plots.png)

Vous pouvez redimensionner les tracés dans le bloc-notes.

## <a name="tidy-up"></a>Nettoyer

Pour nettoyer après cette procédure et éviter les coûts inutiles, supprimez le **IoTCentralAnalysis** groupe de ressources dans le portail Azure.

Vous pouvez supprimer l’application IoT Central à partir de la **gestion** page au sein de l’application.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à effectuer les opérations suivantes :

* Données de télémétrie à partir d’une application IoT Central à l’aide de Stream *exportation continue de données*.
* Créer un environnement Azure Databricks pour analyser et tracer des données de télémétrie.

Maintenant que vous savez comment créer analytique personnalisée, l’étape suivante suggérée consiste à apprendre comment [visualiser et analyser vos données Azure IoT Central dans un tableau de bord Power BI](howto-connect-powerbi.md).
