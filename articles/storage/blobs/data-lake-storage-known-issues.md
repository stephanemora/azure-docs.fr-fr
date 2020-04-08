---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: Apprenez-en davantage sur les limitations et les problèmes connus d’Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061506"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article décrit les limitations et les problèmes connus d’Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Fonctionnalités du stockage Blob prises en charge

Les comptes ayant un espace de noms hiérarchique prennent en charge un nombre croissant de fonctionnalités de stockage Blob. Pour en obtenir la liste complète, consultez [Fonctionnalités de stockage blob disponibles dans Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Intégrations de service Azure prises en charge

Data Lake Storage Gen2 prend en charge plusieurs services Azure permettant d’ingérer des données, d’obtenir des données d’analytique et de créer des représentations visuelles. Pour obtenir la liste des services Azure pris en charge, consultez [Services Azure prenant en charge Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

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
* [Placer BLOB (ajouter)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Ajouter un bloc à partir d’une URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Les disques de machine virtuelle non gérés ne sont pas pris en charge dans les comptes qui ont un espace de noms hiérarchique. Si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez les disques de machine virtuelle non gérés dans un compte de stockage pour lequel la fonctionnalité espace de noms hiérarchique n’est pas activée.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Prise en charge des systèmes de fichiers dans les SDK

Actuellement, les opérations d’obtention et de définition de listes de contrôle d’accès ne sont pas récursives.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Prise en charge des systèmes de fichiers dans PowerShell et Azure CLI

- La prise en charge de [PowerShell](data-lake-storage-directory-file-acl-powershell.md) et d’[Azure CLI](data-lake-storage-directory-file-acl-cli.md) sont en préversion publique.
- Actuellement, les opérations d’obtention et de définition de listes de contrôle d’accès ne sont pas récursives.

## <a name="lifecycle-management-policies"></a>Stratégies de gestion du cycle de vie

* La suppression des instantanés d’objets BLOB n’est pas encore prise en charge.  

## <a name="archive-tier"></a>Niveau Archive

Il existe actuellement un bogue qui affecte le niveau d’accès Archive.


## <a name="blobfuse"></a>Blobfuse

Blobfuse n’est pas pris en charge.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Utilisez uniquement la dernière version d’AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy (telles qu’AzCopy v8.1) ne sont pas prises en charge.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Explorateur Stockage Azure

Utilisez uniquement les versions `1.6.0` ou ultérieures.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Explorateur Stockage dans le portail Azure

Les listes de contrôle d’accès ne sont pas prises en charge pour le moment.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Applications tierces

Les applications tierces qui utilisent les API REST continueront à fonctionner si vous les utilisez avec Data Lake Storage Gen2. Les applications qui appellent des API Blob fonctionneront probablement.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listes de contrôle d’accès (ACL) et accès en lecture anonyme

Si l’[accès en lecture anonyme](storage-manage-access-to-resources.md) a été accordé à un conteneur, les listes de contrôle d’accès n’ont aucun effet sur ce conteneur ou les fichiers de ce conteneur.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Pilote Windows Azure Storage Blob (WASB)

À l’heure actuelle, il existe plusieurs problèmes associés à l’utilisation du pilote WASB avec des comptes dotés d’un espace de noms hiérarchique. Nous vous recommandons d’utiliser le pilote [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) dans vos charges de travail. 





