---
title: Synchroniser du contenu à partir d’un dossier cloud
description: Découvrez comment déployer votre application dans Azure App Service via la synchronisation de contenu à partir d’un dossier cloud OneDrive ou Dropbox, par exemple.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051221"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronisation de contenu à partir d’un dossier cloud dans Azure App Service
Cet article vous explique comment synchroniser votre contenu avec [Azure App Service](./overview.md) à partir de Dropbox et OneDrive. 

Dans l’approche de la synchronisation de contenu, vous travaillez avec le code et le contenu de votre application dans un dossier cloud désigné pour vous assurer qu’il se trouve dans un état prêt à être déployé, puis opérez la synchronisation sur App Service en cliquant sur un bouton. 

En raison de différences sous-jacentes entre les API, **OneDrive Entreprise** n’est pas pris en charge pour l’instant.

> [!NOTE]
> La page **Centre de développement (classique)** dans le portail Azure, qui est l’ancienne expérience de déploiement, sera déconseillée en mars 2021. Cette modification n’affecte aucun paramètre de déploiement existant dans votre application, et vous pouvez continuer à gérer le déploiement d’applications dans la page du **Centre de déploiement** .

## <a name="enable-content-sync-deployment"></a>Activer le déploiement de la synchronisation de contenu

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Paramètres**. 

1. Dans **Source**, sélectionnez **OneDrive** ou **Dropbox**.

1. Cliquez sur **Autoriser** et suivez les invites d’autorisation. 

    ![Montre comment autoriser OneDrive ou Dropbox dans le Centre de déploiement du Portail Azure.](media/app-service-deploy-content-sync/choose-source.png)

    Vous ne devez autoriser qu’une seule fois avec OneDrive ou Dropbox pour votre compte Azure. Pour autoriser un autre compte OneDrive ou Dropbox pour une application, cliquez sur **Modifier le compte**.

1. Dans **Dossier**, sélectionnez le dossier à synchroniser. Ce dossier est créé sous le chemin de contenu désigné suivant dans OneDrive ou Dropbox. 
   
    * **OneDrive** : `Apps\Azure Web Apps`
    * **Dropbox** : `Apps\Azure`
    
1. Cliquez sur **Enregistrer**.

## <a name="synchronize-content"></a>Synchroniser le contenu

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Redéploiement/Synchronisation**. 

    ![Montre comment synchroniser votre dossier cloud avec App Service.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Cliquez sur **OK** pour confirmer la synchronisation.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Démarrez une synchronisation en exécutant la commande suivante et en remplaçant \<group-name> et \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Démarrez une synchronisation en exécutant la commande suivante et en remplaçant \<group-name> et \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Désactiver le déploiement de la synchronisation de contenu

1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de gestion de votre application App Service.

1. Dans le menu de gauche, cliquez sur **Centre de déploiement** > **Paramètres** > **Déconnecter**. 

    ![Montre comment déconnecter la synchronisation de votre dossier cloud avec votre application App Service dans le Portail Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer à partir du dépôt Git local](deploy-local-git.md)