---
title: Migrer des ressources web Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources web Azure depuis Azure Allemagne vers Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d67a4aadbe22b5447aa73063e48c0ca43fb45d04
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122562083"
---
# <a name="migrate-web-resources-to-global-azure"></a>Migrer des ressources web Azure vers Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Cet article contient des informations qui peuvent vous aider à migrer des ressources web Azure depuis Azure Allemagne vers Azure global.

## <a name="web-apps"></a>Web Apps

La migration des applications que vous avez créées à l’aide de la fonctionnalité Web Apps d’Azure App Service à partir d’Azure Allemagne vers Azure global n’est pas prise en charge actuellement. Nous vous recommandons d’exporter une application web comme modèle Azure Resource Manager. Ensuite, procédez au redéploiement après avoir modifié la propriété d’emplacement en indiquant la nouvelle région de destination.

> [!IMPORTANT]
> Modifiez l’emplacement, les secrets Azure Key Vault, les certificats et autres GUID de façon à assurer la cohérence avec la nouvelle région.

### <a name="migrate-web-app-resource"></a>Migrer une ressource d’application web

1. [Exportez l’application Web et le Plan App Service en tant que modèle](../azure-resource-manager/templates/export-template-portal.md) à partir de votre abonnement Azure Allemagne. Sélectionnez les ressources que vous souhaitez migrer dans votre groupe de ressources d’application Web et exportez-les en tant que modèle.
1. Téléchargez le modèle en tant que fichier zip. 
1. Modifiez la propriété de l’emplacement dans le fichier **template.json** pour la région global Azure cible. Par exemple, l’emplacement cible du fichier JSON suivant est *USA Ouest*.

    ```json
        "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('serverfarms_myappservice_name')]",
            "location": "West US",

    ```
1. Déployez le modèle modifié.sur Azure global. Par exemple, vous pouvez utiliser PowerShell pour déployer.

    ```powershell
    az deployment group create --name "<web app name>" \
        --resource-group "<resource group name>" \
        --template-file "<path of your template.json file>"
    ```

### <a name="migrate-web-app-content"></a>Migrer le contenu de l’application Web

1. Dans le portail Azure Allemagne, sélectionnez votre application Web.
1. Sélectionnez **Outils de développement > Outils avancés** .
1. Dans le menu principal, sélectionnez **Console de débogage** puis choisissez **PowerShell**.
1. Sélectionnez **site**.
1. Sélectionnez l’**icône télécharger** à côté du dossier **wwwroot**. Le fichier zip téléchargé contient le code source de votre application Web.
1. Déployez la racine Web dans l’application Web Azure globale migrée. Par exemple, vous pouvez utiliser le script PowerShell suivant.

    ``` powershell
    az webapp deployment source config-zip \
        --resource-group "<resource group name>" \
        --name "<web App name>" \
        --src "path to webroot folder zip file"
    ```

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur App Service](../app-service/tutorial-dotnetcore-sqldb-app.md).
- Découvrez comment [exporter des modèles Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md).
- Consultez la [présentation d’Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Consultez la [présentation d’App Service](../app-service/overview.md).
- Lisez la [présentation des emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="notification-hubs"></a>Notification Hubs

Pour migrer les paramètres d’une instance Azure Notification Hubs vers une autre instance, exportez, puis importez tous les jetons d’inscription et balises :

1. [Exportez les inscriptions aux hubs de notification existants](/previous-versions/azure/azure-services/dn790624(v=azure.100)) vers un conteneur Azure Storage Blob.
1. Créez un hub de notification dans l’environnement cible.
1. [Importez vos jetons d’inscription](/previous-versions/azure/azure-services/dn790624(v=azure.100)) depuis le Stockage blob sur votre nouveau hub de notification.

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).
- Consultez la [présentation de Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="event-hubs"></a>Event Hubs

Pour migrer un Event Hub Azure, vous exportez le modèle de ressource Event Hub à partir d’Azure Allemagne, puis vous déployez le modèle dans Azure global.

1. [Exportez Event Hub en tant que modèle](../azure-resource-manager/templates/export-template-portal.md) à partir de votre abonnement Azure Allemagne.
1. [Déployez le modèle Event Hub en tant que modèle personnalisé](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) dans votre abonnement Azure global. Chargez et déployez le modèle que vous avez exporté à partir de votre abonnement Azure Allemagne.

Pour plus d’informations :

- Consultez la [vue d’ensemble d’Event Hubs](../event-hubs/event-hubs-about.md).
- Consultez la [présentation d’Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Découvrez comment [exporter des modèles Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md).
- Apprenez comment [redéployer un modèle](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils d'administration](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)