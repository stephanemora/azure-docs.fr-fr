---
title: Lancer un basculement de compte de stockage (préversion) - Stockage Azure
description: Apprenez à lancer un basculement de compte en cas d'indisponibilité du point de terminaison principal de votre compte de stockage. Le basculement met à jour la région secondaire pour qu'elle devienne la région primaire de votre compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7529cfbd0ab75d0113e5cea666bc04aa1b15d30b
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157701"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Lancer un basculement de compte de stockage (préversion)

Si le point de terminaison principal de votre compte de stockage géoredondant devient indisponible pour une raison quelconque, vous pouvez lancer un basculement de compte (préversion). Un basculement de compte met à jour le point de terminaison secondaire pour qu'il devienne le point de terminaison principal de votre compte de stockage. Au terme du basculement, les clients peuvent commencer à écrire dans la nouvelle région primaire. Le basculement forcé vous donne les moyens de garantir la haute disponibilité de vos applications.

Cet article explique comment effectuer un basculement de compte pour votre compte de stockage en utilisant le portail Azure, PowerShell ou l'interface de ligne de commande Azure. Pour plus d'informations sur le basculement de compte, consultez [Récupération d'urgence et basculement de compte (préversion) dans Stockage Azure](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Un basculement de compte entraîne généralement une certaine perte de données. Pour comprendre les implications d'un basculement de compte et vous préparer à la perte de données, consultez [Comprendre le processus de basculement de compte](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir effectuer un basculement de compte sur votre compte de stockage, assurez-vous d'avoir effectué les étapes suivantes :

- Inscrivez-vous à la préversion de la fonctionnalité de basculement de compte. Pour plus d'informations sur la procédure d'inscription, consultez [À propos de la préversion](storage-disaster-recovery-guidance.md#about-the-preview).
- Vérifiez que votre compte de stockage est configuré pour utiliser soit le stockage géoredondant (GRS), soit le stockage géographiquement redondant avec accès en lecture (RA-GRS). Pour plus d'informations sur le stockage géoredondant, consultez [Redondance du stockage Azure](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>Conséquences importantes du basculement de compte

Lorsque vous lancez un basculement de compte pour votre compte de stockage, les enregistrements DNS du point de terminaison secondaire sont mis à jour pour que le point de terminaison secondaire devienne le point de terminaison principal. Assurez-vous de bien comprendre les conséquences possibles sur votre compte de stockage avant de procéder à un basculement.

Pour estimer l'étendue de la perte de données probable avant de procéder à un basculement, consultez la propriété **Heure de la dernière synchronisation** en utilisant la cmdlet PowerShell `Get-AzStorageAccount`, et incluez le paramètre `-IncludeGeoReplicationStats`. Consultez ensuite la propriété `GeoReplicationStats` de votre compte. \

Au terme du basculement, votre compte de stockage devient un compte de stockage localement redondant (LRS) dans la nouvelle région primaire. Vous pouvez réactiver le stockage géoredondant (GRS) ou le stockage géographiquement redondant avec accès en lecture (RA-GRS) pour le compte. Notez que la conversion de LRS en GRS ou RA-GRS entraîne un coût supplémentaire. Pour plus d'informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

Une fois que vous avez réactivé GRS pour votre compte de stockage, Microsoft commence à répliquer les données de votre compte dans la nouvelle région secondaire. Le temps de réplication dépend de la quantité de données répliquées.  

## <a name="portaltabazure-portal"></a>[Portail](#tab/azure-portal)

Pour lancer un basculement de compte à partir du portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage.
2. Sous **Paramètres**, sélectionnez **Géoréplication**. L'illustration suivante représente l'état de géoréplication et de basculement d'un compte de stockage.

    ![Capture d'écran représentant l'état de géoréplication et de basculement](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Vérifiez que votre compte de stockage est configuré en tant que stockage géoredondant (GRS) ou stockage géographiquement redondant avec accès en lecture (RA-GRS). Si ce n'est pas le cas, sélectionnez **Configuration** sous **Paramètres** pour mettre à jour votre compte et le rendre géoredondant. 
4. La propriété **Heure de la dernière de synchronisation** indique le décalage entre le secondaire et le primaire. **Heure de la dernière de synchronisation** fournit une estimation de l'étendue de la perte de données que vous connaîtrez une fois le basculement terminé.
5. Sélectionnez **Préparer un basculement (préversion)** . 
6. Passez en revue la boîte de dialogue de confirmation. Lorsque vous êtes prêt, entrez **Oui** pour confirmer votre choix et lancer le basculement.

    ![Capture d'écran représentant la boîte de dialogue de confirmation d'un basculement de compte](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour lancer un basculement de compte à l'aide de PowerShell, vous devez d'abord installer le module de préversion 6.0.1. Pour installer le module, procédez comme suit :

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

1. Fermez, puis rouvrez la fenêtre PowerShell.
 
Pour lancer un basculement de compte à partir de PowerShell, exécutez la commande suivante :

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour lancer un basculement de compte à partir de l'interface de ligne de commande Azure, exécutez la commande suivante :

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Étapes suivantes

- [Récupération d'urgence et basculement de compte (préversion) dans Stockage Azure](storage-disaster-recovery-guidance.md)
- [Conception d'applications hautement disponibles à l'aide du stockage RA-GRS](storage-designing-ha-apps-with-ragrs.md)
- [Tutoriel : Générer une application hautement disponible avec le stockage Blob](../blobs/storage-create-geo-redundant-storage.md) 
