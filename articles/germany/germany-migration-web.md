---
title: Migrer des ressources web Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources web Azure depuis Azure Allemagne vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 4e607d821d0a587b25c6cb6929ac6765d500b6fb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033655"
---
# <a name="migrate-web-resources-to-global-azure"></a>Migrer des ressources web Azure vers Azure global

Cet article contient des informations qui peuvent vous aider à migrer des ressources web Azure depuis Azure Allemagne vers Azure global.

## <a name="web-apps"></a>Web Apps

La migration des applications que vous avez créées à l’aide de la fonctionnalité Web Apps d’Azure App Service à partir d’Azure Allemagne vers Azure global n’est pas prise en charge actuellement. Nous vous recommandons d’exporter une application web comme modèle Azure Resource Manager. Ensuite, procédez au redéploiement après avoir modifié la propriété d’emplacement en indiquant la nouvelle région de destination.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Azure Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur App Service](https://docs.microsoft.com/azure/app-service/#step-by-step-tutorials).
- Découvrez comment [exporter des modèles Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Consultez la [présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
- Consultez la [présentation d’App Service](../app-service/overview.md).
- Lisez la [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="notification-hubs"></a>Notification Hubs

Pour migrer les paramètres d’une instance Azure Notification Hubs vers une autre instance, exportez, puis importez tous les jetons d’inscription et balises :

1. [Exportez les inscriptions aux hubs de notification existants](/previous-versions/azure/azure-services/dn790624(v=azure.100)) vers un conteneur Azure Storage Blob.
1. Créez un hub de notification dans l’environnement cible.
1. [Importez vos jetons d’inscription](/previous-versions/azure/azure-services/dn790624(v=azure.100)) depuis le Stockage blob sur votre nouveau hub de notification.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/#step-by-step-tutorials).
- Consultez la [présentation de Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les outils, techniques et recommandations pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Mise en réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
