---
title: Autoriser l’accès au stockage via une exception de service approuvé
titleSuffix: Azure Cognitive Search
description: Guide pratique qui explique comment configurer une exception de service approuvé pour accéder aux données des comptes de stockage de manière sécurisée.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 4fbffaa7bc68bb32bd07b657f4b769e5af4302bf
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950022"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>Accès aux données dans les comptes de stockage de manière sécurisée via une exception de service approuvé

Les indexeurs qui accèdent aux données de comptes de stockage peuvent utiliser la capacité d’[exception de service approuvé](../storage/common/storage-network-security.md#exceptions) pour accéder aux données de manière sécurisée. Ce mécanisme offre aux clients dans l’impossibilité d’accorder un [accès aux indexeurs via des règles de pare-feu IP](search-indexer-howto-access-ip-restricted.md) une alternative simple, sécurisée et gratuite pour accéder aux données de comptes de stockage.

> [!NOTE]
> La prise en charge de l’accès aux données dans les comptes de stockage par le biais d’une exception de service approuvé est limitée aux solutions Stockage Blob Azure et Azure Data Lake Storage Gen2. Le stockage Table Azure n’est pas pris en charge.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>Étape 1 : Configurer une connexion au compte de stockage via l’identité

Suivez les détails décrits dans le [guide d’accès des identités managées](search-howto-managed-identities-storage.md) pour configurer des indexeurs en vue d’accéder aux comptes de stockage via l’identité managée du service de recherche.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>Étape 2 : Autoriser les services Microsoft approuvés à accéder au compte de stockage

Sur le portail Azure, accédez à l’onglet « Pare-feu et réseaux virtuels » du compte de stockage. Vérifiez que l’option « Autoriser les services Microsoft approuvés à accéder à ce compte de stockage » est cochée. Avec cette option, seule l’instance du service de recherche spécifique dotée de l’accès en fonction du rôle approprié au compte de stockage (authentification forte) est autorisée à accéder aux données du compte de stockage, même s’il est sécurisé par des règles de pare-feu IP.

![Exception de service approuvé](media\search-indexer-howto-secure-access\exception.png "Exception de service approuvé")

Les indexeurs pourront désormais accéder aux données du compte de stockage, même si celui-ci est sécurisé par des règles de pare-feu IP.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les indexeurs de Stockage Azure :

- [Indexeur de blobs Azure](search-howto-indexing-azure-blob-storage.md)
- [Indexeur Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
- [Indexeur de tables Azure](search-howto-indexing-azure-tables.md)