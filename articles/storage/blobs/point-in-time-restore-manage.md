---
title: Activer et gérer une restauration dans le temps pour les objets blob de blocs (préversion)
titleSuffix: Azure Storage
description: Découvrez comment utiliser la limite de restauration dans le temps (préversion) pour restaurer des objets blob de blocs à un état antérieur.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9a4c68454807cb26ac62799b598f146680e37c42
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230175"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Activer et gérer une restauration dans le temps pour les objets blob de blocs (préversion)

Vous pouvez utiliser la limite de restauration dans le temps (préversion) pour restaurer des objets blob de blocs à un état antérieur. Cet article explique comment activer la limite de restauration dans le temps pour un compte de stockage avec PowerShell. Il explique également comment effectuer une opération de restauration avec PowerShell.

Pour plus d’informations et pour savoir comment s’inscrire à la préversion, consultez [Limite de restauration dans le temps pour les objets blob de blocs (préversion)](point-in-time-restore-overview.md).

> [!CAUTION]
> La limite de restauration dans le temps prend en charge la restauration des opérations sur les objets blob de blocs uniquement. Les opérations sur les conteneurs ne peuvent pas être restaurées. Si vous supprimez un conteneur du compte de stockage en appelant l’opération [Supprimer le conteneur](/rest/api/storageservices/delete-container) au cours de la restauration dans le temps en préversion, ce conteneur ne peut pas être restauré à l’aide d’une opération de restauration. Pendant la préversion, au lieu de supprimer un conteneur, supprimez chacun des objets blob si vous souhaitez les restaurer.

> [!IMPORTANT]
> La préversion de la restauration dans le temps est destinée uniquement à une utilisation hors production. Les contrats SLA (contrats de niveau de service) de production ne sont actuellement pas disponibles.

## <a name="install-the-preview-module"></a>Installez le module en préversion

Pour configurer la limite de restauration dans le temps Azure avec PowerShell, commencez par installer la version 1.14.1-preview ou ultérieure du module en préversion Az.Storage. L’utilisation de la préversion la plus récente est recommandée, mais la limite de restauration dans le temps est prise en charge dans le version 1.14.1-preview et versions ultérieures. Supprimez toutes les autres versions du module Az.Storage.

La commande suivante installe le module Az.Storage [2.0.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) :

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

La commande ci-dessus nécessite la version 2.2.4.1 ou supérieure de PowerShellGet. Pour déterminer la version chargée actuellement :
```powershell
Get-Module PowerShellGet
```
Pour en savoir plus sur l’installation d’Azure PowerShell, voir [Installer Azure PowerShell avec PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Activer et configurer la restauration dans le temps

Avant d’activer et de configurer la limite de restauration dans le temps, activez ses prérequis pour le compte de stockage : suppression réversible, flux de modification et gestion des versions d’objets blob. Pour plus d’informations sur l’activation de chacune de ces fonctionnalités, consultez les articles suivants :

- [Activer la suppression réversible pour les objets blob](soft-delete-enable.md)
- [Activer et désactiver le flux de modification](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Activer et gérer le contrôle de version des objets blob](versioning-enable.md)

Pour configurer la restauration dans le temps Azure avec PowerShell, appelez la commande Enable-AzStorageBlobRestorePolicy. L’exemple suivant active la suppression réversible et définit la période de rétention de suppression réversible, active le flux de modification, puis active la restauration dans le temps. Avant d’exécuter l’exemple, utilisez le portail Azure ou un modèle Azure Resource Manager pour activer également le contrôle de version d’objets blob.

Lorsque vous exécutez l’exemple, n’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Effectuer une opération de restauration

Pour lancer une opération de restauration, appelez la commande **Restore-AzStorageBlobRange**, en spécifiant le point de restauration sous forme de valeur UTC **DateTime**. Vous pouvez spécifier des plages lexicographiques de noms d'objets blob à restaurer ou omettre la plage pour restaurer tous les objets blob de tous les conteneurs du compte de stockage. Dix plages lexicographiques sont prises en charge par opération de restauration. Les objets blob de pages et les objets blob d’ajout ne sont pas inclus dans la restauration. L’opération de restauration peut prendre plusieurs minutes.

Gardez à l’esprit les règles suivantes lorsque vous spécifiez une plage d’objets blob à restaurer :

- Le modèle de conteneur spécifié pour la plage de début et la plage de fin doit comprendre au moins trois caractères. La barre oblique (/) utilisée pour séparer le nom de conteneur du nom d’objet blob n’est pas prise en compte dans cette valeur minimale.
- Vous pouvez spécifier dix plages par opération de restauration.
- Les caractères génériques ne sont pas pris en charge. Ils sont traités comme des caractères standard.
- Vous pouvez restaurer des objets blob dans les conteneurs `$root` et `$web` en les spécifiant explicitement dans une plage transmise à une opération de restauration. Les conteneurs `$root` et `$web` sont restaurés uniquement s’ils sont spécifiés explicitement. Les autres conteneurs système ne peuvent pas être restaurés.

> [!IMPORTANT]
> Lorsque vous effectuez une opération de restauration, Stockage Azure bloque les opérations de données sur les objets blob de la plage en cours de restauration pendant toute la durée de l'opération. Les opérations de lecture, d’écriture et de suppression sont bloquées dans l’emplacement principal. C’est la raison pour laquelle les opérations telles que l’énumération des conteneurs sur le portail Azure peuvent ne pas se dérouler comme prévu pendant que l’opération de restauration est en cours.
>
> Les opérations de lecture à partir de l’emplacement secondaire peuvent se poursuivre pendant l’opération de restauration si le compte de stockage est géorépliqué.

### <a name="restore-all-containers-in-the-account"></a>Restaurer tous les conteneurs du compte

Pour restaurer tous les conteneurs et objets blob du compte de stockage, appelez la commande **Restore-AzStorageBlobRange**, en omettant le paramètre `-BlobRestoreRange`. L’exemple suivant restaure les conteneurs du compte de stockage en leur appliquant l’état qu’ils présentaient 12 heures avant le moment présent :

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Restaurer une seule plage d’objets blob de blocs

Pour restaurer une plage d’objets blob, appelez la commande **Restore-AzStorageBlobRange** et spécifiez une plage lexicographique de noms de conteneurs et d’objets blob pour le paramètre `-BlobRestoreRange`. Le début de la plage est inclusive et la fin de la plage est exclusive.

Par exemple, pour restaurer les objets blob dans un seul conteneur appelé *exemple-conteneur*, vous pouvez spécifier une plage qui commence par *exemple-conteneur* et se termine par *exemple-conteneur1*. Il n’est pas nécessaire que les conteneurs nommés dans les plages de début et de fin existent. Étant donné que la fin de la plage est exclusive, même si le compte de stockage comprend un conteneur nommé *exemple-conteneur1*, seul le conteneur nommé *exemple-conteneur* sera restauré :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Pour spécifier un sous-ensemble d’objets blob dans un conteneur à restaurer, utilisez une barre oblique (/) pour séparer le nom du conteneur du modèle d’objet blob. Par exemple, la plage suivante sélectionne des objets blob dans un conteneur unique dont les noms commencent par les lettres *d* à *f* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Ensuite, fournissez la plage à la commande **Restore-AzStorageBlobRange**. Spécifiez le point de restauration en fournissant une valeur UTC **DateTime** pour le paramètre `-TimeToRestore`. L’exemple suivant restaure les objets blob de la plage spécifiée dans leur état 3 jours avant le moment présent :

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Restaurer plusieurs plages d’objets blob de blocs

Pour restaurer plusieurs plages d’objets blob de blocs, spécifiez un groupe de plages pour le paramètre `-BlobRestoreRange`. Dix plages sont prises en charge par opération de restauration. L'exemple suivant spécifie deux plages pour restaurer le contenu complet de *Container1* et *Container4* :

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>Restaurer des objets blob de blocs de manière asynchrone

Pour exécuter une opération de restauration de manière asynchrone, ajoutez le paramètre `-AsJob` à l’appel à **Restore-AzStorageBlobRange** et stockez le résultat de l’appel dans une variable. La commande **Restore-AzStorageBlobRange** retourne un objet de type **AzureLongRunningJob**. Vous pouvez vérifier la propriété **État** de cet objet pour déterminer si l’opération de restauration est terminée. La valeur de la propriété **État** peut être **En cours d’exécution** ou **Terminée**.

L’exemple suivant montre comment appeler une opération de restauration de manière asynchrone :

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Pour attendre la fin de l’opération de restauration une fois qu’elle est en cours d’exécution, appelez la commande [Wait-Job](/powershell/module/microsoft.powershell.core/wait-job), comme indiqué dans l’exemple suivant :

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Problèmes connus
- Pour un sous-ensemble de restaurations où des objets blob d’ajout sont présents, la restauration échoue. Pour le moment, veuillez ne pas effectuer de restauration si des objets blob d’ajout sont présents dans le compte.

## <a name="next-steps"></a>Étapes suivantes

- [Restauration dans le temps pour les objets blob de blocs (préversion)](point-in-time-restore-overview.md)
- [Suppression réversible](soft-delete-overview.md)
- [Flux de modification (préversion)](storage-blob-change-feed.md)
- [Contrôle de version des blobs](versioning-overview.md)
