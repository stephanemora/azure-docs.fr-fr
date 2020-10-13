---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: Apprenez-en davantage sur les limitations et les problèmes connus d’Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: c1e9e3d63e8a4f7fe461e2d33603da91d3d9bec6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441783"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article décrit les limitations et les problèmes connus d’Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Fonctionnalités du stockage Blob prises en charge

Les comptes ayant un espace de noms hiérarchique prennent en charge un nombre croissant de fonctionnalités de stockage Blob. Pour en obtenir la liste complète, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Intégrations de service Azure prises en charge

Azure Data Lake Storage Gen2 prend en charge plusieurs services Azure permettant d’ingérer des données, d’obtenir des données d’analytique et de créer des représentations visuelles. Pour obtenir la liste des services Azure pris en charge, consultez [Services Azure prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Consultez [Services Azure prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plateformes open source prises en charge

Plusieurs plateformes open source prennent en charge le stockage Data Lake Gen2. Pour obtenir une liste complète, consultez [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Consultez [Plateformes open source prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>API Stockage Blob

Les API Blob et les API Data Lake Storage Gen2 peuvent fonctionner sur les mêmes données.

Cette section décrit les problèmes et les limitations liés à l’utilisation des API d’objets BLOB et des API Data Lake Storage Gen 2 pour fonctionner sur les mêmes données.

* Vous ne pouvez pas utiliser à la fois les API d’objets BLOB et les API Data Lake Storage pour écrire dans la même instance d’un fichier. Si vous écrivez dans un fichier à l’aide des API Data Lake Storage Gen 2, les blocs de ce fichier ne seront pas visibles pour les appels à l’API [Obtenir la liste de bloc](https://docs.microsoft.com/rest/api/storageservices/get-block-list) d’objets BLOB. Vous pouvez remplacer un fichier à l’aide des API Data Lake Storage Gen 2 ou des API d’objets BLOB. Cela n’affecte pas les propriétés du fichier.

* Lorsque vous utilisez l’opération [Lister les objets BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sans spécifier de délimiteur, les résultats incluront à la fois des répertoires et des objets BLOB. Si vous choisissez d’utiliser un délimiteur, n’utilisez qu’une barre oblique (`/`). Il s’agit du seul délimiteur pris en charge.

* Si vous utilisez l’API [Supprimer un objet BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) pour supprimer un répertoire, ce répertoire est supprimé uniquement s’il est vide. Cela signifie que vous ne pouvez pas utiliser les répertoires de suppression de l’API d’objet BLOB de manière récursive.

Ces API REST BLOB ne sont pas prises en charge :

* [Placer BLOB (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obtenir les portées de page](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Copie incrémentielle BLOB](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Placer la page à partir de l’URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Ajouter un bloc à partir d’une URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Les disques de machine virtuelle non gérés ne sont pas pris en charge dans les comptes qui ont un espace de noms hiérarchique. Si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez les disques de machine virtuelle non gérés dans un compte de stockage pour lequel la fonctionnalité espace de noms hiérarchique n’est pas activée.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Prise en charge de la définition de listes de contrôle d’accès (ACL) de manière récursive

La possibilité d’appliquer les modifications aux listes ACL de manière récursive du répertoire parent vers les éléments enfants est proposée dans la [préversion publique](recursive-access-control-lists.md). Dans la version actuelle de cette fonctionnalité, vous pouvez appliquer des modifications aux listes ACL à l’aide de PowerShell, du SDK .NET et du SDK Python. La prise en charge n’est pas encore disponible pour le SDK Java, Azure CLI, le portail Azure ou l’Explorateur Stockage Azure.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Utilisez uniquement la dernière version d’AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy (telles qu’AzCopy v8.1) ne sont pas prises en charge.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Explorateur de stockage Azure

Utilisez uniquement les versions `1.6.0` ou ultérieures.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Explorateur Stockage dans le portail Azure

Les listes de contrôle d’accès ne sont pas prises en charge pour le moment.

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>Applications tierces

Les applications tierces qui utilisent les API REST continueront à fonctionner si vous les utilisez avec Data Lake Storage Gen2. Les applications qui appellent des API Blob fonctionneront probablement.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listes de contrôle d’accès (ACL) et accès en lecture anonyme

Si l’[accès en lecture anonyme](storage-manage-access-to-resources.md) a été accordé à un conteneur, les listes de contrôle d’accès n’ont aucun effet sur ce conteneur ou les fichiers de ce conteneur.

## <a name="premium-performance-blockblobstorage-storage-accounts"></a>Comptes de stockage BlockBlobStorage de performances Premium

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Les journaux de diagnostic ne peuvent pas être activés à l’aide du Portail Azure. Vous pouvez les activer à l’aide de PowerShell. Par exemple :

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

Le réglage du nombre de jours de rétention n’est pas encore pris en charge, mais vous pouvez supprimer les journaux manuellement à l’aide de n’importe quel outil pris en charge, comme Explorateur Stockage Azure, REST ou un Kit de développement logiciel (SDK).

### <a name="lifecycle-management-policies"></a>Stratégies de gestion du cycle de vie

- Les stratégies de gestion du cycle de vie sont prises en charge uniquement par les comptes universels v2. Elles ne sont pas encore prises en charge par les comptes de stockage BlockBlobStorage Premium.
- Les données ne peuvent pas être déplacées du niveau Premium vers des niveaux inférieurs.


### <a name="hdinsight-support"></a>Support HDInsight

Lorsque vous créez un cluster HDInsight, vous ne pouvez pas encore sélectionner un compte BlockBlobStorage sur lequel la fonctionnalité d’espace de noms hiérarchique est activée. Toutefois, vous pouvez attacher le compte au cluster après l’avoir créé.

### <a name="dremio-support"></a>Support Dremio

Dremio ne se connecte pas encore à un compte BlockBlobStorage sur lequel la fonctionnalité d’espace de noms hiérarchique est activée. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Pilote Windows Azure Storage Blob (WASB) (non pris en charge avec Data Lake Storage Gen2)

Actuellement, le pilote WASB, qui a été conçu pour fonctionner avec l’API Blob uniquement, rencontre des problèmes dans quelques scénarios courants. C’est le cas en particulier quand il s’agit d’un client pour un compte de stockage prenant en charge un espace de noms hiérarchique. L’accès multiprotocole sur Data Lake Storage n’atténue pas ces problèmes. 

Pour le moment (et probablement pour très longtemps), nous ne prenons pas en charge les utilisateurs qui utilisent le pilote WASB en tant que client pour un compte de stockage prenant en charge un espace de noms hiérarchique. Nous vous recommandons plutôt d’utiliser le pilote [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) dans votre environnement Hadoop. Si vous tentez d’effectuer une migration à partir d’un environnement Hadoop local avec une version antérieure à Hadoop Branch-3, ouvrez un ticket de support Azure pour que nous puissions vous contacter et vous indiquer la bonne direction pour vous et votre organisation.
