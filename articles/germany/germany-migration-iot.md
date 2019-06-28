---
title: Migrer des ressources Azure IoT depuis Azure Germany vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources Azure IoT depuis Azure Germany vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 8/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: ccb837001a4ef69ac6145e2f93bb9eb4766120f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033661"
---
# <a name="migrate-iot-resources-to-global-azure"></a>Migrer des ressources Azure IoT vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources Azure IoT depuis Azure Germany vers Azure global.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Vous pouvez utiliser l’outil de migration de données Azure Cosmos DB pour migrer vos données vers Azure Cosmos DB. Il s’agit d’une solution open source qui importe des données dans Azure Cosmos DB à partir de différentes sources.

L’outil de migration de données Azure Cosmos DB est disponible sous la forme d’un outil d’interface graphique ou d’un outil de ligne de commande. Le code source est disponible dans le référentiel GitHub [de l’outil de migration de données Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool). Une [version compilée de l’outil](https://www.microsoft.com/download/details.aspx?id=46436) est disponible dans le Centre de téléchargement Microsoft.

Pour migrer des ressources Azure Cosmos DB, nous vous recommandons de procéder comme suit :

1. Passez en revue les exigences relatives à la durée de fonctionnement et aux configurations de compte, afin de déterminer le meilleur plan d’action.
1. Clonez les configurations de compte à partir d’Azure Germany vers la nouvelle région, en exécutant l’outil de migration de données.
1. Si vous pouvez utiliser une fenêtre de maintenance, copiez les données de la source sur la destination, via l’outil de migration de données.
1. Sinon, copiez les données de la source sur la destination via l’outil, puis procédez comme suit :
   1. Utilisez une approche basée sur la configuration pour modifier les lectures/écritures dans une application.
   1. Effectuez une synchronisation de type synchronisation initiale.
   1. Configurez une synchronisation incrémentielle et rattrapez le flux de modification.
   1. Faites pointer les lectures vers le nouveau compte et validez l’application.
   1. Arrêtez les écritures sur l’ancien compte, vérifiez que vous avez rattrapé le flux de modification, puis faites pointer les écritures vers le nouveau compte.
   1. Arrêtez l’outil et supprimez l’ancien compte.
1. Exécutez l’outil pour valider la cohérence des données entre l’ancien et le nouveau compte.

Pour plus d'informations :

- Lisez une [présentation d’Azure Cosmos DB](../cosmos-db/introduction.md).
- Apprenez comment [importer des données dans Azure Cosmos DB](../cosmos-db/import-data.md).

## <a name="functions"></a>Fonctions

Actuellement, il n’est pas possible de migrer des ressources Azure Functions depuis Azure Germany vers Azure global. Nous vous recommandons d’exporter un modèle Resource Manager, de modifier l’emplacement et de le redéployer vers la région cible.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Azure Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant le [didacticiel sur Azure Functions](https://docs.microsoft.com/azure/azure-functions/#step-by-step-tutorials).
- Apprenez comment [exporter des modèles Microsoft Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) ou lisez la présentation de [Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Consultez la [vue d’ensemble d’Azure Functions](../azure-functions/functions-overview.md)
- Lisez la [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="notification-hubs"></a>Notification Hubs

Pour migrer les paramètres d’une instance Microsoft Azure Notification Hubs vers une autre instance, exportez, puis importez tous les jetons d’inscription et balises :

1. [Exportez les inscriptions aux hubs de notification existants](/previous-versions/azure/azure-services/dn790624(v=azure.100)) vers un conteneur Azure Storage Blob.
1. Créez un hub de notification dans l’environnement cible.
1. [Importez vos jetons d’inscription](/previous-versions/azure/azure-services/dn790624(v=azure.100)) depuis le Stockage blob sur votre nouveau hub de notification.

Pour plus d'informations :

- Actualisez vos connaissances en effectuant le [didacticiel sur Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/#step-by-step-tutorials).
- Consultez la [présentation de Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="iot-hub"></a>IoT Hub

Bien que vous puissiez migrer des instances Azure IoT Hub depuis Azure Germany vers Azure global, la migration n’est pas transparente.

> [!NOTE]
> Elle peut entraîner des temps d’arrêt et des pertes de données dans votre application Azure IoT. Tous les messages de télémétrie, commandes C2D et informations liés aux travaux (planifications et l’historique) ne sont pas migrés. Vous devez reconfigurer vos appareils et applications back-end pour commencer à utiliser les nouvelles chaînes de connexion.

### <a name="step-1-re-create-the-iot-hub"></a>Étape 1 : Recréer le hub IoT

Azure IoT Hub ne prend pas en charge le clonage en mode natif. Toutefois, vous pouvez utiliser la fonctionnalité Azure Resource Manager pour [exporter un groupe de ressources en tant que modèle](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates) afin d’exporter les métadonnées de votre hub IoT. Les itinéraires configurés et autres paramètres de hubs IoT sont inclus dans les métadonnées exportées. Ensuite, redéployez le modèle dans Azure global. Il peut s’avérer plus facile à recréer le hub IoT dans le Portail Microsoft Azure en examinant les détails dans le JSON exporté.

### <a name="step-2-migrate-device-identities"></a>Étape 2 : Migrer des identités d’appareil

Pour migrer des identités d’appareil, procédez comme suit :

1. Dans le locataire source sur Azure Germany, utilisez l’API [ExportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) de Resource Manager pour exporter les identités d’appareil, les jumeaux d’appareils et les jumeaux de module (y compris les clés) vers un conteneur de stockage. Vous pouvez utiliser un conteneur de stockage dans Azure Germany ou Azure global. Vérifiez que l’URI de signature d’accès partagé généré dispose de permissions suffisantes. 
1. Exécutez l’API [ImportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) de Resource Manager pour importer l’ensemble des identités d’appareil du conteneur de stockage vers le hub IoT dans Azure global.
1. Reconfigurez vos appareils et services back-end pour commencer à utiliser les nouvelles chaînes de connexion. Le nom d’hôte passe de **\*.azure-devices.de** à **\*.azure-devices.com**.  

> [!NOTE]
> L’autorité de certification racine est différente dans Azure Germany et Azure global. Tenez compte de cette donnée lorsque vous reconfigurez les appareils et applications back-end qui interagissent avec l’instance IoT Hub.

Pour plus d'informations :

- Apprenez comment [exporter des identités IoT Hub en bloc](../iot-hub/iot-hub-bulk-identity-mgmt.md#export-devices).
- Apprenez comment [importer des identités IoT Hub en bloc](../iot-hub/iot-hub-bulk-identity-mgmt.md#import-devices).
- Consultez la [présentation d’Azure IoT Hub](../iot-hub/about-iot-hub.md).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
