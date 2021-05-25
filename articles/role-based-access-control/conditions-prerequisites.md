---
title: Conditions préalables pour les conditions d’attribution de rôle Azure (préversion)
description: Conditions préalables pour les conditions d’attribution de rôle Azure (préversion).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: e0da284f7ce9ac1c8699a6f22f2144ff6f3f8c80
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489646"
---
# <a name="prerequisites-for-azure-role-assignment-conditions-preview"></a>Conditions préalables pour les conditions d’attribution de rôle Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour ajouter ou modifier des conditions d’attribution de rôle Azure, vous devez répondre aux prérequis suivants.

## <a name="storage-accounts"></a>Comptes de stockage

Pour les conditions qui utilisent des balises d’index d’objets blob, vous devez utiliser un compte de stockage compatible avec la fonctionnalité d’index d’objets blob. Par exemple, seuls les comptes de stockage à usage général v2 (GPv2) avec l’espace de noms hiérarchique (HNS) désactivé sont actuellement pris en charge. Pour plus d’informations, consultez [Gérer et rechercher des données Azure Blob à l’aide de balises d’index de blob (préversion)](../storage/blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)

## <a name="azure-powershell"></a>Azure PowerShell

Lorsque vous utilisez Azure PowerShell pour ajouter ou mettre à jour des conditions, vous devez utiliser les versions suivantes :

- [Module Az version 5.5.0 ou ultérieure](https://www.powershellgallery.com/packages/Az/5.5.0)
- [Module Az.Resources version 3.2.1 ou ultérieure](https://www.powershellgallery.com/packages/Az.Resources/3.2.1)
    - Inclus dans le module Az v5.5.0 et versions ultérieures, mais peut être installé manuellement via PowerShell Gallery
- [Module Az.Storage en préversion version 2.5.2-preview ou ultérieure](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview)

## <a name="azure-cli"></a>Azure CLI

Lorsque vous utilisez Azure CLI pour ajouter ou mettre à jour des conditions, vous devez utiliser les versions suivantes :

- [Azure CLI 2.18 ou version ultérieure](/cli/azure/install-azure-cli)

## <a name="permissions"></a>Autorisations

À l’instar des attributions de rôles, pour ajouter ou mettre à jour des conditions, vous devez être connecté à Azure avec un utilisateur disposant des autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, comme [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire](built-in-roles.md#owner).

## <a name="next-steps"></a>Étapes suivantes

- [Exemple de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob en utilisant le portail Azure (préversion)](../storage/common/storage-auth-abac-portal.md)
