---
title: Accès d’indexeur au service Stockage Azure à l’aide d’une exception de service approuvée
titleSuffix: Azure Cognitive Search
description: Autorisez un accès aux données stockées de manière sécurisée dans le service Stockage Azure via un indexeur dans le service Recherche cognitive Azure.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2021
ms.openlocfilehash: db614d99a3c25e6f782c0be6bea95ec82124b8e5
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554837"
---
# <a name="indexer-access-to-azure-storage-using-the-trusted-service-exception-azure-cognitive-search"></a>Accès d’indexeur au service Stockage Azure à l’aide de l’exception de service approuvé (Recherche cognitive Azure)

Les indexeurs d’un service Recherche cognitive Azure qui accèdent aux données dans des comptes Stockage Azure peuvent utiliser la capacité d’[exception de service approuvé](../storage/common/storage-network-security.md#exceptions) pour accéder aux données de manière sécurisée. Ce mécanisme offre aux clients dans l’impossibilité d’accorder un [accès aux indexeurs en utilisant des règles de pare-feu IP](search-indexer-howto-access-ip-restricted.md) une alternative simple, sécurisée et gratuite pour accéder aux données de comptes de stockage.

> [!NOTE]
> La prise en charge de l’accès aux données dans les comptes de stockage par le biais d’une exception de service approuvé est limitée aux solutions Stockage Blob Azure et Azure Data Lake Storage Gen2. Le Stockage Table Azure n’est pas pris en charge.

## <a name="step-1-configure-a-connection-using-a-managed-identity"></a>Étape 1 : Configurer une connexion à l’aide d’une identité managée

Suivez les instructions de la page [Configurer une connexion à un compte Stockage Azure à l’aide d’une identité managée](search-howto-managed-identities-storage.md). Lorsque vous avez terminé, vous aurez inscrit votre service de recherche auprès de Azure Active Directory en tant que service approuvé, et vous aurez accordé des autorisations dans le stockage Azure qui donne à l’identité de recherche des droits spécifiques pour accéder aux données ou aux informations.

> [!NOTE]
> Les instructions vous guident dans une approche utilisant le portail pour configurer Recherche cognitive en tant que service approuvé. Pour réaliser cette opération dans le code, vous pouvez utiliser l’[API REST](/rest/api/searchmanagement/services/createorupdate), [Azure PowerShell](search-manage-powershell.md#create-a-service-with-a-system-assigned-managed-identity) ou [Azure CLI](search-manage-azure-cli.md#create-a-service-with-a-system-assigned-managed-identity).

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Étape 2 : Autoriser les services Microsoft approuvés à accéder au compte de stockage

Sur le portail Azure, accédez à l’onglet **Pare-feu et réseaux virtuels** du compte de stockage. Vérifiez que l’option **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** est cochée. Avec cette option, seule l’instance du service de recherche spécifique dotée de l’accès en fonction du rôle approprié au compte de stockage (authentification forte) est autorisée à accéder aux données du compte de stockage, même s’il est sécurisé par des règles de pare-feu IP.

![Exception de service approuvé](media\search-indexer-howto-secure-access\exception.png "Exception de service approuvé")

Les indexeurs pourront désormais accéder aux données du compte de stockage, même si celui-ci est sécurisé par des règles de pare-feu IP.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les indexeurs de Stockage Azure :

- [Indexeur de blobs Azure](search-howto-indexing-azure-blob-storage.md)
- [Indexeur Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexeur de tables Azure](search-howto-indexing-azure-tables.md)