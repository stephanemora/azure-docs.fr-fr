---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les limitations et les problèmes connus avec Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: 51230fe050c67253dd5b2bb3f23d03512df64ef6
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939244"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article contient des problèmes connus et limitations temporaires avec Data Lake Storage Gen2.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>Prise en charge du Kit de développement logiciel pour les comptes Data Lake Storage Gen2

Il n’existe pas de kits de développement logiciel disponibles qui fonctionnent avec les comptes Data Lake Storage Gen2.

## <a name="blob-storage-apis"></a>API Stockage Blob

API de stockage BLOB ne sont pas encore disponibles pour les comptes Data Lake Storage Gen2.

Ces API sont désactivées pour éviter les problèmes d’accès accidentel aux données qui peuvent se produire, car les API Stockage Blob ne sont pas encore interopérables avec les API Azure Data Lake Gen2.

Si vous avez utilisé ces API pour charger des données avant leur désactivation, et que pour la production vous avez besoin d’accéder à ces données, contactez le Support Microsoft en spécifiant les informations suivantes :

* ID d’abonnement (le GUID, pas le nom)

* Noms des comptes de stockage

* Si vous êtes impacté activement en production et, dans ce cas, pour quels comptes de stockage ?

* Même si vous n’êtes pas activement impacté en production, dites-nous si vous avez besoin que ces données soient copiées vers un autre compte de stockage pour une raison quelconque et, le cas échéant, pourquoi ?

Dans ces circonstances, nous pouvons restaurer l’accès à l’API Blob pour une période limitée afin que vous puissiez copier ces données dans un compte de stockage pour lequel les espaces de noms hiérarchiques sont désactivés.

Les disques de machines virtuelles non managés dépendent des API de Stockage Blob désactivées. Par conséquent, si vous souhaitez activer un espace de noms hiérarchique sur un compte de stockage, placez les disques de machines virtuelles non managés dans un compte de stockage pour lequel la fonctionnalité d'espace de noms hiérarchique n'est pas activée.

## <a name="api-interoperability"></a>Interopérabilité d’API

Les API Stockage Blob et Azure Data Lake Gen2 APIs ne sont pas interopérables.

Si vous disposez d’outils, d’applications, de services ou de scripts qui utilisent des API Blob et que vous souhaitez les utiliser pour travailler avec tout le contenu que vous téléchargez sur votre compte, n’activez pas les espaces de noms hiérarchiques sur votre compte de stockage Blob avant que les API Blob deviennent interopérables avec les API Azure Data Lake Gen2. À l’aide d’un compte de stockage sans espace de noms hiérarchique signifie que vous n’avez alors pas accès à Data Lake Storage Gen2 des fonctionnalités spécifiques, telles que des listes de contrôle d’accès système répertoire et fichier.

## <a name="azure-storage-explorer"></a>Explorateur de stockage Azure

Pour afficher ou gérer des comptes Data Lake Storage Gen2 à l’aide de l’Explorateur Stockage Azure, vous devez avoir au moins la version `1.6.0` de l’outil qui est disponible en tant que [téléchargement gratuit](https://azure.microsoft.com/features/storage-explorer/).

Notez que la version de l’Explorateur de stockage qui est incorporée dans le portail Azure ne prend pas en prendre en charge l’affichage ou la gestion des comptes Data Lake Storage Gen2 avec la fonctionnalité de l’espace de noms hiérarchique activée.

## <a name="blob-viewing-tool"></a>Outil d’affichage Blob

Objet BLOB de l’outil d’affichage sur le portail Azure prend uniquement en charge limitée pour le stockage Data Lake Gen2.

## <a name="third-party-applications"></a>Applications tierces

Les applications tierces ne peuvent pas en charge le stockage Data Lake Gen2.

La prise en charge est à la discrétion de chaque fournisseur d’application tierce. Actuellement, les API de stockage de Blob et Data Lake Storage Gen2 API ne peut pas être utilisé pour gérer le même contenu. Pendant que nous travaillons pour activer l’interopérabilité, il est possible que de nombreux outils tiers seront automatiquement prennent en charge le stockage Data Lake Gen2.

## <a name="azcopy-support"></a>Prise en charge d’AzCopy

AzCopy version 8 ne prend pas en charge le stockage Data Lake Gen2.

Au lieu de cela, utilisez la dernière version de la version préliminaire d’AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) comme il prend en charge les points de terminaison Data Lake Storage Gen2.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) ne reçoit pas les événements à partir de comptes Azure Data Lake Gen2, car ces comptes ne les génèrent pas encore.  

## <a name="soft-delete-and-snapshots"></a>Captures instantanées et suppression réversible

Captures instantanées et suppression réversible ne sont pas disponibles pour les comptes Data Lake Storage Gen2.

Toutes les fonctionnalités de gestion des versions, notamment les [captures instantanées](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) et la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) ne sont pas encore disponibles pour les comptes de stockage pour lesquels la fonctionnalité d'espace de noms hiérarchique est activée.

## <a name="object-level-storage-tiers"></a>Niveaux de stockage au niveau objet

Les niveaux de stockage au niveau objet (chaud, froid et archive) ne sont pas encore disponibles pour les comptes Azure Data Lake Storage Gen 2, mais ils sont disponibles pour les comptes de stockage où la fonctionnalité d'espace de noms hiérarchique n'est pas activée.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Stratégies de gestion du cycle de vie du Stockage Blob Azure

Stratégies de gestion de cycle de vie du stockage d’objets Blob Azure ne sont pas encore disponibles pour les comptes Data Lake Storage Gen2.

Ces stratégies sont disponibles pour les comptes de stockage dont la fonctionnalité d'espace de noms hiérarchique n'est pas activée.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Journaux de diagnostic ne sont pas disponibles pour les comptes Data Lake Storage Gen2.
