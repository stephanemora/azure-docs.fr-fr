---
title: Suppression réversible pour objets blob de stockage Azure | Microsoft Docs
description: Le Stockage Azure offre désormais une fonctionnalité de suppression réversible pour les objets blob, qui vous permet de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses de celles-ci par une application ou un autre utilisateur du compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 15db96824336c92611b9e1113c42c621f6508744
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978115"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Suppression réversible pour objets blob de Stockage Azure

Le Stockage Azure offre désormais une fonctionnalité de suppression réversible pour les objets blob, qui vous permet de récupérer plus facilement vos données en cas de modification ou de suppression malencontreuses de celles-ci par une application ou un autre utilisateur du compte de stockage.

## <a name="how-soft-delete-works"></a>Fonctionnement de la suppression réversible

Quand elle est activée, la suppression réversible vous permet d’enregistrer et de récupérer vos données en cas de suppression d’objets blob ou d’instantanés d’objets blob. Cette protection s’étend aux données d’objet blob effacées à la suite d’un remplacement.

Lorsque les données sont supprimées, elle passent par un état transitoire de suppression réversible au lieu d’être supprimées définitivement. Quand la suppression réversible est activée, lorsque vous remplacez des données, un instantané de l’objet blob supprimé de manière réversible est généré pour enregistrer l’état des données remplacées. Les objets supprimés de manière réversible sont invisibles, sauf s’ils sont répertoriés de façon explicite. Vous pouvez configurer la durée pendant laquelle les données supprimées de manière réversible seront récupérables avant leur expiration définitive.

La suppression réversible étant rétrocompatible, il est inutile d’apporter des modifications à vos applications pour tirer parti des protections qu’offre cette fonctionnalité. Toutefois, la fonctionnalité de [récupération des données](#recovery) introduit une nouvelle API de **rétablissement d’objet blob supprimé**.

### <a name="configuration-settings"></a>Paramètres de configuration

Lorsque vous créez un compte, la suppression réversible est désactivée par défaut. Elle est également désactivée par défaut pour les comptes de stockage existants. Vous pouvez activer ou désactiver la fonctionnalité à tout moment pendant la durée de vie d’un compte de stockage.

Vous pouvez toujours accéder aux données supprimées de manière réversible pour les récupérer lorsque la fonctionnalité est désactivée, pour autant que les données supprimées de manière réversible aient été enregistrées pendant que la fonctionnalité était activée. Lorsque vous activez la suppression réversible, vous devez également configurer la période de rétention.

La période de rétention indique la durée pendant laquelle les données supprimées de manière réversible sont stockées et disponibles pour récupération. Pour les objets blob et instantanés d’objets blob supprimés explicitement, le délai la période de rétention démarre dès la suppression des données. Pour les instantanés supprimés de manière réversible générés par la fonctionnalité de suppression réversible lors du remplacement de données, l’horloge démarre lors de la génération de l’instantané. Actuellement, vous pouvez conserver des données supprimées de manière réversible pendant une période de 1 à 365 jours.

Vous pouvez modifier la période de rétention de suppression réversible à tout moment. Une mise à jour de la période de rétention s’applique uniquement aux données supprimées par la suite. Les données supprimées antérieurement expireront à l’issue de la période de rétention qui était configurée lors de leur suppression. Une tentative de suppression d’un objet supprimé de manière réversible n’affecte pas son délai d’expiration.

### <a name="saving-deleted-data"></a>Enregistrement de données supprimées

La suppression réversible préserve vos données dans les nombreux cas où les objets blob ou instantanés d’objets blob sont supprimés ou remplacés.

Quand un objet blob est remplacé à l’aide d’une des commandes **Put Blob**, **Put Block**, **Put Block List** ou **Copy Blob**, une capture instantanée de l’état de l’objet blob avant l’opération d’écriture est générée automatiquement. Cet instantané étant supprimé de manière réversible, il est invisible, sauf si les objets supprimés de manière réversible sont répertoriés explicitement. Pour savoir comment répertorier des objets supprimés de manière réversible, voir la section [Récupération](#recovery).

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Si B0 est remplacé par B1, un instantané d’objet blob supprimé de manière réversible de B0 est généré. Si B1 est remplacé par B2, un instantané d’objet blob supprimé de manière réversible de B1 est généré.*

> [!NOTE]  
> La fonctionnalité de suppression réversible offre une protection en cas de remplacement pour les opérations de copie uniquement quand elle est activée pour le compte de l’objet blob de destination.

> [!NOTE]  
> La fonctionnalité de suppression réversible n’offre pas de protection en cas de remplacement pour les objets blob au niveau archive. Si un objet blob dans l’archive est remplacé par un nouvel objet blob à un niveau quelconque, l’objet blob remplacé expire définitivement.

Lors de l’appel de la commande **Delete Blob** sur un instantané, celui-ci est marqué comme supprimé de manière réversible. Aucun nouvel instantané n’est généré.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Lors de l’appel de la commande **Snapshot Blob**, B0 devient un instantané et B1 est l’état actif de l’objet blob. Quand l’instantané B0 est supprimé, il est marqué comme supprimé de manière réversible.*

Lors de l’appel de la commande **Delete Blob** sur un objet blob de base (tout objet blob qui est pas un instantané), celui-ci est marqué comme supprimé de manière réversible. Conformément au comportement précédent, l’appel de la commande **Delete Blob** sur un objet blob ayant des instantanés actifs renvoie une erreur. L’appel de la commande **Delete Blob** sur un objet blob ayant un instantané d’objet blob supprimé de manière réversible ne retourne pas d’erreur. Quand la suppression réversible est activée, vous pouvez toujours supprimer un objet blob et tous ses instantanés en une seule opération. Cela a pour effet de marquer l’objet blob de base et les instantanés comme étant supprimés de manière réversible.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Ici, un appel de la commande **Delete Blob** est effectué pour supprimer B2 et tous les instantanés qui y sont associés. L’objet blob actif, B2, ainsi que tous les instantanés associés à celui-ci, sont marqués comme étant supprimés de manière réversible.*

> [!NOTE]  
> Quand un objet blob supprimé de manière réversible est remplacé, un instantané d’objet blob supprimé de manière réversible reflétant l’état de l’objet blob avant l’opération d’écriture est généré automatiquement. Le nouvel objet blob hérite le niveau de l’objet blob remplacé.

La fonctionnalité de suppression réversible n’enregistre pas vos données en cas de suppression de conteneur ou de compte, ou en cas de remplacement de métadonnées et propriétés d’objet blob. Pour protéger un compte de stockage contre une suppression malencontreuse, vous pouvez configurer un verrou à l’aide d’Azure Resource Manager. Pour en savoir plus, voir l’article sur Azure Resource Manager intitulé [Verrouiller les ressources pour empêcher les modifications inattendues](../../azure-resource-manager/resource-group-lock-resources.md).

Le tableau suivant indique le comportement attendu quand la suppression réversible est activée :

| Opération d’API REST | Type de ressource | Description | Modification du comportement |
|--------------------|---------------|-------------|--------------------|
| [Supprimer](/rest/api/storagerp/StorageAccounts/Delete) | Compte | Supprime le compte de stockage avec l’ensemble des conteneurs et objets blob qui y figurent.                           | Aucune modification. Les conteneurs et objets blob dans le compte supprimé ne sont pas récupérables. |
| [Delete Container](/rest/api/storageservices/delete-container) | Conteneur | Supprime le conteneur avec tous les objets blob qui y figurent. | Aucune modification. Les objets blob figurant dans le conteneur supprimé ne sont pas récupérables. |
| [Put Blob](/rest/api/storageservices/put-blob) | Objets blob de blocs, d’ajouts et de pages | Crée ou remplace un objet blob dans un conteneur. | Si cette opération est utilisée pour remplacer un objet blob existant, un instantané de l’état de l’objet blob avant l’appel est généré automatiquement. Cela s’applique également à un objet blob précédemment supprimé de manière réversible si et seulement si celui-ci est remplacé par un objet blob du même type (objet blob de blocs, d’ajouts ou de pages). En cas de remplacement par un objet blob d’un autre type, toutes les données supprimées de manière réversible expireront définitivement. |
| [Delete Blob](/rest/api/storageservices/delete-blob) | Objets blob de blocs, d’ajouts et de pages | Marque un objet blob ou un instantané d’objet blob pour suppression. L’objet blob ou l’instantané est ensuite supprimé pendant le nettoyage de la mémoire | Si cette opération est utilisée pour supprimer un instantané d’objet blob, celui-ci est marqué comme étant supprimé de manière réversible. Si cette opération est utilisée pour supprimer un objet blob, celui-ci est marqué comme étant supprimé de manière réversible. |
| [Copy Blob](/rest/api/storageservices/copy-blob) | Objets blob de blocs, d’ajouts et de pages | Copie un objet blob source vers un objet blob de destination dans le même ou dans un autre compte de stockage. | Si cette opération est utilisée pour remplacer un objet blob existant, un instantané de l’état de l’objet blob avant l’appel est généré automatiquement. Cela s’applique également à un objet blob précédemment supprimé de manière réversible si et seulement si celui-ci est remplacé par un objet blob du même type (objet blob de blocs, d’ajouts ou de pages). En cas de remplacement par un objet blob d’un autre type, toutes les données supprimées de manière réversible expireront définitivement. |
| [Put Block](/rest/api/storageservices/put-block) | Objets blob de blocs | Crée un bloc à valider en tant qu’élément d’un objet blob de blocs. | Si cette opération est utilisée pour valider un bloc dans un objet blob actif, rien ne change. Si elle est utilisée pour valider un bloc dans un objet blob supprimé de manière réversible, un nouvel objet blob est créé, et un instantané est généré automatiquement pour capturer l’état de l’objet blob supprimé de manière réversible. |
| [Put Block List](/rest/api/storageservices/put-block-list) | Objets blob de blocs | Valide un objet blob en spécifiant le jeu d’ID de bloc composant cet objet blob de blocs. | Si cette opération est utilisée pour remplacer un objet blob existant, un instantané de l’état de l’objet blob avant l’appel est généré automatiquement. Cela s’applique également à tout objet blob précédemment supprimé de manière réversible si et seulement s’il s’agit d’un objet blob de blocs. En cas de remplacement par un objet blob d’un autre type, toutes les données supprimées de manière réversible expireront définitivement. |
| [Put Page](/rest/api/storageservices/put-page) | Objets blob de pages | Écrit une plage de pages dans un objet blob de pages. | Aucune modification. Les données de l’objet blob de pages remplacées ou effacées par cette opération n’étant pas enregistrées, elles sont irrécupérables. |
| [Append Block](/rest/api/storageservices/append-block) | Objets blob d’ajouts | Écrit un bloc de données à la fin d’un objet blob d’ajouts. | Aucune modification. |
| [Set Blob Properties](/rest/api/storageservices/set-blob-properties) | Objets blob de blocs, d’ajouts et de pages | Définit les valeurs des propriétés système pour un objet blob. | Aucune modification. Les propriétés d’objet blob remplacées ne sont pas récupérables. |
| [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) | Objets blob de blocs, d’ajouts et de pages | Définit les métadonnées définies par l’utilisateur pour l’objet blob spécifié en tant qu’une ou plusieurs paires nom-valeur. | Aucune modification. Les métadonnées de l’objet blob remplacé ne sont pas récupérables. |

Il est important de noter que l’appel de la commande « Put Page » pour remplacer ou effacer des plages de pages dans un objet blob de pages n’a pas pour effet de générer automatiquement des instantanés. Les disques de machine virtuelle s’appuient sur des objets blob de pages et utilisent la commande **Put Page** pour écrire des données.

### <a name="recovery"></a>Récupération

L’appel de l’opération [Undelete Blob](/rest/api/storageservices/undelete-blob) sur un objet blob de base supprimé de manière réversible a pour effet de restaurer comme actifs cet objet blob et tous les instantanés supprimés de manière réversible qui lui sont associés. L’appel de l’opération `Undelete Blob` sur un objet blob de base actif a pour effet de restaurer comme actifs tous les instantanés supprimés de manière réversible qui lui sont associés. Quand des instantanés sont restaurés comme actifs, ils ressemblent à des instantanés générés par l’utilisateur et ne remplacent pas l’objet blob de base.

Pour restaurer un objet blob en capture instantanée supprimée de manière réversible, vous pouvez appeler `Undelete Blob` sur l’objet blob de base. Ensuite, vous pouvez copier l’instantané sur l’objet blob ainsi activé. Vous pouvez également copier l’instantané vers un nouvel objet blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Les données supprimées de manière réversible s’affichent en gris, et les données actives en bleu. Les données écrites plus récemment s’affichent sous les données plus anciennes. Ici, la commande **Undelete Blob** est appelée sur l’objet blob B, ce qui a pour effet de restaurer comme actifs l’objet blob de base B1 ainsi que tous les instantanés qui y sont associés, en l’occurrence uniquement B0. Dans la deuxième étape, B0 est copié sur l’objet blob de base. Cette opération de copie génère un instantané supprimé de manière réversible de B1.*

Pour afficher les objets blob et instantanés d’objets blob supprimés de manière réversible, vous pouvez inclure des données supprimées dans la commande **List Blobs**. Vous pouvez afficher uniquement les objets blob de base supprimés de manière réversible ou inclure les instantanés d’objets blob supprimés. Pour toutes les données supprimées de manière réversible, vous pouvez afficher l’heure de leur suppression, ainsi que le nombre de jours avant leur expiration définitive.

### <a name="example"></a>Exemples

Voici la sortie de console d’un script .NET qui charge, remplace, crée un instantané, supprime et restaure un objet blob nommé *HelloWorld* quand la suppression réversible est activée :

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Pour obtenir un pointeur vers l’application qui a produit cette sortie, voir la section [Étapes suivantes](#next-steps).

## <a name="pricing-and-billing"></a>Tarification et facturation

Toutes les données supprimées de manière réversible sont facturées au même tarif que des données actives. Vous ne serez pas facturé pour des données supprimées définitivement à l’issue de la période de rétention configurée. Pour plus de détails sur les instantanés et la manière dont ils augmentent les coûts, voir [Présentation des frais liés aux instantanés](storage-blob-snapshots.md).

Vous ne serez pas facturé pour les transactions associées à la génération automatique d’instantanés. Les transactions **Undelete Blob** vous seront facturées au taux des opérations d’écriture.

Pour plus de détails sur les prix du Stockage Blob Azure en général, voir la [page relative aux tarifs de Stockage Blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

Lorsque vous activez initialement la suppression réversible, nous vous recommandons d’utiliser une courte période de rétention pour mieux comprendre l’impact de cette fonctionnalité sur votre facture.

## <a name="get-started"></a>Prise en main

Les étapes suivantes montrent comment prendre en main la suppression réversible.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Activez la suppression réversible pour blob sur votre compte de stockage à l’aide du Portail Azure :

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre compte de stockage. 

2. Accédez à l’option **Protection des données** sous **Service blob**.

3. Cliquez sur **Activé** sous **Suppression réversible blob**

4. Entrez le nombre de jours que vous souhaitez *conserver* sous **Stratégies de rétention**

5. Choisissez le bouton **Enregistrer** pour confirmer vos paramètres de protection des données

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Pour afficher les objets blob supprimés de manière réversible, activez la case à cocher **Afficher les objets blob supprimés**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Pour afficher les instantanés d’objets blob supprimés de manière réversible pour un objet blob donné, sélectionnez celui-ci, puis cliquez sur **Afficher les instantanés**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Assurez-vous que la case à cocher **Afficher les instantanés supprimés** est activée.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Lorsque vous cliquez sur un blob ou un instantané d’objet blob supprimés de manière réversible, notez les nouvelles propriétés de l’objet blob. Elles indiquent quand l’objet a été supprimé, et le nombre de jours restants avant l’expiration définitive de l’objet blob ou de l’instantané d’objet blob. Si l’objet supprimé de manière réversible n’est pas un instantané, vous avez également la possibilité d’annuler sa suppression.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

N’oubliez pas que l’annulation de la suppression d’un objet blob a également pour effet d’annuler la suppression de tous les instantanés associés. Pour annuler la suppression des instantanés d’objets blob supprimés de manière réversible pour un objet blob actif, cliquez sur celui-ci, puis sélectionnez **Annuler la suppression de tous les instantanés**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Après avoir annulé la suppression d’instantanés d’un objet blob, vous pouvez cliquer sur **Promouvoir** pour copier un instantané sur la racine du blob, ce qui a pour effet de restaurer l’objet blob à son instantané.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob. L’exemple suivant active la suppression réversible pour un sous-ensemble de comptes dans un abonnement :

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Vous pouvez vérifier que la suppression réversible a été activée à l’aide de la commande suivante :

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Pour récupérer des objets blob supprimés accidentellement, appelez la commande Undelete sur ceux-ci. N’oubliez pas que l’appel de la commande **Undelete Blob** sur les objets blob tant actifs que supprimés de manière réversible, a pour effet de restaurer comme actifs tous les instantanés d’objets blob supprimés de manière réversible. L’exemple suivant appelle la commande Undelete sur tous les objets blob, tant actifs que supprimés de manière réversible, figurant dans un conteneur :

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Pour rechercher la stratégie actuelle de conservation de suppression réversible, utilisez la commande suivante :

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="clitabazure-cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/azure-CLI)

Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob :

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Pour vérifier si la suppression réversible est activée, utilisez la commande suivante : 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob :

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="nettabnet"></a>[.NET](#tab/net)

Pour activer la suppression réversible, mettez à jour les propriétés du service du client d’un objet blob :

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Pour récupérer des objets blob supprimés accidentellement, appelez la commande Undelete sur ceux-ci. N’oubliez pas que l’appel de la commande **Undelete Blob** sur les objets blob tant actifs que supprimés de manière réversible, a pour effet de restaurer comme actifs tous les instantanés d’objets blob supprimés de manière réversible. L’exemple suivant appelle la commande Undelete sur tous les objets blob, tant actifs que supprimés de manière réversible, figurant dans un conteneur :

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Pour récupérer une version spécifique d’un objet blob, commencez par appeler la commande Undelete sur un objet blob, puis copiez l’instantané souhaité sur l’objet blob. L’exemple suivant récupère un objet blob de blocs en restaurant son instantané généré le plus récemment :

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

## <a name="special-considerations"></a>Considérations spéciales

S’il existe une possibilité de modification ou de suppression accidentelles de vos données par une application ou un autre utilisateur du compte de stockage, nous vous recommandons d’activer la suppression réversible. L’activation de la suppression réversible pour les données fréquemment remplacées peut entraîner une augmentation des frais en termes de capacité de stockage, ainsi qu’une latence plus élevée lors de la classification des objets blob. Vous pouvez réduire ce coût et cette latence supplémentaires en stockant les données fréquemment remplacées dans un compte de stockage distinct où la suppression réversible est désactivée. 

## <a name="faq"></a>Forum Aux Questions

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>Pour quels services de stockage puis-je utiliser la suppression réversible ?

Actuellement, la suppression réversible est disponible uniquement pour le stockage d’objets blob.

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>La suppression réversible est-elle disponible pour tous les types de compte de stockage ?

Oui, la suppression réversible est disponible pour les comptes de stockage d’objets blob, ainsi que pour les objets blob figurant dans des comptes de stockage (GPv1 et GPv2) à usage général. Les types de compte de stockage standard et premium sont pris en charge. La suppression réversible est disponible pour les disques non managés, qui sont des objets blob de pages en arrière-plan. La suppression réversible n’est pas disponible pour les disques managés.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>La suppression réversible est-elle disponible pour tous les niveaux de stockage ?

Oui, la suppression réversible est disponible pour tous les niveaux de stockage : chaud, froid et archive. Toutefois, la suppression réversible n’offre de protection contre le remplacement pour les objets blob au niveau archive.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Puis-je utiliser l’API de définition d’un niveau d’objet blob pour différencier les objets blob des instantanés supprimés de manière réversible ?

Oui. Les instantanés supprimés de manière réversible restent dans le niveau d’origine, mais l’objet blob de base est déplacé dans le nouveau niveau. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Les comptes de stockage Premium sont limités à 100 instantanés par objet blob. Les instantanés d’objets blob supprimés de manière réversible sont-ils pris en compte par rapport à cette limite ?

Non, ils ne le sont pas.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Puis-je activer la suppression réversible pour des comptes de stockage existants ?

Oui, la suppression réversible est configurable pour des comptes de stockage tant existants que nouveaux.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Si je supprime un compte ou un conteneur entier alors que la suppression réversible est activée, les objets blob associés sont-ils tous enregistrés ?

Non, si vous supprimez un compte ou un conteneur entiers, tous les objets blob associés sont supprimés définitivement. Pour plus d’informations sur la protection d’un compte de stockage contre les suppressions accidentelles, consultez [Verrouiller les ressources pour empêcher les modifications inattendues](../../azure-resource-manager/resource-group-lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Puis-je afficher les métriques de capacité de données supprimées ?

Les données supprimées de manière réversible sont incluses dans la capacité totale de votre compte de stockage. Pour plus d’informations sur le suivi et l’analyse de la capacité de stockage, consultez [Storage Analytics](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Si je désactive la suppression réversible, pourrai-je toujours accéder aux données supprimées de manière réversible ?

Oui, vous pourrez toujours accéder aux données supprimées de manière réversible non expirées, ainsi que les récupérer, même si la suppression réversible est désactivée.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Puis-je lire et copier les instantanés supprimés de manière réversible de mon objet blob ?  

Oui, mais vous devez commencer par appeler la commande Undelete sur l’objet blob.

### <a name="is-soft-delete-available-for-all-blob-types"></a>La suppression réversible est-elle disponible pour tous les types d’objets blob ?

Oui, la suppression réversible est disponible pour les objets blob de blocs, d’ajout et de pages.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>La suppression réversible est-elle disponible pour les disques de machine virtuelle ?  

La suppression réversible est disponible pour les disques non managés Standard et Premium, qui sont des objets blob de pages en arrière-plan. La suppression réversible vous permettra de récupérer uniquement des données supprimées par les opérations **Delete Blob**, **Put Blob**, **Put Block List**, **Put Block** et **Copy Blob**. Les données remplacées par un appel de la commande **Put Page** ne sont pas récupérables.

Étant donné qu’une machine virtuelle Azure écrit sur un disque non managé à l'aide d'appels à **Put Page**, la suppression réversible pour annuler des écritures sur un disque non managé à partir d'une machine virtuelle Azure n'est pas un scénario pris en charge.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Dois-je modifier mes applications existantes pour utiliser la suppression réversible ?

Vous pouvez tirer parti de la suppression réversible, quelle que soit la version d’API que vous utilisez. Toutefois, pour répertorier et récupérer des objets blob et instantanés d’objets blob supprimés de manière réversible, vous devez utiliser la version 2017-07-29 de l’[API REST des services de stockage](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) ou une version supérieure. Microsoft recommande de toujours utiliser la dernière version de l’API de stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

* [Exemple de code .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [API REST du service Blob](/rest/api/storageservices/blob-service-rest-api)
* [Réplication Azure Storage](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Conception d’applications hautement disponibles à l’aide du stockage RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Récupération d'urgence et basculement de compte de stockage (préversion) dans Stockage Azure](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
