---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les limitations et les problèmes connus avec Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fac09ff236e4bb2c63691f9dc1ad41bb49edae4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793347"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article liste les fonctionnalités et outils qui ne sont pas encore pris en charge ou qui sont partiellement pris en charge avec les comptes de stockage ayant un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problèmes et limitations des API Blob

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

## <a name="filesystem-support-in-sdks"></a>Prise en charge des systèmes de fichiers dans les SDK

- .NET, Java et Python sont en préversion publique. D’autres SDK ne sont actuellement pas pris en charge.
- Les opérations d’extraction et de définition de listes de contrôle d’accès ne sont pas récursives.

## <a name="filesystem-support-in-powershell-and-azure-cli"></a>Prise en charge des systèmes de fichiers dans PowerShell et Azure CLI

Les opérations d’extraction et de définition de listes de contrôle d’accès ne sont pas récursives.

## <a name="support-for-other-blob-storage-features"></a>Prise en charge d’autres fonctionnalités de Stockage Blob

Le tableau suivant liste tous les autres outils et fonctionnalités qui ne sont pas encore pris en charge ou qui sont partiellement pris en charge avec les comptes de stockage ayant un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

| Fonctionnalité / outil    | Plus d’informations    |
|--------|-----------|
| **AZCopy** | Prise en charge propre à la version <br><br>Utilisez uniquement la dernière version d’AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy, telles qu’AzCopy v8.1, ne sont pas prises en charge.|
| **Stratégies de gestion du cycle de vie de Stockage Blob Azure** | Les stratégies de gestion du cycle de vie sont prises en charge (préversion).  Tous les niveaux d’accès sont pris en charge. Le niveau d’accès archive est actuellement en préversion. La suppression des instantanés d’objets BLOB n’est pas encore prise en charge. <br><br> Il existe actuellement des bogues affectant les stratégies de gestion du cycle de vie et le niveau d’accès à l’archive.  Inscrivez-vous à la préversion des stratégies de gestion du cycle de vie et du niveau d’accès archive [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).   |
| **Azure Content Delivery Network (CDN)** | Pas encore pris en charge|
| **Recherche Azure** |Prise en charge (préversion)|
| **Azure Storage Explorer** | Prise en charge propre à la version. <br><br>Utilisez uniquement les versions `1.6.0` ou ultérieures. <br> Il existe actuellement un bogue lié au stockage affectant la version `1.11.0` qui peut entraîner des erreurs d’authentification dans certains scénarios. Un correctif pour le bogue de stockage est en cours de déploiement, mais comme solution de contournement, nous vous recommandons d’utiliser la version `1.10.x` qui est disponible en [téléchargement gratuit](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` n’est pas affecté par le bogue de stockage.|
| **Listes ACL de conteneur d’objets blob** |Pas encore pris en charge|
| **Blobfuse** |Pas encore pris en charge|
| **Domaines personnalisés** |Pas encore pris en charge|
| **Explorateur Stockage sur le Portail Azure** | Prise en charge limitée. Les ACL ne sont pas encore prises en charge. |
| **Journalisation de diagnostic** |Les journaux de diagnostic sont pris en charge (préversion).<br><br>L’activation des journaux dans le Portail Azure n’est pas prise en charge actuellement. Voici un exemple d’activation des journaux à l’aide de PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Veillez à spécifier `Blob` comme valeur du paramètre `-ServiceType`, comme indiqué dans cet exemple. <br><br>Actuellement, l’Explorateur Stockage Azure ne peut pas être utilisé pour l’affichage des journaux de diagnostic. Pour afficher les journaux, utilisez AzCopy ou des SDK.
| **Stockage non modifiable** |Pas encore pris en charge <br><br>Le stockage non modifiable vous donne la possibilité de stocker des données dans un état [WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage).|
| **Couches de niveau objet** |Les niveaux froid et archive sont pris en charge. Le niveau archive est en préversion. Tous les autres niveaux d’accès ne sont pas encore pris en charge. <br><br> Il existe actuellement des bogues affectant le niveau d’accès de l’archive.  Inscrivez-vous à la préversion du niveau d’accès de l’archive [ici](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).|
| **Sites web statiques** |Pas encore pris en charge <br><br>Plus spécifiquement, la possibilité de délivrer des fichiers à des [sites web statiques](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Applications tierces** | Prise en charge limitée <br><br>Les applications tierces qui utilisent l’API REST continueront à fonctionner si vous les utilisez avec Data Lake Storage Gen2. <br>Les applications qui appellent des API Blob ont de grandes chances de fonctionner.|
|**Suppression réversible** |Pas encore pris en charge|
| **Fonctionnalités de gestion de versions** |Pas encore pris en charge <br><br>Cela comprend la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) et d’autres fonctionnalités de gestion de versions telles que les [captures instantanées](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


