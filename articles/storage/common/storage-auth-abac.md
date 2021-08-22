---
title: Autoriser l'accès aux objets blob à l'aide des conditions d'attribution de rôle Azure (préversion)
titleSuffix: Azure Storage
description: Autorisez l'accès aux objets blob Azure à l'aide des conditions d'attribution de rôle Azure et du contrôle d'accès en fonction des attributs Azure (Azure ABAC). Définissez les conditions sur les attributions de rôle à l'aide des attributs de stockage.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 355eaa0c7e907dea1e1fe88e0640d3b09463fa09
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113729017"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Autoriser l'accès aux objets blob à l'aide des conditions d'attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d'attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le contrôle d'accès en fonction des attributs (ABAC) est une stratégie d'autorisation qui définit les niveaux d'accès en fonction des attributs associés aux principaux de sécurité, aux ressources, aux requêtes et à l'environnement. Azure ABAC s'appuie sur le contrôle d'accès en fonction du rôle Azure (Azure RBAC) en ajoutant des [conditions aux attributions de rôles Azure](../../role-based-access-control/conditions-overview.md) dans le système de gestion des identités et des accès (IAM) existant. Cette préversion prend en charge les conditions d’attribution de rôle sur les objets blob et Data Lake Storage Gen2. Il vous permet de créer des conditions d’attribution de rôle en fonction des attributs de ressource et de requête.

## <a name="overview-of-conditions-in-azure-storage"></a>Vue d'ensemble des conditions dans le service Stockage Azure

Le service Stockage Azure permet d'[utiliser Azure Active Directory](authorize-data-access.md) (Azure AD) pour autoriser les requêtes adressées aux services Stockage Blob et Stockage File d'attente. Azure AD autorise les droits d’accès aux ressources sécurisées à l’aide d’Azure RBAC. Le service Stockage Azure définit des [rôles intégrés](../../role-based-access-control/built-in-roles.md#storage) Azure englobant les ensembles communs d'autorisations qui permettent d'accéder aux données blob et aux données de file d'attente. Vous pouvez également définir des rôles personnalisés avec certains ensembles d'autorisations. Le service Stockage Azure prend en charge les attributions de rôles pour les comptes de stockage ou les conteneurs d'objets blob.

Cependant, dans certains cas, vous devrez peut-être accorder un accès plus fin aux ressources de stockage ou simplifier les centaines d’attributions de rôles d’une ressource de stockage. Vous pouvez configurer des [conditions sur les attributions de rôles](../../role-based-access-control/conditions-overview.md) pour [DataActions](../../role-based-access-control/role-definitions.md#dataactions) afin d’atteindre ces objectifs. Vous pouvez utiliser des conditions avec un [rôle personnalisé](../../role-based-access-control/custom-roles.md) ou sélectionner des rôles intégrés. Notez que les conditions ne sont pas prises en charge pour les [Actions](../../role-based-access-control/role-definitions.md#actions) de gestion via le [Fournisseur de ressources de stockage](/rest/api/storagerp).

Les conditions relatives au Stockage Azure sont prises en charge pour les objets blob. Vous pouvez utiliser des conditions avec les comptes pour lesquels la fonctionnalité d’[espace de noms hiérarchique](../blobs/data-lake-storage-namespace.md) (HNS) est activée. Les conditions ne sont actuellement pas prises en charge pour les services Fichiers, Files d'attente et Tables.

## <a name="supported-attributes-and-operations"></a>Attributs et opérations pris en charge

Dans cette préversion, vous pouvez ajouter des conditions aux rôles intégrés ou personnalisés. L’utilisation de rôles personnalisés vous permet d’accorder uniquement les autorisations ou les actions de données essentielles à vos utilisateurs. Parmi les rôles intégrés pris en charge dans cette préversion figurent les rôles [Lecteur de données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader), [Contributeur aux données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) et [Propriétaire des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner).

Si vous utilisez des conditions basées sur des [balises d’index de blob](../blobs/storage-manage-find-blobs.md), vous devrez avoir recours au rôle *Propriétaire des données blob du stockage* dans la mesure où les autorisations relatives aux opérations de balises sont incluses dans ce rôle.

> [!NOTE]
> Les balises d’index d’objet blob ne sont pas prises en charge pour les comptes de stockage Data Lake Storage Gen2, qui utilisent un espace de noms hiérarchique. Vous ne devez pas créer de conditions d’affectation de rôles à l’aide de balises d’index sur les comptes de stockage pour lesquels HNS est activé.

Le [format de condition d'attribution de rôle Azure](../../role-based-access-control/conditions-format.md) permet d'utiliser les attributs `@Resource` ou `@Request` dans les conditions. Un attribut `@Resource` fait référence à un attribut existant d'une ressource de stockage à laquelle on accède, comme un compte de stockage, un conteneur ou un objet blob. Un attribut `@Request` fait référence à un attribut inclus dans une requête d'opération de stockage.

Pour obtenir la liste complète des attributs pris en charge pour chaque DataAction, consultez [Actions et attributs pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-attributes.md).

## <a name="see-also"></a>Voir aussi

- [Considérations relatives à la sécurité pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-security.md)
- [Actions et attributs pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Qu'est-ce que le contrôle d'accès en fonction des attributs Azure (Azure ABAC) ? (préversion)](../../role-based-access-control/conditions-overview.md)