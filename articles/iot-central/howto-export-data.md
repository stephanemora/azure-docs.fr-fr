---
title: Exporter vos données à partir d’Azure IoT Central | Microsoft Docs
description: Comment exporter des données depuis votre application Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 98e19cccff1c02f653022f2061854697ee11d1a2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759897"
---
# <a name="export-your-data-from-azure-iot-central"></a>Exporter vos données à partir d’Azure IoT Central

*Cette rubrique s’applique aux administrateurs.*

Cet article décrit la façon d’utiliser la fonctionnalité d’exportation continue de données d’Azure IoT Central afin d’exporter vos données vers vos instances **Stockage Blob Azure**, **Azure Event Hubs** et **Azure Service Bus**. Vous pouvez exporter des **mesures**, des **appareils** et des **modèles d’appareil** vers votre propre destination pour obtenir des analyses sur le chemin à chaud et à froid. Vous pouvez exporter des données vers le stockage d’objets Blob pour exécuter une analyse de tendances à long terme dans Microsoft Power BI, ou exporter des données vers Event Hubs et Service Bus pour transformer et enrichir vos données en temps quasi réel avec Azure Logic Apps ou Azure Functions.

> [!Note]
> Quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.

## <a name="prerequisites"></a>Conditions préalables

Vous devez être administrateur dans votre application IoT Central

## <a name="export-to-blob-storage"></a>Exporter vers le Stockage Blob

Les données de mesures, d’appareils et de modèles d’appareil sont exportées vers votre compte de stockage une fois par minute. Chaque fichier contient le lot de modifications apportées depuis la dernière exportation de fichier. Les données sont exportées au format [Apache AVRO](https://avro.apache.org/docs/current/index.html).

En savoir plus sur [l’exportation vers le Stockage Blob](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Exporter vers Event Hubs et Service Bus

Les données de mesures, d’appareils et de modèles d’appareil sont exportées vers votre Event Hub ou la file d’attente ou la rubrique Service Bus. Les données de mesures exportées arrivent en temps quasi réel et contiennent l’intégralité du message que vos appareils ont envoyé à IoT Central, pas uniquement les valeurs des mesures elles-mêmes. Les données d’appareils exportées arrivent par lots une fois par minute et contiennent les modifications apportées aux propriétés et aux paramètres de tous les appareils, et les modèles d’appareil exportés contiennent les modifications apportées à tous les modèles d’appareil.

En savoir plus sur [l’exportation vers Event Hubs et Service Bus](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Définir la destination de l’exportation

En l’absence de données de Stockage/Event Hubs/Service Bus existantes à exporter, suivez ces étapes :

### <a name="create-storage-account"></a>Créer un compte de stockage

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Pour en savoir plus, consultez les [documents sur le Stockage Azure](https://aka.ms/blobdocscreatestorageaccount).

2. Choisissez le type de compte **Usage général** ou **Stockage Blob**.

3. Choisissez un abonnement.

    > [!Note]
    > Vous pouvez maintenant exporter des données vers des abonnements autres, **différents** de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connectez à l’aide d’une chaîne de connexion dans ce cas.

4. Créez un conteneur dans votre compte de stockage. Accédez à votre compte de stockage. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.

### <a name="create-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

1. Créez un [espace de noms Event Hubs dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Pour en savoir plus, consultez les [documents sur Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

2. Choisissez un abonnement.

    > [!Note]
    > Vous pouvez maintenant exporter des données vers des abonnements autres, **différents** de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connectez à l’aide d’une chaîne de connexion dans ce cas.

3. Créez un Event Hub dans votre espace de noms Event Hubs. Accédez à votre espace de noms, puis sélectionnez **+ Event Hub**, en haut, pour créer une instance Event Hub.

### <a name="create-service-bus-namespace"></a>Créer un espace de noms Service Bus

1. Créez un [espace de noms Service Bus dans le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Pour en savoir plus, consultez les [documents sur Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).

2. Choisissez un abonnement.

    > [!Note]
    > Vous pouvez maintenant exporter des données vers des abonnements autres, **différents** de celui de votre application de paiement à l’utilisation IoT Central. Vous vous connectez à l’aide d’une chaîne de connexion dans ce cas.

3. Accédez à votre espace de noms Service Bus, puis sélectionnez **+ File d’attente** ou **+ Rubrique**, en haut, pour créer une file d’attente ou une rubrique de destination d’exportation.

## <a name="set-up-continuous-data-export"></a>Configurer l’exportation de données en continu

Maintenant que vous avez une destination de Stockage/Event Hubs/Service Bus pour exporter les données, suivez ces étapes pour configurer l’exportation continue des données.

1. Connectez-vous à votre application IoT Central.

2. Dans le menu de gauche, sélectionnez **exportation continue de données**.

    > [!Note]
    > Si vous ne voyez pas l’option d’exportation de données continue dans le menu de gauche, vous n’êtes pas administrateur de votre application. Contactez un administrateur pour configurer l’exportation de données.

    ![Créer nouveau Hub d’événements](media/howto-export-data/export_menu.png)

3. Sélectionnez le **+ nouveau** bouton dans le coin supérieur droit. Choisissez **Stockage Blob Azure**, **Azure Event Hubs** ou **Azure Service Bus** comme destination de l’exportation.

    > [!NOTE]
    > Le nombre maximal d’exportations par application est de cinq.

    ![Créer une exportation de données continue](media/howto-export-data/export_new.png)

4. Dans la zone de liste déroulante, sélectionnez votre **compte de stockage/espace de noms Event Hubs/espace de noms Service Bus**. Vous pouvez également choisir la dernière option de la liste : **Entrer une chaîne de connexion**. 

    > [!NOTE]
    > Vous voyez uniquement les espaces de noms de comptes de stockage/concentrateurs espaces de noms/Service Bus dans le **même abonnement que votre application IoT Central**. Si vous souhaitez exporter les données vers une destination en dehors de cet abonnement, choisissez **Entrer une chaîne de connexion** et reportez-vous à l’étape 5.

    > [!NOTE]
    > Pour les applications d’évaluation de 7 jours, le seul moyen de configurer l’exportation des données est d’utiliser une chaîne de connexion. En effet, ces applications ne sont associées à aucun abonnement Azure.

    ![Créer un Event Hub d’exportation de données continue](media/howto-export-data/export_create.png)

5. (Facultatif) Si vous avez choisi **Entrer une chaîne de connexion**, une nouvelle zone vous permettant de coller votre chaîne de connexion s’affiche. Pour obtenir votre chaîne de connexion :
    - Pour votre compte de stockage, accédez à ce compte dans le Portail Azure.
        - Sous **paramètres**, sélectionnez **clés d’accès**
        - Copiez soit la chaîne de connexion key1 soit la chaîne de connexion key2
    - Pour Event Hubs ou Service Bus, accédez à l’espace de noms correspondant dans le Portail Azure.
        - Sous **paramètres**, sélectionnez **stratégies d’accès partagé**
        - Choisissez la valeur par défaut **RootManageSharedAccessKey** ou créez-en une
        - Copiez la chaîne de connexion primaire ou secondaire

6. Choisissez un conteneur/un Event Hub/une file d’attente ou une rubrique dans la zone de liste déroulante.

7. Sous **Données à exporter**, spécifiez chaque type de données à exporter en définissant le type avec la valeur **Activé**.

8. Pour activer l’exportation des données continue, assurez-vous que l’option **Exportation de données** est définie sur **Activé**. Sélectionnez **Enregistrer**.

    ![Configurer l’exportation de données continue](media/howto-export-data/export_list.png)

9. Après quelques minutes, vos données apparaissent dans votre destination choisie.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment exporter vos données, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Exporter des données vers le Stockage Blob Azure](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Exporter des données vers Azure Event Hubs et Azure Service Bus](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Guide pratique pour visualiser vos données dans Power BI](howto-connect-powerbi.md)
