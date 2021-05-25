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
ms.openlocfilehash: 5bba36eb13405d70379feaa561d258813ab52bec
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489675"
---
# <a name="authorize-access-to-blobs-using-azure-role-assignment-conditions-preview"></a>Autoriser l'accès aux objets blob à l'aide des conditions d'attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d'attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le contrôle d'accès en fonction des attributs (ABAC) est une stratégie d'autorisation qui définit les niveaux d'accès en fonction des attributs associés aux principaux de sécurité, aux ressources, aux requêtes et à l'environnement. Azure ABAC s'appuie sur le contrôle d'accès en fonction du rôle Azure (Azure RBAC) en ajoutant des [conditions aux attributions de rôles Azure](../../role-based-access-control/conditions-overview.md) dans le système de gestion des identités et des accès (IAM) existant. Cette préversion englobe la prise en charge des conditions d'attribution de rôle sur les objets blob et vous permet de créer des conditions d'attribution de rôle en fonction des attributs des ressources et des requêtes.

## <a name="overview-of-conditions-in-azure-storage"></a>Vue d'ensemble des conditions dans le service Stockage Azure

Le service Stockage Azure permet d'[utiliser Azure Active Directory](storage-auth-aad.md) (Azure AD) pour autoriser les requêtes adressées aux services Stockage Blob et Stockage File d'attente. Azure AD autorise l'accès aux ressources sécurisées par le biais du contrôle d'accès en fonction du rôle (RBAC). Le service Stockage Azure définit des [rôles intégrés](../../role-based-access-control/built-in-roles.md#storage) Azure englobant les ensembles communs d'autorisations qui permettent d'accéder aux données blob et aux données de file d'attente. Vous pouvez également définir des rôles personnalisés avec certains ensembles d'autorisations. Le service Stockage Azure prend en charge les attributions de rôles pour les comptes de stockage ou les conteneurs d'objets blob.

Cependant, dans certains cas, vous devrez peut-être accorder un accès plus fin aux ressources de stockage ou simplifier la gestion des centaines d'attributions de rôles d'une ressource de stockage. Dans ce cas, les conditions peuvent être configurées sous forme de [conditions sur les attributions de rôles](../../role-based-access-control/conditions-overview.md) pour [DataActions](../../role-based-access-control/role-definitions.md#dataactions). Les conditions ne sont actuellement pas prises en charge pour les opérations de gestion sur le [fournisseur de ressources de stockage](/rest/api/storagerp).

Vous pouvez utiliser des conditions avec un [rôle personnalisé](../../role-based-access-control/custom-roles.md) ou sélectionner des rôles intégrés. Les rôles personnalisés présentent l'avantage de pouvoir être conçus de façon à ne contenir que les autorisations ou actions de données spécifiques que vous souhaitez accorder à vos utilisateurs. Parmi les rôles intégrés pris en charge dans cette préversion figurent le rôle Contributeur aux données blob du stockage et le rôle Lecteur des données blob du stockage. Si vous utilisez des conditions basées sur des balises d'index de blob, vous devrez peut-être avoir recours au rôle Propriétaire des données blob du stockage dans la mesure où les autorisations relatives aux opérations de balises sont incluses dans ce rôle.

Les conditions relatives au Stockage Azure sont prises en charge pour les objets blob. Cela inclut les objets blob des comptes sur lesquels la fonctionnalité d'espace de noms hiérarchique est activée. Les conditions ne sont actuellement pas prises en charge pour les services Fichiers, Files d'attente et Tables.

## <a name="supported-attributes-and-operations"></a>Attributs et opérations pris en charge

Dans cette préversion, vous pouvez ajouter des conditions aux rôles intégrés pour l'accès aux données blob, notamment pour les rôles [Contributeur aux données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) et [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Si vous utilisez des conditions basées sur des [balises d'index de blob](../blobs/storage-manage-find-blobs.md), vous devrez peut-être avoir recours au rôle [Propriétaire des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans la mesure où les autorisations relatives aux opérations de balises sont incluses dans ce rôle.

Le [format de condition d'attribution de rôle Azure](../../role-based-access-control/conditions-format.md) permet d'utiliser les attributs `@Resource` ou `@Request` dans les conditions. Un attribut `@Resource` fait référence à un attribut existant d'une ressource de stockage à laquelle on accède, comme un compte de stockage, un conteneur ou un objet blob. Un attribut `@Request` fait référence à un attribut inclus dans une requête d'opération de stockage.

Le service Stockage Azure prend en charge un ensemble sélectionné d'attributs de requête ou de ressource qui peuvent être utilisés dans des conditions d'attribution de rôle pour chaque DataAction. Pour obtenir la liste complète des attributs pris en charge pour chaque DataAction, consultez [Actions et attributs pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-attributes.md).

## <a name="see-also"></a>Voir aussi

- [Considérations relatives à la sécurité pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-security.md)
- [Actions et attributs pour les conditions d'attribution de rôle Azure dans le service Stockage Azure (préversion)](storage-auth-abac-attributes.md)
- [Qu'est-ce que le contrôle d'accès en fonction des attributs Azure (Azure ABAC) ? (préversion)](../../role-based-access-control/conditions-overview.md)