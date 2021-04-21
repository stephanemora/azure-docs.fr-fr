---
title: Configurer la préférence de routage réseau
titleSuffix: Azure Storage
description: Configurez une préférence de routage réseau pour votre compte de stockage Azure afin de spécifier comment le trafic réseau est acheminé vers votre compte à partir de clients sur Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790452"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configuration d’une préférence de routage réseau pour le Stockage Azure

Cet article explique comment configurer la préférence de routage réseau et les points de terminaison propres à l’itinéraire pour un compte de stockage. 

La préférence de routage réseau spécifie la façon dont le trafic réseau est acheminé vers votre compte à partir de clients sur Internet. Les points de terminaison propres à l’itinéraire constituent de nouveaux points de terminaison créés par le Stockage Azure pour le compte de stockage. Ils acheminent le trafic sur l’itinéraire souhaité sans modifier vos préférences de routage par défaut. Pour plus d’informations, consultez [Configuration d’une préférence de routage réseau pour le Stockage Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configuration de la préférence de routage pour le point de terminaison public par défaut

Par défaut, la préférence de routage pour le point de terminaison public du compte de stockage est définie sur le réseau global Microsoft. Vous pouvez choisir entre le routage réseau Microsoft mondial et le routage Internet comme préférence de routage par défaut pour le point de terminaison public de votre compte de stockage. Pour plus d’informations sur la différence entre ces deux types de routage, consultez [Préférence de routage réseau pour le Stockage Azure](network-routing-preference.md). 

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour faire du routage Internet votre préférence de routage, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez à votre compte de stockage dans le portail.

3. Sous **Paramètres**, choisissez **Mise en réseau**.

    > [!div class="mx-imgBorder"]
    > ![Option de menu Mise en réseau](./media/configure-network-routing-preference/networking-option.png)

4.  Dans l’onglet **Pare-feu et réseaux virtuels**, sous **Routage réseau**, changez le paramètre **Préférence de routage** en **Routage Internet**.

5.  Cliquez sur **Enregistrer**.

    > [!div class="mx-imgBorder"]
    > ![Option de routage Internet](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

   ```powershell
   Connect-AzAccount
   ```

2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

3. Pour définir votre préférence de routage sur le routage Internet, utilisez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) et définissez le paramètre `--routing-choice` sur `InternetRouting`.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom du groupe de ressources qui contient le compte de stockage.

   Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom du compte de stockage.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Connectez-vous à votre abonnement Azure.

   - Pour lancer Azure Cloud Shell, connectez-vous au [Portail Azure](https://portal.azure.com).

   - Pour vous connecter à votre installation locale de l’interface CLI, exécutez la commande [az login](/cli/azure/reference-index#az_login) :

     ```azurecli
     az login
     ```
2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

3. Pour définir votre préférence de routage sur le routage Internet, utilisez la commande [az storage account update](/cli/azure/storage/account#az_storage_account_update) et définissez le paramètre `--routing-choice` sur `InternetRouting`.

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom de votre compte de stockage.

---

## <a name="configure-a-route-specific-endpoint"></a>Configuration d’un point de terminaison propre à l’itinéraire

Vous pouvez également configurer un point de terminaison propre à l’itinéraire. Par exemple, vous pouvez définir la préférence de routage du point de terminaison par défaut sur *Routage Internet*, puis publier un point de terminaison propre à l’itinéraire qui autorise le trafic entre les clients sur Internet et votre compte de stockage à être acheminé sur le réseau global Microsoft.

Cette préférence n’a une incidence que sur le point de terminaison propre à l’itinéraire. Elle n’affecte pas la préférence de routage par défaut.  

### <a name="portal"></a>[Portail](#tab/azure-portal)

1.  Accédez à votre compte de stockage dans le portail.

2.  Sous **Paramètres**, choisissez **Mise en réseau**.

3.  Dans l’onglet **Pare-feu et réseaux virtuels**, sous **Publier des points de terminaison propres à l’itinéraire**, choisissez la préférence de routage de votre point de terminaison propre à l’itinéraire, puis cliquez sur **Enregistrer**.

    L’illustration suivante montre l’option **Routage réseau Microsoft** sélectionnée.

    > [!div class="mx-imgBorder"]
    > ![Option Routage réseau Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Pour configurer un point de terminaison propre à l’itinéraire, utilisez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). 

   - Pour créer un point de terminaison propre à l’itinéraire qui utilise la préférence de routage réseau Microsoft, définissez le paramètre `-PublishMicrosoftEndpoint` sur `true`. 

   - Pour créer un point de terminaison propre à l’itinéraire qui utilise la préférence de routage Internet, définissez le paramètre `-PublishInternetEndpointTo` sur `true`.  

   L’exemple suivant crée un point de terminaison propre à l’itinéraire qui utilise la préférence de routage réseau Microsoft.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom du groupe de ressources qui contient le compte de stockage.

   Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom du compte de stockage.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pour configurer un point de terminaison propre à l’itinéraire, utilisez la commande [az storage account update](/azure/storage/account#az_storage_account_update). 

   - Pour créer un point de terminaison propre à l’itinéraire qui utilise la préférence de routage réseau Microsoft, définissez le paramètre `--publish-microsoft-endpoints` sur `true`. 

   - Pour créer un point de terminaison propre à l’itinéraire qui utilise la préférence de routage Internet, définissez le paramètre `--publish-internet-endpoints` sur `true`.  

   L’exemple suivant crée un point de terminaison propre à l’itinéraire qui utilise la préférence de routage réseau Microsoft.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom du compte de stockage.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Identification du nom d’un point de terminaison propre à l’itinéraire

Si vous avez configuré un point de terminaison propre à l’itinéraire, vous le trouverez dans les propriétés de votre compte de stockage.

### <a name="portal"></a>[Portail](#tab/azure-portal)

1.  Sous **Paramètres**, choisissez **Propriétés**.

    > [!div class="mx-imgBorder"]
    > ![Option de menu Propriétés](./media/configure-network-routing-preference/properties.png)

2.  Le point de terminaison **Routage réseau Microsoft** est affiché pour chacun des services qui prennent en charge les préférences de routage. Cette image montre le point de terminaison des services Blob et Fichier.

    > [!div class="mx-imgBorder"]
    > ![Option de routage réseau Microsoft pour les points de terminaison propres à l’itinéraire](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Pour imprimer les points de terminaison sur la console, utilisez la propriété `PrimaryEndpoints` de l’objet de compte de stockage.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom du groupe de ressources qui contient le compte de stockage.

   Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom du compte de stockage.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pour imprimer les points de terminaison sur la console, utilisez la propriété [az storage account show](/cli/azure/storage/account#az_storage_account_show) de l’objet de compte de stockage.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Remplacez la valeur d’espace réservé `<resource-group-name>` par le nom du groupe de ressources qui contient le compte de stockage.

   Remplacez la valeur d’espace réservé `<storage-account-name>` par le nom du compte de stockage.

---

## <a name="see-also"></a>Voir aussi

- [Préférence de routage réseau](network-routing-preference.md)
- [Configurer des pare-feu et des réseaux virtuels dans Stockage Azure](storage-network-security.md)
- [Recommandations de sécurité pour Stockage Blob](../blobs/security-recommendations.md)
