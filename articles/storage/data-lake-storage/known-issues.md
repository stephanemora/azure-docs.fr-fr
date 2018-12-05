---
title: Problèmes connus avec Azure Data Lake Storage Gen2 | Microsoft Docs
description: En savoir plus sur les limitations et les problèmes connus avec Azure Data Lake Storage Gen2
services: storage
author: normesta
manager: twooley
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: normesta
ms.openlocfilehash: fa37c23f936173c19f32b76dffab8908176ebd75
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621705"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problèmes connus avec Azure Data Lake Storage Gen2

Cet article contient les problèmes connus et les limitations temporaires avec Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Interopérabilité d’API

Les API Stockage Blob et Azure Data Lake Gen2 APIs ne sont pas interopérables.

Si vous devez utiliser le même outil pour travailler avec tout le contenu que vous chargez dans votre compte, alors n’activez pas les espaces de noms hiérarchiques sur votre compte de stockage Blob jusqu’à ce que ces API deviennent interopérables. L’utilisation d’un compte de stockage sans l’espace de noms hiérarchique signifie que vous n’avez alors pas accès aux fonctionnalités spécifiques de Data Lake Storage Gen2, comme les listes de contrôle d’accès au répertoire et au système de fichiers.

## <a name="blob-storage-apis"></a>API Stockage Blob

Les API Stockage Blob ne sont pas encore disponibles pour les comptes Azure Data Lake Storage Gen 2.

Ces API sont désactivées pour éviter les problèmes d’accès accidentel aux données qui peuvent se produire, car les API Stockage Blob ne sont pas encore interopérables avec les API Azure Data Lake Gen2.

Les disques de machine virtuelle non managée dépendent de ces API, donc si vous souhaitez activer les espaces de noms hiérarchiques sur un compte de stockage, envisagez de placer les disques de machine virtuelle non managée dans un compte de stockage où les espaces de noms hiérarchiques ne sont pas activés.

## <a name="azure-storage-explorer"></a>Explorateur de stockage Azure

Certaines fonctionnalités de l’Explorateur de stockage ne fonctionnent pas encore avec les systèmes de fichiers Azure Data Lake Storage Gen2. Ces limitations s’appliquent à la [version autonome](https://azure.microsoft.com/features/storage-explorer/) de l’Explorateur Stockage Azure, ainsi qu’à la version qui s’affiche dans le portail Azure.

## <a name="blob-viewing-tool"></a>Outil d’affichage Blob

L’outil d’affichage Blob sur le portail Azure n’offre qu’une prise en charge limitée d’Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Applications tierces

Les applications tierces peuvent ne pas prendre en charge Azure Data Lake Storage Gen2.

La prise en charge est à la discrétion de chaque fournisseur d’application tierce. Actuellement, les API Stockage Blob et Azure Data Lake Storage Gen2 ne peuvent pas être utilisées pour gérer le même contenu. Pendant que nous travaillons en vue d’activer l’interopérabilité, il est possible que de nombreux outils tiers prennent automatiquement en charge Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Prise en charge d’AzCopy

AzCopy version 8 ne prend pas en charge Azure Data Lake Storage Gen2.

À la place, utilisez la dernière préversion d’AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ), car elle prend en charge les points de terminaison Azure Data Lake Storage Gen2.

## <a name="oauth-authentication"></a>Authentification OAuth

Les services comme Azure Databricks, HDInsight et Azure Data Factory ne s’intègrent pas encore avec l’authentification de jeton du porteur OAuth d’Azure Active Directory (Azure AD).

## <a name="access-control-lists-acl"></a>Listes de contrôle d’accès (ACL)

Les listes ACL au niveau du répertoire et du fichier sont difficiles à manager. Il n’existe aucun outil basé sur l’interface utilisateur que vous pouvez utiliser pour obtenir et définir ces listes de contrôle d’accès.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) ne reçoit pas les événements à partir de comptes Azure Data Lake Gen2, car ces comptes ne les génèrent pas encore.  

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Vous ne pouvez pas appliquer le contrôle d’accès en fonction du rôle à des objets système de fichiers dans un compte Azure Data Lake Storage Gen2.

## <a name="sql-data-warehouse-polybase"></a>SQL Data Warehouse PolyBase

Lorsque des pare-feux de stockage sont activés sur un compte Stockage Azure, SQL Data Warehouse [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017) ne peut pas accéder à ces comptes.

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
