---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les limitations et les problèmes connus avec Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f635360c5a6da19d60f3992878a8950b03c5f748
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513885"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article liste les fonctionnalités et outils qui ne sont pas encore pris en charge ou qui sont partiellement pris en charge avec les comptes de stockage ayant un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>API Stockage Blob

Les API Stockage Blob sont désactivées afin d’éviter les problèmes de fonctionnement des fonctionnalités qui peuvent se produire car les API Stockage Blob ne sont pas encore interopérables avec les API Azure Data Lake Gen2.

> [!NOTE]
> Avec la préversion publique de l’accès multiprotocole sur Data Lake Storage, les API d’objets blob et les API Data Lake Storage Gen2 peuvent opérer sur les mêmes données. Pour en savoir plus, consultez [Accès multiprotocole pour Data Lake Storage](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Que faire avec les services, applications et outils existants

Si l’un de ces derniers utilise des API d’objets blob et que vous souhaitez les utiliser pour travailler avec tout le contenu de votre compte, vous avez deux options.

* **Option 1** : N’activez pas un espace de noms hiérarchique sur votre compte de stockage d’objets blob tant que l’[accès multi-protocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md) n’est pas en disponibilité générale et que les API d’objets blob ne sont pas entièrement interopérables avec les API Azure Data Lake Gen2. L’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md) est actuellement offert en préversion publique.  L’utilisation d’un compte de stockage **sans** espace de noms hiérarchique signifie que vous n’aurez pas accès à des fonctionnalités spécifiques de Data Lake Storage Gen2, comme les listes de contrôle d’accès au répertoire et au conteneur.

* **Option 2** : Activez les espaces de noms hiérarchiques. Avec la préversion publique de l’[accès multi-protocole sur Data Lake Storage ](data-lake-storage-multi-protocol-access.md), les outils et les applications qui appellent des API d’objets blob, ainsi que les fonctionnalités de stockage d’objets blob (telles que les journaux de diagnostic) peuvent fonctionner avec des comptes dotés d’un espace de noms hiérarchique. Veillez à consulter cet article pour connaître les problèmes connus et les limitations.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Que faire si vous avez utilisé les API Blob pour charger des données avant la désactivation des API Blob

Si vous avez utilisé ces API pour charger des données avant leur désactivation, et que pour la production vous avez besoin d’accéder à ces données, contactez le Support Microsoft en spécifiant les informations suivantes :

> [!div class="checklist"]
> * ID d’abonnement (le GUID, pas le nom)
> * Noms des comptes de stockage
> * Si vous êtes impacté activement en production et, dans ce cas, pour quels comptes de stockage ?
> * Même si vous n’êtes pas activement impacté en production, dites-nous si vous avez besoin que ces données soient copiées vers un autre compte de stockage pour une raison quelconque et, le cas échéant, pourquoi ?

Dans ces circonstances, nous pouvons restaurer l’accès à l’API Blob pour une période limitée afin que vous puissiez copier ces données dans un compte de stockage pour lequel les espaces de noms hiérarchiques sont désactivés.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problèmes et limitations liés à l’utilisation des API d’objets BLOB sur les comptes qui ont un espace de noms hiérarchique

Avec la préversion publique de l’accès multiprotocole sur Data Lake Storage, les API d’objets blob et les API Data Lake Storage Gen2 peuvent opérer sur les mêmes données.

Cette section décrit les problèmes et les limitations liés à l’utilisation des API d’objets BLOB et des API Data Lake Storage Gen 2 pour fonctionner sur les mêmes données.

* Vous ne pouvez pas utiliser à la fois les API d’objets BLOB et les API Data Lake Storage pour écrire dans la même instance d’un fichier.

* Si vous écrivez dans un fichier à l’aide des API Data Lake Storage Gen 2, les blocs de ce fichier ne seront pas visibles pour les appels à l’API [Obtenir la liste de bloc](https://docs.microsoft.com/rest/api/storageservices/get-block-list) d’objets BLOB.

* Vous pouvez remplacer un fichier à l’aide des API Data Lake Storage Gen 2 ou des API d’objets BLOB. Cela n’affecte pas les propriétés du fichier.

* Lorsque vous utilisez l’opération [Lister les objets BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sans spécifier de délimiteur, les résultats incluront à la fois des répertoires et des objets BLOB.

  Si vous choisissez d’utiliser un délimiteur, n’utilisez qu’une barre oblique (`/`). Il s’agit du seul délimiteur pris en charge.

* Si vous utilisez l’API [Supprimer un objet BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) pour supprimer un répertoire, ce répertoire est supprimé uniquement s’il est vide.

  Cela signifie que vous ne pouvez pas utiliser les répertoires de suppression de l’API d’objet BLOB de manière récursive.

Ces API REST BLOB ne sont pas prises en charge :

* [Placer BLOB (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obtenir les portées de page](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Copie incrémentielle BLOB](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Placer la page à partir de l’URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Placer BLOB (ajouter)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Ajouter un bloc à partir d’une URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Que faire avec les disques de machines virtuelles non gérés

Les disques de machine virtuelle non gérés ne sont pas pris en charge dans les comptes qui ont un espace de noms hiérarchique. Si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez les disques de machine virtuelle non gérés dans un compte de stockage pour lequel la fonctionnalité espace de noms hiérarchique n’est pas activée.


## <a name="support-for-other-blob-storage-features"></a>Prise en charge d’autres fonctionnalités de Stockage Blob

Le tableau suivant liste tous les autres outils et fonctionnalités qui ne sont pas encore pris en charge ou qui sont partiellement pris en charge avec les comptes de stockage ayant un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

| Fonctionnalité / outil    | Plus d’informations    |
|--------|-----------|
| **API pour les comptes de stockage Data Lake Storage Gen2** | Partiellement pris en charge <br><br>L’accès multiprotocole sur Data Lake Storage est actuellement offert en préversion publique. Cette préversion vous permet d’utiliser des API d’objets BLOB dans les kits de développement logiciel .NET, Java et Python avec des comptes dotés d’un espace de noms hiérarchique.  Les kits de développement logiciel ne contiennent pas encore d’API qui vous permettent d’interagir avec des répertoires ou de définir des listes de contrôle d’accès (ACL). Pour exécuter ces fonctions, vous pouvez utiliser Data Lake Storage Gen 2 API **REST**. |
| **AZCopy** | Prise en charge propre à la version <br><br>Utilisez uniquement la dernière version d’AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy, telles qu’AzCopy v8.1, ne sont pas prises en charge.|
| **Stratégies de gestion du cycle de vie de Stockage Blob Azure** | Prises en charge par la préversion de l’[accès multi-protocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md). Les niveaux d’accès archive et froid sont pris en charge uniquement par la préversion. La suppression des instantanés d’objets BLOB n’est pas encore prise en charge. |
| **Azure Content Delivery Network (CDN)** | Pas encore pris en charge|
| **Recherche Azure** |Prises en charge par la préversion de l’[accès multi-protocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md).|
| **Azure Storage Explorer** | Prise en charge propre à la version <br><br>Utilisez uniquement la version `1.6.0` ou une version ultérieure. <br>La version `1.6.0` est disponible en tant que [téléchargement gratuit](https://azure.microsoft.com/features/storage-explorer/).|
| **Listes ACL de conteneur d’objets blob** |Pas encore pris en charge|
| **Blobfuse** |Pas encore pris en charge|
| **Domaines personnalisés** |Pas encore pris en charge|
| **Explorateur de système de fichiers** | Prise en charge limitée |
| **Journalisation de diagnostic** |Les journaux de diagnostic sont pris en charge par la préversion de l’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md). <br><br>L’activation des journaux dans le Portail Azure n’est pas prise en charge actuellement. Voici un exemple d’activation des journaux à l’aide de PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Veillez à spécifier `Blob` comme valeur du paramètre `-ServiceType`, comme indiqué dans cet exemple. <br><br>Actuellement, l’Explorateur Stockage Azure ne peut pas être utilisé pour l’affichage des journaux de diagnostic. Pour afficher les journaux, utilisez AzCopy ou des SDK.
| **Stockage non modifiable** |Pas encore pris en charge <br><br>Le stockage non modifiable vous donne la possibilité de stocker des données dans un état [WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage).|
| **Couches de niveau objet** |Les niveaux froid et archive sont pris en charge par la préversion de l’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md). <br><br> Tous les autres niveaux d’accès ne sont pas encore pris en charge.|
| **Prise en charge de PowerShell et de l’interface CLI** | Fonctionnalité limitée <br><br>Les opérations de gestion, telles que la création d’un compte, sont prises en charge. Les opérations de plan de données telles que le chargement et le téléchargement de fichiers sont en préversion publique dans le cadre de l’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md). L’utilisation de répertoires et la définition de listes de contrôle d’accès (ACL) ne sont pas encore prises en charge. |
| **Sites web statiques** |Pas encore pris en charge <br><br>Plus spécifiquement, la possibilité de délivrer des fichiers à des [sites web statiques](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Applications tierces** | Prise en charge limitée <br><br>Les applications tierces qui utilisent l’API REST continueront à fonctionner si vous les utilisez avec Data Lake Storage Gen2. <br>Les applications qui appellent des API d’objets blob fonctionneront probablement avec la préversion publique de l’[accès multiprotocole sur Data Lake Storage](data-lake-storage-multi-protocol-access.md). |
|**Suppression réversible** |Pas encore pris en charge|
| **Fonctionnalités de gestion de versions** |Pas encore pris en charge <br><br>Cela comprend la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) et d’autres fonctionnalités de gestion de versions telles que les [captures instantanées](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


