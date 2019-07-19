---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les limitations et les problèmes connus avec Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: daf9199104047f714d568bd2796490b836243952
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443232"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article liste les fonctionnalités et outils qui ne sont pas encore pris en charge ou qui sont partiellement pris en charge avec les comptes de stockage ayant un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>API Stockage Blob

Les API Stockage Blob sont désactivées afin d’éviter les problèmes de fonctionnement des fonctionnalités qui peuvent se produire car les API Stockage Blob ne sont pas encore interopérables avec les API Azure Data Lake Gen2.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Que faire avec les services, applications et outils existants

Si l’un de ces éléments utilise des API Blob et que vous souhaitez les utiliser pour travailler avec tout le contenu que vous chargez sur votre compte, n’activez pas les espaces de noms hiérarchiques sur votre compte de stockage Blob avant que les API Blob deviennent interopérables avec les API Azure Data Lake Gen2.

L’utilisation d’un compte de stockage sans espace de noms hiérarchique signifie que vous n’avez alors pas accès aux fonctionnalités propres à Data Lake Storage Gen2, comme les listes de contrôle d’accès au répertoire et au système de fichiers.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Que faire avec les disques de machines virtuelles non managés

Ces disques dépendent des API Stockage Blob désactivées. Par conséquent, si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez-les dans un compte de stockage pour lequel la fonctionnalité d’espace de noms hiérarchique n’est pas activée.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Que faire si vous avez utilisé les API Blob pour charger des données avant la désactivation des API Blob

Si vous avez utilisé ces API pour charger des données avant leur désactivation, et que pour la production vous avez besoin d’accéder à ces données, contactez le Support Microsoft en spécifiant les informations suivantes :

> [!div class="checklist"]
> * ID d’abonnement (le GUID, pas le nom)
> * Noms des comptes de stockage
> * Si vous êtes impacté activement en production et, dans ce cas, pour quels comptes de stockage ?
> * Même si vous n’êtes pas activement impacté en production, dites-nous si vous avez besoin que ces données soient copiées vers un autre compte de stockage pour une raison quelconque et, le cas échéant, pourquoi ?

Dans ces circonstances, nous pouvons restaurer l’accès à l’API Blob pour une période limitée afin que vous puissiez copier ces données dans un compte de stockage pour lequel les espaces de noms hiérarchiques sont désactivés.

## <a name="all-other-features-and-tools"></a>Toutes les autres outils et fonctionnalités

Le tableau suivant liste tous les autres outils et fonctionnalités qui ne sont pas encore pris en charge ou qui sont partiellement pris en charge avec les comptes de stockage ayant un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

| Fonctionnalité / outil    | Plus d’informations    |
|--------|-----------|
| **API pour les comptes de stockage Data Lake Storage Gen2** | Partiellement pris en charge <br><br>Vous pouvez utiliser les API **REST** Data Lake Storage Gen2, mais les API dans d’autres kits SDK Blob tels que les SDK .NET, Java et Python ne sont pas encore disponibles.|
| **AZCopy** | Prise en charge propre à la version <br><br>Utilisez uniquement la dernière version d’AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy, telles qu’AzCopy v8.1, ne sont pas prises en charge.|
| **Stratégies de gestion du cycle de vie du Stockage Blob Azure** | Pas encore pris en charge |
| **Azure Content Delivery Network (CDN)** | Pas encore pris en charge|
| **Recherche Azure** |Pas encore pris en charge|
| **Azure Storage Explorer** | Prise en charge propre à la version <br><br>Utilisez uniquement la version `1.6.0` ou une version ultérieure. <br>La version `1.6.0` est disponible en tant que [téléchargement gratuit](https://azure.microsoft.com/features/storage-explorer/).|
| **Listes ACL de conteneur d’objets blob** |Pas encore pris en charge|
| **Blobfuse** |Pas encore pris en charge|
| **Domaines personnalisés** |Pas encore pris en charge|
| **Journaux de diagnostic** |Pas encore pris en charge|
| **Explorateur de système de fichiers** | Prise en charge limitée |
| **Stockage non modifiable** |Pas encore pris en charge <br><br>Le stockage non modifiable vous donne la possibilité de stocker des données dans un état [WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage).|
| **Couches de niveau objet** |Pas encore pris en charge <br><br>Par exemple :  niveaux Premium, Chaud, Froid et Archive.|
| **Prise en charge de PowerShell et de l’interface CLI** | Fonctionnalité limitée <br><br>Vous pouvez créer un compte à l’aide de Powershell ou de l’interface CLI. Vous ne pouvez pas effectuer d’opérations ni définir des listes de contrôle d’accès sur des systèmes de fichiers, des répertoires et des fichiers.|
| **Sites web statiques** |Pas encore pris en charge <br><br>Plus spécifiquement, la possibilité de délivrer des fichiers à des [sites web statiques](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Applications tierces** | Prise en charge limitée <br><br>Les applications tierces qui utilisent l’API REST continueront à fonctionner si vous les utilisez avec Data Lake Storage Gen2. <br>Si vous avez une application qui utilise des API Blob, cette application aura très probablement des problèmes si vous l’utilisez avec Data Lake Storage Gen2. Pour plus d’informations, consultez la section [Les API Stockage Blob sont désactivées pour les comptes de stockage Data Lake Storage Gen2](#blob-apis-disabled) de cet article.|
| **Fonctionnalités de gestion de versions** |Pas encore pris en charge <br><br>Cela inclut les [captures instantanées](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) et la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

