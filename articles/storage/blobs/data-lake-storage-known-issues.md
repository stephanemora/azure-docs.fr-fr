---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les limitations et les problèmes connus avec Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: cbd58c0873a4a46d175c6d7cbdf2d004da304c06
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247236"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article contient les problèmes connus et les limitations temporaires avec Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Interopérabilité d’API

Les API Stockage Blob et Azure Data Lake Gen2 APIs ne sont pas interopérables.

Si vous devez utiliser le même outil pour travailler avec tout le contenu que vous chargez dans votre compte, alors n’activez pas les espaces de noms hiérarchiques sur votre compte de stockage Blob jusqu’à ce que ces API deviennent interopérables. L’utilisation d’un compte de stockage sans l’espace de noms hiérarchique signifie que vous n’avez alors pas accès aux fonctionnalités spécifiques de Data Lake Storage Gen2, comme les listes de contrôle d’accès au répertoire et au système de fichiers.

## <a name="blob-storage-apis"></a>API Stockage Blob

Les API Stockage Blob ne sont pas encore disponibles pour les comptes Azure Data Lake Storage Gen2.

Ces API sont désactivées pour éviter les problèmes d’accès accidentel aux données qui peuvent se produire, car les API Stockage Blob ne sont pas encore interopérables avec les API Azure Data Lake Gen2.

Si vous avez utilisé ces API pour charger des données avant leur désactivation, et que pour la production vous avez besoin d’accéder à ces données, contactez le Support Microsoft en spécifiant les informations suivantes :

* ID d’abonnement (le GUID, pas le nom)

* Noms des comptes de stockage

* Si vous êtes impacté activement en production et, dans ce cas, pour quels comptes de stockage ?

* Même si vous n’êtes pas activement impacté en production, dites-nous si vous avez besoin que ces données soient copiées vers un autre compte de stockage pour une raison quelconque et, le cas échéant, pourquoi ?

Dans ces circonstances, nous pouvons restaurer l’accès à l’API Blob pour une période limitée afin que vous puissiez copier ces données dans un compte de stockage pour lequel les espaces de noms hiérarchiques sont désactivés.

Les disques de machines virtuelles non managés dépendent des API de Stockage Blob désactivées. Par conséquent, si vous souhaitez activer les espaces de noms hiérarchiques sur un compte de stockage, placez les disques de machines virtuelles non managés dans un compte de stockage pour lequel les espaces de noms hiérarchiques ne sont pas activés.

## <a name="azure-storage-explorer"></a>Explorateur de stockage Azure

Pour afficher ou gérer des comptes Data Lake Storage Gen2 à l’aide de l’Explorateur Stockage Azure, vous devez avoir au moins la version `1.6.0` de l’outil qui est disponible en tant que [téléchargement gratuit](https://azure.microsoft.com/features/storage-explorer/).

Notez qu’actuellement la version de l’Explorateur Stockage qui se trouve dans le portail Azure ne prend pas en charge l’affichage ou la gestion des comptes Data Lake Storage Gen2 avec des espaces de noms hiérarchiques activés.

## <a name="blob-viewing-tool"></a>Outil d’affichage Blob

L’outil d’affichage Blob sur le portail Azure n’offre qu’une prise en charge limitée d’Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Applications tierces

Les applications tierces peuvent ne pas prendre en charge Azure Data Lake Storage Gen2.

La prise en charge est à la discrétion de chaque fournisseur d’application tierce. Actuellement, les API Stockage Blob et Azure Data Lake Storage Gen2 ne peuvent pas être utilisées pour gérer le même contenu. Pendant que nous travaillons en vue d’activer l’interopérabilité, il est possible que de nombreux outils tiers prennent automatiquement en charge Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Prise en charge d’AzCopy

AzCopy version 8 ne prend pas en charge Azure Data Lake Storage Gen2.

À la place, utilisez la dernière préversion d’AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ), car elle prend en charge les points de terminaison Azure Data Lake Storage Gen2.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) ne reçoit pas les événements à partir de comptes Azure Data Lake Gen2, car ces comptes ne les génèrent pas encore.  

## <a name="soft-delete-and-snapshots"></a>Captures instantanées et suppression réversible

Les captures instantanées et la suppression réversible ne sont pas disponibles pour les comptes Azure Data Lake Storage Gen2.

Toutes les fonctionnalités de gestion des versions, notamment les [captures instantanées](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) et la [suppression réversible](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) ne sont pas encore disponibles pour les comptes de stockage pour lesquels les espaces de noms hiérarchiques sont activés.

## <a name="object-level-storage-tiers"></a>Niveaux de stockage au niveau objet

Les niveaux de stockage au niveau objet (chaud, froid et archive) ne sont pas encore disponibles pour les comptes Azure Data Lake Storage Gen 2, mais ils sont disponibles pour les comptes de stockage où les espaces de noms hiérarchiques ne sont pas activés.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Stratégies de gestion (préversion) du cycle de vie du Stockage Blob Azure

Les stratégies de gestion (préversion) du cycle de vie du Stockage Blob Azure ne sont pas encore disponibles pour les comptes Azure Data Lake Storage Gen2.

Ces stratégies sont disponibles pour les comptes de stockage qui où les espaces de noms hiérarchiques ne sont pas activés.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Les journaux de diagnostic ne sont pas disponibles pour les comptes Azure Data Lake Storage Gen2.

Pour demander les journaux de diagnostic, contactez le Support Azure. Fournissez le nom de votre compte et la période de temps pour laquelle vous avez besoin des journaux.
