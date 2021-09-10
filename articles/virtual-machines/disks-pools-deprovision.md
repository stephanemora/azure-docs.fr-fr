---
title: Annuler l’approvisionnement d’un pool de disques Azure (préversion)
description: Découvrez comment annuler l’approvisionnement d’un pool de disques Azure, l’arrêter et le supprimer.
author: roygara
ms.date: 07/19/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 823dd066e1751896b3f7986a18d18d80d68219a1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469867"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>Annuler l’approvisionnement d’un pool de disques Azure (préversion)

Cet article décrit le processus de suppression d’un pool de disques Azure (préversion) et explique comment désactiver la prise en charge iSCSI.

## <a name="stop-a-disk-pool"></a>Arrêter un pool de disques

Vous pouvez arrêter un pool de disques pour réduire les coûts et conserver toutes les configurations. Lorsqu’un pool de disques est arrêté, vous ne pouvez plus vous y connecter via iSCSI. Les ressources managées déployées pour prendre en charge le pool de disques ne seront pas supprimées. Vous devez d’abord déconnecter tous les clients ayant des connexions iSCSI au pool de disques avant d’arrêter un pool de disques. Vous pouvez démarrer un pool de disques à tout moment. Cette opération réactivera la cible iSCSI exposée sur ce pool de disques.
# <a name="portal"></a>[Portail](#tab/azure-portal)

Cette fonctionnalité n’est actuellement pas prise en charge dans le portail Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name 'myDiskPool' -ResourceGroupName 'myResourceGroupt'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool stop --name "myDiskPool" --resource-group "myResourceGroup"
```
---

## <a name="delete-a-disk-pool"></a>Supprimer un pool de disques

Quand vous supprimez un pool de disques, toutes les ressources du groupe de ressources managé sont également supprimées. Si des connexions iSCSI au pool de disques sont en attente, vous ne pouvez pas supprimer le pool de disques. Vous devez d’abord déconnecter tous les clients ayant des connexions iSCSI au pool de disques avant d’arrêter un pool de disques. Les disques qui ont été ajoutés au pool de disques ne seront pas supprimés.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez et sélectionnez **Pool de disques**, puis sélectionnez le pool de disques à supprimer.
1. En haut de la page, sélectionnez **Supprimer**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPool -Name "myDiskpoolName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool delete --name "myDiskPool" --resource-group "myResourceGroup"
```

---

## <a name="disable-iscsi-support"></a>Désactiver la prise en charge iSCSI

Si vous désactivez la prise en charge iSCSI sur un pool de disques, vous ne pouvez plus utiliser ce pool.

Lorsque vous activez pour la première fois la prise en charge iSCSI sur un pool de disques, une cible iSCSI est créée en tant que point de terminaison de la connexion iSCSI. Vous pouvez désactiver la prise en charge iSCSI sur le pool de disques en supprimant la cible iSCSI. Chaque pool de disques ne peut avoir qu’une seule cible iSCSI configurée.

Vous pouvez réactiver la prise en charge iSCSI sur un pool de disques existant. La prise en charge iSCSI ne peut pas être désactivée sur le pool de disques si des connexions iSCSI au pool de disques sont en attente.

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Recherchez **Pool de disques** et sélectionnez votre pool de disques.
1. Sous **Paramètres**, sélectionnez **iSCSI**.
1. Décochez la case **Activer iSCSI** et sélectionnez **Enregistrer**.    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "myDiskpoolName" -Name "myiSCSITargetName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "myDiskPool" --name "myIscsiTarget" --resource-group "myResourceGroup"
```

---

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [disques gérés Azure](managed-disks-overview.md).