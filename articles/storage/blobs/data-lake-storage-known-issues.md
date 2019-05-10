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
ms.openlocfilehash: 27adc0eeeabed2b1f2e86f301a60604a3d358b82
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464716"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article répertorie les fonctionnalités et outils qui ne sont pas encore pris en charge ou partiellement pris en charge avec les comptes de stockage qui ont un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>API Stockage Blob

Stockage d’objets BLOB Qu'api sont désactivées pour éviter les problèmes d’accès malencontreuse de données qui peut se produire, car les API de stockage d’objets Blob ne sont pas encore interopérable avec les API de génération 2 d’Azure Data Lake.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Que faire avec les services, applications et outils existants

Si une de ces API d’objet Blob d’utilisation et que vous souhaitez les utiliser pour travailler avec tout le contenu que vous téléchargez sur votre compte, puis n’activez pas un espace de noms hiérarchique sur votre compte de stockage d’objets Blob jusqu'à ce que l’API de l’objet Blob de devenir interopérable avec les API de génération 2 d’Azure Data Lake.

À l’aide d’un compte de stockage sans espace de noms hiérarchique signifie que vous n’avez alors pas accès à Data Lake Storage Gen2 des fonctionnalités spécifiques, telles que des listes de contrôle d’accès système répertoire et fichier.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Que faire avec les disques de Machine virtuelle (VM) non gérés

Ceux-ci varient selon les API de stockage d’objets Blob désactivé, si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, vous devez donc les placer dans un compte de stockage qui n’a pas activé la fonctionnalité d’espace de noms hiérarchique.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Que faire si vous avez utilisé les API d’objet Blob pour charger des données avant de l’API d’objet Blob ont été désactivés

Si vous avez utilisé ces API pour charger des données avant leur désactivation, et que pour la production vous avez besoin d’accéder à ces données, contactez le Support Microsoft en spécifiant les informations suivantes :

> [!div class="checklist"]
> * ID d’abonnement (le GUID, pas le nom).
> * Noms de compte de stockage.
> * Si vous êtes affecté activement en production et dans ce cas, les comptes de stockage ?.
> * Même si vous n’êtes pas activement impacté en production, dites-nous si vous avez besoin que ces données soient copiées vers un autre compte de stockage pour une raison quelconque et, le cas échéant, pourquoi ?

Dans ces circonstances, nous pouvons restaurer l’accès à l’API Blob pour une période limitée afin que vous puissiez copier ces données dans un compte de stockage pour lequel les espaces de noms hiérarchiques sont désactivés.

## <a name="all-other-features-and-tools"></a>Toutes les autres fonctionnalités et outils

Le tableau suivant répertorie toutes les autres fonctionnalités et outils qui ne sont pas encore pris en charge ou partiellement pris en charge avec les comptes de stockage qui ont un espace de noms hiérarchique (Azure Data Lake Storage Gen2).

| Fonctionnalité / outil    | Plus d’informations    |
|--------|-----------|
| **API pour les comptes de stockage Data Lake Storage Gen2** | Partiellement pris en charge <br><br>Vous pouvez utiliser Data Lake Storage Gen2 **REST** API, mais les API dans d’autres kits de développement logiciel Blob tels que les kits de développement .NET, Java, Python n’est pas encore disponibles.|
| **AZCopy** | Prise en charge spécifique à la version <br><br>Utiliser uniquement la dernière version d’AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Les versions antérieures d’AzCopy comme AzCopy v8.1, ne sont pas pris en charge.|
| **Stratégies de gestion du cycle de vie de stockage Blob Azure** | Pas encore pris en charge |
| **Azure Content Delivery Network (CDN)** | Pas encore pris en charge|
| **Azure Event Grid** | Pas encore pris en charge |
| **Recherche Azure** |Pas encore pris en charge|
| **Azure Storage Explorer** | Prise en charge spécifique à la version <br><br>Utilisez uniquement la version `1.6.0` ou une version ultérieure. <br>Version `1.6.0` est disponible en tant qu’un [téléchargement gratuit](https://azure.microsoft.com/features/storage-explorer/).|
| **ACL de conteneur d’objets BLOB** |Pas encore pris en charge|
| **Blobfuse** |Pas encore pris en charge|
| **Domaines personnalisés** |Pas encore pris en charge|
| **Journaux de diagnostic** |Pas encore pris en charge|
| **Explorateur de système de fichiers** | Prise en charge limitée |
| **Stockage immuable** |Pas encore pris en charge <br><br>Stockage immuable donne la possibilité de stocker des données dans un [WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) état.|
| **Niveaux de niveau objet** |Pas encore pris en charge <br><br>Exemple : Premium, les niveaux chaud, froid et Archive.|
| **Prise en charge de PowerShell et CLI** | Fonctionnalités limitées <br><br>Vous pouvez créer un compte à l’aide de Powershell ou l’interface CLI. Impossible d’effectuer des opérations ou de définir des listes de contrôle d’accès sur les systèmes de fichiers, répertoires et fichiers.|
| **Sites Web statiques** |Pas encore pris en charge <br><br>Plus précisément, la possibilité de délivrer des fichiers à [sites Web statiques](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Les applications tierces** | Prise en charge limitée <br><br>Les applications tierces qui utilisent l’API REST pour travailler continue de fonctionner si vous les utilisez avec Data Lake Storage Gen2. <br>Si vous avez une application qui utilise les API d’objet Blob, cette application sera très probablement des problèmes si vous utilisez cette application avec Data Lake Storage Gen2. Pour plus d’informations, consultez le [API sont désactivées pour les comptes de stockage Data Lake Storage Gen2 de stockage d’objets Blob](#blob-apis-disabled) section de cet article.|
| **Fonctionnalités de contrôle de version** |Pas encore pris en charge <br><br>Cela inclut les [instantanés](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) et [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

