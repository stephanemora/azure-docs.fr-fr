---
author: mattchenderson
ms.service: app-service-web
ms.topic: include
ms.date: 06/11/2021
ms.author: mahender
ms.openlocfilehash: 9b05a4d16cbf6d3b9677c894bffb6e47018b5cff
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122262762"
---
Les Stockage Blob Azure peuvent être configurés pour [autoriser les requêtes avec Azure AD](../articles/storage/blobs/authorize-access-azure-active-directory.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Cela signifie qu’au lieu de générer une clé SAS avec une expiration, vous pouvez à la place vous fier à l'[identité gérée](../articles/app-service/overview-managed-identity.md) de l’application. Par défaut, l’identité affectée par le système de l’application est utilisée. Si vous souhaitez spécifier une identité affectée par l’utilisateur, vous pouvez définir le `WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID` paramètre d’application sur l’ID de ressource de cette identité. Le paramètre peut également accepter « SystemAssigned » comme valeur, bien qu’il soit identique à l’omission du paramètre.

Pour activer le package à extraire à l’aide de l’identité :

1. Veillez à ce que le blob soit [configuré pour un accès privé](../articles/storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

1. Accordez à l’identité le rôle de[Lecteur des données Blob du stockage](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader) avec étendue sur le blob du package. Voir [Attribuer un rôle Azure pour l’accès aux données d’objet blob](../articles/storage/blobs/assign-azure-role-data-access.md) pour plus d’informations sur la création de l’affectation du rôle.

1. Définissez les `WEBSITE_RUN_FROM_PACKAGE` paramètres d’application à l’URL de l’objet blob du package. Cela aura probablement le format « https://{storage-account-name}. blob. Core. Windows. net/{Container-Name}/{Path-to-package} » ou similaire.