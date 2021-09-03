---
title: Configurer des stratégies d’immuabilité pour les conteneurs
titleSuffix: Azure Storage
description: Découvrez comment configurer une stratégie d’immuabilité étendue à un conteneur. Les stratégies d’immuabilité offrent une prise en charge de type WORM (écriture unique, lectures multiples) pour le stockage d’objets blob en stockant les données dans un état non effaçable et non modifiable.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1cdc40b8aebe2d80553a23deec3990d4349ebd79
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255611"
---
# <a name="configure-immutability-policies-for-containers"></a>Configurer des stratégies d’immuabilité pour les conteneurs

Le stockage immuable pour le Stockage Blob Azure permet aux utilisateurs de stocker des données critiques pour l’entreprise dans un état WORM (écriture unique, lectures multiples). Dans l’état WORM, les données ne peuvent pas être modifiées ou supprimées pendant un intervalle spécifié par l’utilisateur. En configurant des stratégies d’immuabilité pour les données blob, vous pouvez protéger vos données contre les remplacements et les suppressions. Les stratégies d’immuabilité comprennent des stratégies de rétention temporelles et la conservation légale. Pour plus d’informations sur les stratégies de stockage immuable, consultez [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md).

Une stratégie d’immuabilité peut être étendue à une version d’objet blob individuelle (préversion) ou à un conteneur. Cet article explique comment configurer une stratégie d’immuabilité au niveau du conteneur. Pour savoir comment configurer des stratégies d’immuabilité au niveau de la version, consultez [Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)](immutable-policy-configure-version-scope.md).

## <a name="configure-a-retention-policy-on-a-container"></a>Configurer une stratégie de rétention sur un conteneur

Pour configurer une stratégie de rétention temporelle ou sur un conteneur, utilisez le Portail Azure, PowerShell ou Azure CLI. Vous pouvez configurer la stratégie de rétention au niveau du conteneur entre 1 et 146 000 jours.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour configurer une stratégie de rétention temporelle sur un conteneur, utilisez le Portail Azure et procédez comme suit :

1. Accédez au conteneur souhaité.
1. Sélectionnez le bouton **Plus** à droite et sélectionnez **Stratégie d’accès**.
1. Sous la section **Stockage d’objets blob immuables**, sélectionnez **Ajouter une stratégie**.
1. Dans le champ **Type de stratégie**, sélectionnez **Rétention temporelle**, puis spécifiez la période de rétention en jours.
1. Pour créer une stratégie avec une étendue de conteneur, n’activez pas la case à cocher **Activer l’immuabilité au niveau de la version**.
1. Si vous le souhaitez, sélectionnez **Autoriser les ajouts protégés supplémentaires** pour permettre l’écriture sur les objets blob d’ajouts protégés par une stratégie d’immuabilité. Pour plus d’informations, consultez [Autoriser les écritures sur les objets blob d’ajouts protégés](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

    :::image type="content" source="media/immutable-policy-configure-container-scope/configure-retention-policy-container-scope.png" alt-text="Capture d’écran montrant comment configurer l’étendue de la stratégie d’immuabilité au conteneur":::

Une fois que vous avez configuré la stratégie d’immuabilité, vous voyez qu’elle est étendue au conteneur :

:::image type="content" source="media/immutable-policy-configure-container-scope/view-retention-policy-container-scope.png" alt-text="Capture d’écran montrant une stratégie d’immuabilité existante étendue au conteneur":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour configurer une stratégie de rétention temporelle sur un conteneur avec PowerShell, appelez la commande [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) en spécifiant l’intervalle de rétention en jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 10
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer une stratégie de rétention temporelle sur un conteneur avec Azure CLI, appelez la commande [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) en spécifiant l’intervalle de rétention en jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage container immutability-policy \
    --resource-group <resource-group>
    --account-name <storage-account> \
    --container-name <container> \
    --period 10
```

---

## <a name="modify-an-unlocked-retention-policy"></a>Modifier une stratégie de rétention déverrouillée

Vous pouvez modifier une stratégie de rétention temporelle déverrouillée pour raccourcir ou prolonger l’intervalle de rétention et permettre des écritures supplémentaires sur les objets blob d’ajouts dans le conteneur. Vous pouvez également supprimer une stratégie déverrouillée.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour modifier une stratégie de rétention temporelle déverrouillée dans le Portail Azure, procédez comme suit :

1. Accédez au conteneur souhaité.
1. Sélectionnez le bouton **Plus** et choisissez **Stratégie d’accès**.
1. Sous la section **Version d’objets blob immuables**, recherchez la stratégie actuellement déverrouillée. Sélectionnez le bouton **Plus**, puis sélectionnez **Modifier** dans le menu.
1. Indiquez un nouvel intervalle de rétention pour la stratégie. Vous pouvez également sélectionner **Autoriser des ajouts protégés supplémentaires** pour autoriser les écritures sur les objets blob d’ajouts protégés.

    :::image type="content" source="media/immutable-policy-configure-container-scope/modify-retention-policy-container-scope.png" alt-text="Capture d’écran montrant comment modifier une stratégie temporelle de rétention déverrouillée":::

Pour supprimer une stratégie déverrouillée, sélectionnez le bouton **Plus**, puis **Supprimer**.

> [!NOTE]
> Vous pouvez activer les stratégies d’immuabilité au niveau de la version (préversion) en activant la case à cocher Activer l’immuabilité au niveau de la version. Pour plus d’informations sur la configuration de stratégies d’immuabilité au niveau de la version, consultez [Configurer des stratégies d’immuabilité pour les versions d’objets blob (préversion)](immutable-policy-configure-version-scope.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour modifier une stratégie déverrouillée, récupérez d’abord la stratégie en appelant la commande [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy). Ensuite, appelez la commande [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) pour mettre à jour la stratégie. Incluez le nouvel intervalle de rétention en jours et le paramètre `-ExtendPolicy`. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 21 `
    -AllowProtectedAppendWrite true `
    -Etag $policy.Etag `
    -ExtendPolicy
```

Pour supprimer une stratégie déverrouillée, appelez la commande [Remove-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/remove-azrmstoragecontainerimmutabilitypolicy).

```azurepowershell
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> 
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour modifier une stratégie déverrouillée de rétention temporelle avec Azure CLI, appelez la commande [az storage container immutability-policy extend](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_extend) en spécifiant le nouvel intervalle de rétention en jours. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
$etag=$(az storage container immutability-policy show /
        --account-name <storage-account> /
        --container-name <container> /
        --query etag /
        --output tsv) 

az storage container immutability-policy \
    --resource-group <resource-group>
    --account-name <storage-account> \
    --container-name <container> \
    --period 21 \
    --if-match $etag \
    --allow-protected-append-writes true 
```

Pour supprimer une stratégie déverrouillée, appelez la commande [az storage container immutability-policy delete](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_delete).

---

## <a name="lock-a-time-based-retention-policy"></a>Verrouiller une stratégie de rétention temporelle

Une fois que vous avez fini de tester une stratégie de rétention temporelle, vous pouvez verrouiller la stratégie. Les stratégies verrouillées sont conformes à la norme SEC 17A-4 (f) et à toute les autres conformités réglementaires. Vous pouvez augmenter jusqu’à cinq fois l’intervalle de rétention pour une stratégie verrouillée, mais vous ne pouvez pas le raccourcir.

Une fois qu’une stratégie est verrouillée, vous ne pouvez pas la supprimer. Toutefois, vous pouvez supprimer l’objet blob après l’expiration de l’intervalle de rétention.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour verrouiller une stratégie avec le portail Azure, procédez comme suit :

1. Accédez à un conteneur avec une stratégie déverrouillée.
1. Sous la section **Version d’objets blob immuables**, recherchez la stratégie actuellement déverrouillée. Sélectionnez le bouton **Plus**, puis sélectionnez **Verrouiller la stratégie** dans le menu.
1. Confirmez que vous souhaitez verrouiller la stratégie.

:::image type="content" source="media/immutable-policy-configure-container-scope/lock-retention-policy.png" alt-text="Capture d’écran montrant comment verrouiller la stratégie de rétention temporelle dans le portail Azure":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour verrouiller une stratégie avec PowerShell, appelez d’abord la commande [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) pour récupérer l’ETag de cette stratégie. Ensuite, appelez la commande [Lock-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/lock-azrmstoragecontainerimmutabilitypolicy) et transmettez la valeur de l’ETag pour verrouiller la stratégie. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> `
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour verrouiller une stratégie avec Azure CLI, appelez d’abord la commande [az storage container immutability-policy show](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_show) pour récupérer l’ETag de la stratégie. Ensuite, appelez la commande [az storage container immutability-policy lock](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_lock) et transmettez la valeur de l’ETag pour verrouiller la stratégie. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
$etag=$(az storage container immutability-policy show /
        --account-name <storage-account> /
        --container-name <container> /
        --query etag /
        --output tsv) 

az storage container immutability-policy lock /
    --resource-group <resource-group> /
    --account-name <storage-account> /
    --container-name <container> /
    --if-match $etag
```

---

## <a name="configure-or-clear-a-legal-hold"></a>Configurer ou effacer une conservation légale

La conservation légale stocke des données immuables jusqu’à ce que la conservation légale soit explicitement désactivée. Pour en savoir plus sur les stratégies de conservation légale, consultez [Conservation légale pour les données d’objets blob immuables](immutable-legal-hold-overview.md).

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour configurer une conservation légale sur un conteneur avec le portail Azure, effectuez les étapes suivantes :

1. Accédez au conteneur souhaité.
1. Sélectionnez le bouton **Plus** et choisissez **Stratégie d’accès**.
1. Sous la section **Version d’objets blob immuables**, sélectionnez **Ajouter une stratégie**.
1. Choisissez le type de stratégie **Conservation légale**, puis sélectionnez **OK** pour l’appliquer.

L’illustration suivante montre un conteneur avec une stratégie de rétention temporelle et une conservation légale configurée.

:::image type="content" source="media/immutable-policy-configure-container-scope/retention-policy-legal-hold-container-scope.png" alt-text="Capture d’écran montrant un conteneur avec une stratégie de rétention temporelle et une conservation légale configurée":::

Pour annuler la conservation légale, accédez à la boîte de dialogue **Stratégie d’accès**, sélectionnez le bouton **Plus** et choisissez **Supprimer**.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour configurer une conservation légale sur un conteneur avec PowerShell, appelez la commande [Add-AzRmStorageContainerLegalHold](/powershell/module/az.storage/add-azrmstoragecontainerlegalhold). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurepowershell
Add-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

Pour supprimer une conservation légale, appelez la commande [Remove-AzRmStorageContainerLegalHold](/powershell/module/az.storage/remove-azrmstoragecontainerlegalhold) :

```azurepowershell
Remove-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> ` 
    -Tag <tag1>,<tag2>,...
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer une conservation légale sur un conteneur avec PowerShell, appelez la commande [az storage container legal-hold set](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_set). N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```azurecli
az storage container legal-hold set /
    --tags tag1 tag2 /
    --container-name <container> /
    --account-name <storage-account> /
    --resource-group <resource-group>
```

Pour annuler la conservation légale, appelez la commande [az storage container legal-hold clear](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_clear) :

```azurecli
az storage container legal-hold clear /
    --tags tag1 tag2 /
    --container-name <container> /
    --account-name <storage-account> /
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Stocker des données blob critiques pour l’entreprise avec un stockage immuable](immutable-storage-overview.md)
- [Stratégies de rétention temporelle pour les données d’objets blob immuables](immutable-time-based-retention-policy-overview.md)
- [Conservation légale des données d’objets blob immuables](immutable-legal-hold-overview.md)
- [Configurer des stratégies d’immuabilité pour les versions d’objets BLOB (version préliminaire)](immutable-policy-configure-version-scope.md)
