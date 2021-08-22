---
title: Identités managées avec Azure Video Analyzer
description: Cette rubrique explique comment utiliser des identités managées avec Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 073f9ee8d039435b4cf8c7dac59831a2870b2ce0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601415"
---
# <a name="managed-identity"></a>Identité managée

La gestion des secrets et des informations d’identification pour sécuriser la communication entre les différents services constitue un défi courant pour les développeurs. Sur Azure, les identités managées évitent aux développeurs d’avoir à gérer les informations d’identification en fournissant une identité pour la ressource Azure dans Azure Active Directory (Azure AD) et en l’utilisant pour obtenir des jetons Azure AD.

Lorsque vous créez un compte Azure Video Analyzer, vous devez y associer un compte de stockage Azure. Si vous utilisez Video Analyzer pour enregistrer la vidéo en direct à partir d’une caméra, ces données sont stockées en tant que blobs dans un conteneur dans le compte de stockage. Vous devez utiliser une identité managée pour accorder au compte Video Analyzer l’accès approprié au compte de stockage, comme suit.


## <a name="user-assigned-managed-identity-for-video-analyzer"></a>Identité managée affectée par l’utilisateur pour Video Analyzer

1. Créer une [identité managée affectée par l’utilisateur](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

1. Créer un compte de stockage Azure

   [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

1. Ajoutez à l’identité managée les rôles [Contributeur des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) et [Lecteur](../../role-based-access-control/built-in-roles.md#reader) pour le compte de stockage ci-dessus.

1. Créez le compte de Video Analyzer, en fournissant l’identité managée affectée par l’utilisateur et le compte de stockage en tant que valeurs aux propriétés appropriées.

Video Analyzer peut alors accéder au compte de stockage en votre nom en utilisant l’identité managée.

Pour voir un exemple d’utilisation du portail Azure pour accomplir l’opération ci-dessus, consultez [cet](create-video-analyzer-account.md) article.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur ce que les identités managées peuvent faire pour vous et vos applications Azure, consultez [Identités managées Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
