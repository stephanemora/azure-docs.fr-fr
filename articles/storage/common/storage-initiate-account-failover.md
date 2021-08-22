---
title: Lancer un basculement de compte de stockage
titleSuffix: Azure Storage
description: Apprenez à lancer un basculement de compte en cas d'indisponibilité du point de terminaison principal de votre compte de stockage. Le basculement met à jour la région secondaire pour qu'elle devienne la région primaire de votre compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/07/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bd1bbf0d1c5a49bde35d303e6780737fb43ca997
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524204"
---
# <a name="initiate-a-storage-account-failover"></a>Lancer un basculement de compte de stockage

Si le point de terminaison principal de votre compte de stockage géoredondant devient indisponible pour une raison quelconque, vous pouvez lancer un basculement de compte. Un basculement de compte met à jour le point de terminaison secondaire pour qu'il devienne le point de terminaison principal de votre compte de stockage. Au terme du basculement, les clients peuvent commencer à écrire dans la nouvelle région primaire. Le basculement forcé vous donne les moyens de garantir la haute disponibilité de vos applications.

Cet article explique comment effectuer un basculement de compte pour votre compte de stockage en utilisant le portail Azure, PowerShell ou l'interface de ligne de commande Azure. Pour plus d'informations sur le basculement de compte, consultez [Récupération d'urgence et basculement de compte de stockage](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Un basculement de compte entraîne généralement une certaine perte de données. Pour comprendre les implications d'un basculement de compte et vous préparer à la perte de données, consultez [Comprendre le processus de basculement de compte](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir effectuer un basculement de compte sur votre compte de stockage, assurez-vous que votre compte de stockage est configuré pour la géo-réplication. Votre compte de stockage peut utiliser l’une des options de redondance suivantes :

- Stockage géoredondant (GRS) ou stockage géographiquement redondant avec accès en lecture (RA-GRS)
- Stockage géoredondant interzone (GZRS) ou stockage géoredondant interzone avec accès en lecture (RA-GZRS)

Pour plus d’informations sur la redondance du Stockage Azure, consultez [Redondance du Stockage Azure](storage-redundancy.md).

N’oubliez pas que les fonctionnalités et services suivants ne sont pas pris en charge pour le basculement de compte :

- Azure File Sync ne prend pas en charge le basculement de compte de stockage. Les comptes de stockage contenant des partages de fichiers Azure utilisés en tant que points de terminaison cloud dans Azure File Sync ne doivent pas être basculés. Cela provoquera en effet un arrêt de la synchronisation et pourra entraîner une perte inattendue de données dans le cas de fichiers nouvellement hiérarchisés.
- Les comptes de stockage dans lesquels est activé un espace de noms hiérarchique (comme Data Lake Storage Gen2) ne sont pas pris en charge pour l’instant.
- Un compte de stockage contenant des objets blob de blocs premium ne peut pas être basculé. Les comptes de stockage qui prennent en charge les objets blob de blocs premium ne prennent pas en charge la géoredondance.
- Un compte de stockage incluant des conteneurs avec une [stratégie d’immuabilité WORM](../blobs/immutable-storage-overview.md) ne peut pas être basculé. Les stratégies de rétention temporelles ou les stratégies de conservation légale déverrouillées/verrouillées empêchent le basculement afin de maintenir la conformité.

## <a name="initiate-the-failover"></a>Lancez le basculement

## <a name="portal"></a>[Portail](#tab/azure-portal)

Pour lancer un basculement de compte à partir du portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage.
1. Sous **Paramètres**, sélectionnez **Géoréplication**. L'illustration suivante représente l'état de géoréplication et de basculement d'un compte de stockage.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Capture d’écran représentant l’état de géoréplication et de basculement":::

1. Vérifiez que votre compte de stockage est configuré en tant que stockage géoredondant (GRS) ou stockage géographiquement redondant avec accès en lecture (RA-GRS). Si ce n'est pas le cas, sélectionnez **Configuration** sous **Paramètres** pour mettre à jour votre compte et le rendre géoredondant.
1. La propriété **Heure de la dernière de synchronisation** indique le décalage entre le secondaire et le primaire. **Heure de la dernière de synchronisation** fournit une estimation de l'étendue de la perte de données que vous connaîtrez une fois le basculement terminé. Pour plus d’informations sur la vérification de la propriété **Heure de la dernière synchronisation**, consultez [Vérification de la propriété Heure de la dernière synchronisation d’un compte de stockage](last-sync-time-get.md).
1. Sélectionnez **Préparer un basculement**.
1. Passez en revue la boîte de dialogue de confirmation. Lorsque vous êtes prêt, entrez **Oui** pour confirmer votre choix et lancer le basculement.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Capture d’écran représentant la boîte de dialogue de confirmation d’un basculement de compte":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

La fonctionnalité de basculement de compte est généralement disponible, mais elle s’appuie toujours sur un module d’évaluation pour PowerShell. Pour lancer un basculement de compte à l'aide de PowerShell, vous devez d'abord installer le module Az.Storage [1.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview). Pour installer le module, procédez comme suit :

1. Désinstallez les installations précédentes d'Azure PowerShell :

    - Supprimez toutes les anciennes installations d'Azure PowerShell de Windows à l'aide du paramètre **Applications et fonctionnalités** situé sous **Paramètres**.
    - Supprimez tous les modules **Azure** de `%Program Files%\WindowsPowerShell\Modules`.

1. Vérifiez que la dernière version de PowerShellGet est installée. Ouvrez une fenêtre Windows PowerShell et exécutez la commande suivante pour installer la dernière version :

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Fermez, puis rouvrez la fenêtre PowerShell après l'installation de PowerShellGet.

1. Installez la dernière version d'Azure PowerShell :

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installez un module en préversion du Stockage Azure qui prend en charge le basculement de compte :

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Pour lancer un basculement de compte à partir de PowerShell, exécutez la commande suivante :

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour lancer un basculement de compte à partir de l'interface de ligne de commande Azure, exécutez la commande suivante :

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Conséquences importantes du basculement de compte

Lorsque vous lancez un basculement de compte pour votre compte de stockage, les enregistrements DNS du point de terminaison secondaire sont mis à jour pour que le point de terminaison secondaire devienne le point de terminaison principal. Assurez-vous de bien comprendre les conséquences possibles sur votre compte de stockage avant de procéder à un basculement.

Pour estimer l'étendue de la perte de données probable avant de procéder à un basculement, consultez la propriété **Heure de la dernière synchronisation**. Pour plus d’informations sur la vérification de la propriété **Heure de la dernière synchronisation**, consultez [Vérification de la propriété Heure de la dernière synchronisation d’un compte de stockage](last-sync-time-get.md).

Le temps nécessaire au basculement après l’initiation peut varier et dure en général moins d’une heure.

Au terme du basculement, votre compte de stockage devient un compte de stockage localement redondant (LRS) dans la nouvelle région primaire. Vous pouvez réactiver le stockage géoredondant (GRS) ou le stockage géographiquement redondant avec accès en lecture (RA-GRS) pour le compte. Notez que la conversion de LRS en GRS ou RA-GRS entraîne un coût supplémentaire. Le coût est dû aux frais de sortie du réseau pour répliquer à nouveau les données dans la nouvelle région secondaire. Pour plus d'informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

Une fois que vous avez réactivé GRS pour votre compte de stockage, Microsoft commence à répliquer les données de votre compte dans la nouvelle région secondaire. La durée de la réplication dépend de nombreux facteurs, notamment :

- Le nombre et la taille des objets du compte de stockage. De nombreux petits objets peuvent prendre plus de temps que des objets moins nombreux et plus volumineux.
- Les ressources disponibles pour la réplication en arrière-plan, telles que l’UC, la mémoire, le disque et la capacité WAN. Le trafic en direct est prioritaire sur la géoréplication.
- Si vous utilisez Stockage Blob, le nombre d’instantanés par blob.
- Si vous utilisez Stockage Table, la [stratégie de partitionnement des données](/rest/api/storageservices/designing-a-scalable-partitioning-strategy-for-azure-table-storage). Le processus de réplication ne peut pas évoluer au-delà du nombre de clés de partition que vous utilisez.
  
## <a name="next-steps"></a>Étapes suivantes

- [Récupération d'urgence et basculement de compte de stockage](storage-disaster-recovery-guidance.md)
- [Vérifier la propriété Heure de la dernière synchronisation pour un compte de stockage](last-sync-time-get.md)
- [Utilisez la géo-redondance pour concevoir des applications hautement disponibles](geo-redundant-design.md)
- [Tutoriel : Générer une application hautement disponible avec le stockage Blob](../blobs/storage-create-geo-redundant-storage.md)
