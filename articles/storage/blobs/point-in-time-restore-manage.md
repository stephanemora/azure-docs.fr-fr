---
title: Effectuer une restauration jusqu’à une date et heure sur les données d’objet blob de blocs
titleSuffix: Azure Storage
description: Découvrez comment utiliser la restauration jusqu’à une date et heure pour restaurer un ensemble d’objets blob de blocs à leur état précédent à un moment donné.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cd58ae6aa0083b0f2a02ef1c008e76f92f39b766
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110676298"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Effectuer une restauration jusqu’à une date et heure sur les données d’objet blob de blocs

Vous pouvez utiliser la récupération jusqu`à une date et heure pour restaurer un ou plusieurs jeux d’objets blob de blocs à un état antérieur. Cet article explique comment activer la limite de restauration dans le temps pour un compte de stockage et comment effectuer une opération de restauration.

Pour en savoir plus sur la restauration jusqu’à une date et heure, consultez [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md).

> [!CAUTION]
> La limite de restauration dans le temps prend en charge la restauration des opérations sur les objets blob de blocs uniquement. Les opérations sur les conteneurs ne peuvent pas être restaurées. Si vous supprimez un conteneur du compte de stockage en appelant l’opération [Supprimer le conteneur](/rest/api/storageservices/delete-container), ce conteneur ne peut pas être restauré à l’aide d’une opération de restauration. Au lieu de supprimer un conteneur entier, supprimez chacun des objets blob si vous souhaitez les restaurer plus tard. Microsoft recommande également l’activation de la suppression réversible pour les conteneurs et les blobs afin de se protéger contre les suppressions accidentelles. Pour plus d’informations, consultez [Suppression réversible pour les conteneurs (préversion)](soft-delete-container-overview.md) et [Suppression réversible pour les blobs](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Activer et configurer la restauration dans le temps

Avant d’activer et de configurer la limite de restauration dans le temps, activez ses prérequis pour le compte de stockage : suppression réversible, flux de modification et gestion des versions d’objets blob. Pour plus d’informations sur l’activation de chacune de ces fonctionnalités, consultez les articles suivants :

- [Activer la suppression réversible pour les objets blob](./soft-delete-blob-enable.md)
- [Activer et désactiver le flux de modification](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Activer et gérer le contrôle de version des objets blob](versioning-enable.md)

> [!IMPORTANT]
> L’activation de la suppression réversible, du flux de modification et du contrôle de version des objets blob peut entraîner des frais supplémentaires. Pour plus d’informations, consultez [Suppression réversible pour les objets blob](soft-delete-blob-overview.md), [Prise en charge du flux de modification dans le stockage d’objets blob Azure](storage-blob-change-feed.md) et [Contrôle de version des objets blob](versioning-overview.md).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour configurer une limite de restauration dans le temps avec le Portail Azure, procédez comme suit :

1. Accédez à votre compte de stockage dans le portail Azure.
1. Sous **Paramètres**, choisissez **Protection des données**.
1. Sélectionnez la restauration **Activer la restauration dans le temps**. Lorsque vous sélectionnez cette option, la suppression réversible pour les objets blob, le contrôle de version et le flux de modification est également activée.
1. Définissez le point de restauration maximal pour la restauration jusqu’à une date et heure, en jours. Ce nombre doit être inférieur ou égal à la période de rétention spécifiée pour la suppression réversible d’objet blob.
1. Enregistrez vos modifications.

L’illustration suivante montre un compte de stockage configuré pour la récupération jusqu’à une date et heure avec un point de restauration de sept jours plus tôt, et une période de rétention pour la suppression réversible des objets blob de 14 jours.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Capture d’écran montrant comment configurer la restauration jusqu’à une date et heure dans le Portail Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour configurer la limite de restauration dans le temps avec PowerShell, commencez par installer le module [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) version 2.6.0 ou une version ultérieure. Appelez ensuite la commande [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) pour activer la restauration jusqu’à une date et heure pour le compte de stockage.

L’exemple suivant active la suppression réversible et définit la période de rétention de suppression réversible, active le flux de modification et le contrôle de version, puis active la restauration à un instant dans le passé. Lorsque vous exécutez l’exemple, n’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour configurer la récupération jusqu`à une date et heure avec Azure CLI, commencez par installer la version 2.2.0 ou ultérieure d’Azure CLI. Appelez ensuite la commande [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) pour activer la restauration jusqu’à une date et heure, ainsi que les autres paramètres requis de protection des données pour le compte de stockage.

L’exemple suivant active la suppression réversible et définit la période de rétention de la suppression réversible sur 14 jours, active le flux de modification et le contrôle de version, et active la récupération jusqu`à une date et heure avec une période de restauration de 7 jours. Lorsque vous exécutez l’exemple, n’oubliez pas de remplacer les valeurs entre crochets par vos propres valeurs :

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Choisir un point de restauration

Le point de restauration correspond à la date et à l’heure auxquelles les données sont restaurées. Stockage Azure utilise toujours une valeur de date/heure UTC comme point de restauration. Toutefois, le portail Azure vous permet de spécifier le point de restauration en heure locale, puis convertit cette valeur de date/heure en valeur de date/heure UTC pour effectuer l’opération de restauration.

Lorsque vous effectuez une opération de restauration à l’aide de PowerShell ou d’Azure CLI, vous devez spécifier le point de restauration comme valeur de date/heure UTC. Si le point de restauration est spécifié avec une valeur d’heure locale au lieu d’une valeur d’heure UTC, l’opération de restauration peut quand même se comporter comme prévu dans certains cas. Par exemple, si votre heure locale correspond à l’heure UTC moins cinq heures, le fait de spécifier une valeur d’heure locale se traduit par un point de restauration qui est cinq heures plus tôt que la valeur que vous avez fournie. Si aucune modification n’a été apportée aux données dans la plage à restaurer pendant cette période de cinq heures, l’opération de restauration produira les mêmes résultats, quelle que soit la valeur d’heure fournie. Il est recommandé de spécifier une heure UTC pour le point de restauration afin d’éviter des résultats inattendus.

## <a name="perform-a-restore-operation"></a>Effectuer une opération de restauration

Vous pouvez restaurer tous les conteneurs dans le compte de stockage, ou vous pouvez restaurer une plage d’objets blob dans un ou plusieurs conteneurs. Une plage d’objets blob est définie de façon lexicographique, ce qui signifie dans l’ordre du dictionnaire. Dix plages lexicographiques sont prises en charge par opération de restauration. Le début de la plage est inclusif et la fin de la plage est exclusive.

Le modèle de conteneur spécifié pour la plage de début et la plage de fin doit comprendre au moins trois caractères. La barre oblique (/) utilisée pour séparer le nom de conteneur du nom d’objet blob n’est pas prise en compte dans cette valeur minimale.

Les caractères génériques ne sont pas pris en charge dans une plage lexicographique. Les caractères génériques sont traités comme des caractères standard.

Vous pouvez restaurer des objets blob dans les conteneurs `$root` et `$web` en les spécifiant explicitement dans une plage transmise à une opération de restauration. Les conteneurs `$root` et `$web` sont restaurés uniquement s’ils sont spécifiés explicitement. Les autres conteneurs système ne peuvent pas être restaurés.

Seuls les objets blob de blocs sont restaurés. Les objets blob de pages et les objets blob d’ajout ne sont pas inclus dans une opération de restauration. Pour plus d’informations sur les limitations liées à l’ajout d’objets blob, consultez [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Lorsque vous effectuez une opération de restauration, Stockage Azure bloque les opérations de données sur les objets blob de la plage en cours de restauration pendant toute la durée de l'opération. Les opérations de lecture, d’écriture et de suppression sont bloquées dans l’emplacement principal. C’est la raison pour laquelle les opérations telles que l’énumération des conteneurs sur le portail Azure peuvent ne pas se dérouler comme prévu pendant que l’opération de restauration est en cours.
>
> Les opérations de lecture à partir de l’emplacement secondaire peuvent se poursuivre pendant l’opération de restauration si le compte de stockage est géorépliqué.
>
> Le temps nécessaire à la restauration d’un jeu de données dépend du nombre d’opérations d’écriture et de suppression effectuées au cours de la période de restauration. Par exemple, la restauration à un point situé 30 jours auparavant d’un compte avec 1 million d’objets, 3 000 objets ajoutés par jour et 1 000 objets supprimés par jour nécessite environ deux heures. Une période de rétention et une restauration à plus de 90 jours dans le passé ne sont pas recommandées pour un compte avec ce taux de changement.

### <a name="restore-all-containers-in-the-account"></a>Restaurer tous les conteneurs du compte

Vous pouvez restaurer tous les conteneurs dans le compte de stockage pour les ramener à leur état précédent à un moment donné.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour restaurer tous les conteneurs et objets blob dans le compte de stockage à l’aide du Portail Azure, procédez comme suit :

1. Accédez à la liste des conteneurs pour votre compte de stockage.
1. Dans la barre d’outils, choisissez **Restaurer les conteneurs**, puis **Tout restaurer**.
1. Dans le volet **Restaurer tous les conteneurs**, spécifiez le point de restauration en fournissant une date et une heure.
1. Confirmez que vous souhaitez continuer en cochant la case.
1. Sélectionnez **Restaurer** pour commencer l’opération de restauration.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Capture d’écran montrant comment restaurer tous les conteneurs sur un point de restauration spécifié":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour restaurer tous les conteneurs et blobs du compte de stockage à l’aide de PowerShell, appelez la commande **Restore-AzStorageBlobRange** et fournissez le point de restauration sous la forme d’une valeur de date/heure UTC. Par défaut, la commande **Restore-AzStorageBlobRange** s’exécute de façon asynchrone et retourne un objet de type **PSBlobRestoreStatus** que vous pouvez utiliser pour vérifier l’état de l’opération de restauration.

L’exemple suivant restaure de manière asynchrone les conteneurs du compte de stockage dans leur état 12 heures avant l’heure actuelle, et vérifie certaines des propriétés de l’opération de restauration :

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Pour exécuter l’opération de restauration de façon synchrone, incluez le paramètre **-WaitForComplete** sur la commande. Lorsque le paramètre **-WaitForComplete** est présent, PowerShell affiche un message qui comprend l’ID de restauration de l’opération, puis il bloque l’exécution jusqu’à ce que l’opération de restauration soit terminée. Gardez à l’esprit que la durée nécessaire à une opération de restauration dépend de la quantité de données à restaurer et qu’une opération de restauration peut prendre jusqu’à une heure.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour restaurer tous les conteneurs et blobs dans le compte de stockage à l’aide d’Azure CLI, appelez la commande [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) et fournissez le point de restauration sous la forme d’une valeur de date/heure UTC.

L’exemple suivant restaure de manière asynchrone tous les conteneurs du compte de stockage en leur appliquant l’état qu’ils présentaient 12 heures avant une date et une heure spécifiées. Pour vérifier l’état de l’opération de restauration, appelez [az storage account show](/cli/azure/storage/account#az_storage_account_show) :

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Pour vérifier les propriétés d'une opération de restauration, appelez [az storage account show](/cli/azure/storage/account#az_storage_account_show) et développez la propriété **blobRestoreStatus**. L'exemple suivant montre comment vérifier la propriété **status**.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Pour exécuter la commande **az storage blob restore** de façon synchrone et bloquer l’exécution jusqu’à la fin de l’opération de restauration, omettez le paramètre `--no-wait`.

---

### <a name="restore-ranges-of-block-blobs"></a>Restaurer des plages d’objets blob de blocs

Vous pouvez restaurer une ou plusieurs plages d’objets blob dans un seul conteneur ou dans plusieurs conteneurs pour ramener ces objets blob à leur état antérieur à un moment donné.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour restaurer une plage d’objets blob dans un ou plusieurs conteneurs avec le Portail Azure, procédez comme suit :

1. Accédez à la liste des conteneurs pour votre compte de stockage.
1. Sélectionnez le ou les conteneurs à restaurer.
1. Dans la barre d’outils, choisissez **Restaurer les conteneurs**, puis **Restaurer la sélection**.
1. Dans le volet **Restaurer les conteneurs sélectionnés**, spécifiez le point de restauration en fournissant une date et une heure.
1. Spécifiez les plages à restaurer. Utilisez une barre oblique (/) pour détourer le nom du conteneur du préfixe de l’objet blob.
1. Par défaut, le volet **Restaurer les conteneurs sélectionnés** spécifie une plage qui comprend tous les objets blob du conteneur. Supprimez cette plage si vous ne souhaitez pas restaurer l’intégralité du conteneur. La plage par défaut est indiquée dans l’image suivante.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Capture d’écran montrant la plage d’objets blob par défaut à supprimer avant de spécifier la plage personnalisée":::

1. Confirmez que vous souhaitez continuer en cochant la case.
1. Sélectionnez **Restaurer** pour commencer l’opération de restauration.

L’illustration suivante montre une opération de restauration sur un ensemble de plages.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Capture d’écran montrant comment restaurer des plages d’objets blob dans un ou plusieurs conteneurs":::

L’opération de restauration indiquée dans l’image effectue les actions suivantes :

- Restaure le contenu complet de *container1*.
- Restaure les objets blob dans la plage lexicographique de *blob1* à *blob5* dans *container2*. Cette plage restaure les objets blob avec des noms tels que *blob1*, *blob11*, *blob100*, *blob2*, et ainsi de suite. Étant donné que la fin de la plage est exclusive, elle restaure les objets blob dont le nom commence par *blob4*, mais pas les objets blob dont le nom commence par *blob5*.
- Restaure tous les objets blob dans *container3* et *container4*. Étant donné que la fin de la plage est exclusive, cette plage ne restaure pas *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour restaurer une unique plage d’objets blob, appelez la commande **Restore-AzStorageBlobRange** et spécifiez une plage lexicographique de noms de conteneurs et d’objets blob pour le paramètre `-BlobRestoreRange`. Par exemple, pour restaurer les objets blob dans un seul conteneur appelé *container1*, vous pouvez spécifier une plage qui commence par *container1* et se termine par *container2*. Il n’est pas nécessaire que les conteneurs nommés dans les plages de début et de fin existent. Étant donné que la fin de la plage est exclusive, même si le compte de stockage comprend un conteneur nommé *container2*, seul le conteneur nommé *container1* sera restauré :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Pour spécifier un sous-ensemble d’objets blob dans un conteneur à restaurer, utilisez une barre oblique (/) pour séparer le nom du conteneur du modèle de préfixe d’objet blob. Par exemple, la plage suivante sélectionne des objets blob dans un conteneur unique dont les noms commencent par les lettres *d* à *f* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Ensuite, fournissez la plage à la commande **Restore-AzStorageBlobRange**. Spécifiez le point de restauration en fournissant une valeur UTC **DateTime** pour le paramètre `-TimeToRestore`. L’exemple suivant restaure les objets blob de la plage spécifiée dans leur état 3 jours avant le moment présent :

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Par défaut, la commande **Restore-AzStorageblobRange** s’exécute de façon asynchrone. Lorsque vous lancez une opération de restauration de manière asynchrone, PowerShell affiche immédiatement une table de propriétés pour l’opération :  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Pour restaurer plusieurs plages d’objets blob de blocs, spécifiez un groupe de plages pour le paramètre `-BlobRestoreRange`. L’exemple suivant spécifie deux plages pour restaurer le contenu complet de *container1* et *container4* à leur état il y a 24 heures, et enregistre le résultat dans une variable :

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Pour exécuter l’opération de restauration de façon synchrone et bloquer l’exécution jusqu’à ce qu’elle soit terminée, incluez le paramètre **-WaitForComplete** sur la commande.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour restaurer une plage de blobs, appelez la commande [az storage blob restore](/cli/azure/storage/blob#az_storage_blob_restore) et spécifiez une plage lexicographique de noms de conteneurs et de blobs pour le paramètre `--blob-range`. Pour spécifier plusieurs plages, indiquez le paramètre `--blob-range` pour chaque plage distincte.

Par exemple, pour restaurer les objets blob dans un seul conteneur appelé *container1*, vous pouvez spécifier une plage qui commence par *container1* et se termine par *container2*. Il n’est pas nécessaire que les conteneurs nommés dans les plages de début et de fin existent. Étant donné que la fin de la plage est exclusive, même si le compte de stockage comprend un conteneur nommé *container2*, seul le conteneur nommé *container1* sera restauré.

Pour spécifier un sous-ensemble d’objets blob dans un conteneur à restaurer, utilisez une barre oblique (/) pour séparer le nom du conteneur du modèle de préfixe d’objet blob. L’exemple ci-dessous restaure de manière asynchrone une plage de blobs dans un conteneur dont les noms commencent par les lettres `d` à `f`.

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Pour exécuter la commande **az storage blob restore** de façon synchrone et bloquer l’exécution jusqu’à la fin de l’opération de restauration, omettez le paramètre `--no-wait`.

---

## <a name="next-steps"></a>Étapes suivantes

- [Restauration dans le temps pour les objets blob de blocs](point-in-time-restore-overview.md)
- [Suppression réversible](./soft-delete-blob-overview.md)
- [Flux de modification](storage-blob-change-feed.md)
- [Contrôle de version des blobs](versioning-overview.md)
