---
title: Créer une stratégie d’expiration pour les signatures d’accès partagé
titleSuffix: Azure Storage
description: Créez une stratégie sur le compte de stockage qui définit la durée pendant laquelle une signature d’accès partagé (SAP) doit être valide. Découvrez comment surveiller les violations de stratégie pour corriger les risques de sécurité.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8d9a381e80c829acc2f87aa6a856a651b19315ae
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709077"
---
# <a name="create-an-expiration-policy-for-shared-access-signatures"></a>Créer une stratégie d’expiration pour les signatures d’accès partagé

Vous pouvez utiliser une signature d’accès partagé (SAP) pour déléguer l’accès aux ressources de votre compte de stockage Azure. Un jeton SAP comprend la ressource ciblée, les autorisations accordées et l’intervalle pendant lequel l’accès est autorisé. Les meilleures pratiques recommandent de limiter l’intervalle d’une signature d’accès partagé au cas où elle serait compromise. En définissant une stratégie d’expiration des signatures d’accès partagé pour vos comptes de stockage, vous pouvez fournir une limite d’expiration supérieure recommandée lorsqu’un utilisateur crée une signature d’accès partagé.

Une stratégie d’expiration de signature d’accès partagé n’empêche pas un utilisateur de créer une signature d’accès partagé avec une expiration supérieure à la limite recommandée par la stratégie. Lorsqu’un utilisateur crée une signature d’accès partagé qui enfreint la stratégie, un avertissement s’affiche avec l’intervalle maximal recommandé. Si vous avez configuré un paramètre de diagnostic pour la journalisation avec Azure Monitor, le stockage Azure écrit dans une propriété dans les journaux à chaque fois qu’un utilisateur crée une SAP qui expire après l’intervalle recommandé.

Pour plus d’informations sur les signatures d’accès partagé, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](storage-sas-overview.md).

## <a name="create-a-sas-expiration-policy"></a>Créer une stratégie d’expiration SAS

Lorsque vous créez une stratégie d’expiration de SAP sur un compte de stockage, la stratégie s’applique à chaque type de SAP que vous pouvez créer sur ce compte de stockage, y compris une SAP de service, une SAP de délégation d’utilisateur ou une SAP de compte.

Pour configurer une stratégie d’expiration de SAP pour un compte de stockage, utilisez le Portail Azure, PowerShell ou Azure CLI.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Pour créer une stratégie d’expiration de SAP dans le portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous **Paramètres**, sélectionnez **Configuration**.
1. Localisez le paramètre permettant d’**Autoriser la limite supérieure recommandée pour l’intervalle d’expiration de la signature d’accès partagé (SAP)** est définissez-le sur **Activé**.
1. Spécifiez l’intervalle recommandé pour les nouvelles signatures d’accès partagé créées sur les ressources de ce compte de stockage.

    :::image type="content" source="media/sas-expiration-policy/configure-sas-expiration-policy-portal.png" alt-text="Capture d’écran montrant comment configurer une stratégie d’expiration de SAP dans le Portail Azure":::

1. Sélectionnez le bouton **Enregistrer** pour enregistrer vos modifications.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour créer une stratégie d’expiration de SAP, utilisez la commande [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount), puis définissez le paramètre `-SasExpirationPeriod` sur le nombre de jours, d’heures, de minutes et de secondes pendant lequel un jeton de SAP peut être actif à partir du moment où une signature d’accès partagé est signée. La chaîne que vous fournissez au paramètre `-SasExpirationPeriod` utilise le format suivant : `<days>.<hours>:<minutes>:<seconds>`. Par exemple, si vous souhaitez que la SAP expire 1 jour, 12 heures, 5 minutes et 6 secondes après sa signature, vous devez utiliser la chaîne `1.12:05:06`.

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account-name> `
    -SasExpirationPeriod <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> Vous pouvez également définir la stratégie d’expiration de SAP lorsque vous créez un compte de stockage en définissant le paramètre `-SasExpirationPeriod` de la commande [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

Pour vérifier que la stratégie a été appliquée, utilisez la propriété `SasPolicy` de [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount) retournée à la variable `$account` dans la commande précédente. 
  
```powershell
$account.SasPolicy
```

La période d’expiration de SAP apparaît dans la sortie de la console.

> [!div class="mx-imgBorder"]
> ![Période d’expiration de SAP](./media/storage-sas-expiration-policy/sas-policy-console-output.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer une stratégie d’expiration de SAP, utilisez la commande [az storage account update](/cli/azure/storage/account#az_storage_account_update), puis définissez le paramètre `--key-exp-days` sur le nombre de jours, d’heures, de minutes et de secondes pendant lequel un jeton de SAP peut être actif à partir du moment où une signature d’accès partagé est signée. La chaîne que vous fournissez au paramètre `--key-exp-days` utilise le format suivant : `<days>.<hours>:<minutes>:<seconds>`. Par exemple, si vous souhaitez que la SAP expire 1 jour, 12 heures, 5 minutes et 6 secondes après sa signature, vous devez utiliser la chaîne `1.12:05:06`.

```azurecli-interactive
az storage account update \
  --name <storage-account> \
  --resource-group <resource-group> \
  --sas-exp <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> Vous pouvez également définir la stratégie d’expiration de SAP lorsque vous créez un compte de stockage en définissant le paramètre `--key-exp-days` de la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create).

Pour vérifier que la stratégie a été appliquée, appelez la commande [az storage account show](/cli/azure/storage/account#az_storage_account_show) et utilisez la chaîne `{SasPolicy:sasPolicy}` pour le paramètre `-query`.
  
```azurecli-interactive
az storage account show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --query "{SasPolicy:sasPolicy}"
```

La période d’expiration de SAP apparaît dans la sortie de la console.

```json
{
  "SasPolicy": {
    "expirationAction": "Log",
    "sasExpirationPeriod": "1.12:05:06"
  }
}
```

---

## <a name="query-logs-for-policy-violations"></a>Requête de journaux de violations de stratégie

Pour consigner la création d’une signature d’accès partagé valide sur un intervalle plus long que celui recommandé par la stratégie d’expiration de SAP, commencez par créer un paramètre de diagnostic qui envoie des journaux à un espace de travail Azure Log Analytics. Pour plus d’informations, consultez l’article [Envoyer des journaux Azure Log Analytics](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics).

Ensuite, utilisez une requête de journal Azure Monitor pour déterminer si une SAP a expiré. Créez une nouvelle requête dans votre espace de travail Log Analytics, ajoutez le texte de requête suivant, puis appuyez sur **Exécuter**.

```kusto
StorageBlobLogs | where SasExpiryStatus startswith "Policy Violated" 
```

## <a name="see-also"></a>Voir aussi

- [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAP)](storage-sas-overview.md)
- [Créer une SAP de service](/rest/api/storageservices/create-service-sas)
- [Créer une SAP de délégation d’utilisateur](/rest/api/storageservices/create-user-delegation-sas)
- [Créer une SAP de compte](/rest/api/storageservices/create-account-sas)