---
title: Stockage approuvé pour la plateforme Media Services
description: L’authentification par identités managées permet à la plateforme Media Services d’accéder au compte de stockage configuré avec un pare-feu ou une restriction de réseau virtuel via un accès à un stockage approuvé.
keywords: stockage approuvé, identités managées
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: fd92eed127ec50a3d3a86f667d9aa764b79c190a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585399"
---
# <a name="trusted-storage-for-media-services"></a>Stockage approuvé pour la plateforme Media Services

Lorsque vous créez un compte Media Services, vous devez l’associer à un compte de stockage. La plateforme Media Services peut accéder à ce compte de stockage à l’aide d’une authentification système ou d’une authentification par identité managée. Media Services vérifie que le compte Media Services et le compte de stockage se trouvent dans le même abonnement, et que l’utilisateur qui ajoute l’association a accès au compte de stockage avec le RBAC Azure Resource Manager.

>[!NOTE]
>Le stockage approuvé est uniquement disponible dans l’API et n’est actuellement pas activé dans le portail Azure.

## <a name="trusted-storage-with-a-firewall"></a>Stockage approuvé avec un pare-feu

Toutefois, si vous souhaitez utiliser un pare-feu pour sécuriser votre compte de stockage et activer le stockage approuvé, l’authentification par [identités managées](concept-managed-identities.md) est l’option recommandée. Celle-ci permet à la plateforme Media Services d’accéder au compte de stockage configuré avec un pare-feu ou une restriction de réseau virtuel via un accès à un stockage approuvé.

## <a name="tutorial"></a>Didacticiel

Vous pouvez en savoir plus sur l’activation du stockage approuvé à l’aide du tutoriel [Stockage approuvé Media Services](tutorial-trusted-storage-rest.md).

> [!NOTE]
> Vous devez accorder l’accès Contributeur aux données blob de stockage avec identités managées AMS afin que la plateforme Media Services soit en mesure de lire et d’écrire dans le compte de stockage.  L’octroi du rôle Contributeur générique ne fonctionne pas, car il n’active pas les autorisations appropriées sur le plan de données.

## <a name="further-reading"></a>Pour aller plus loin

Pour comprendre les méthodes de création d’un stockage approuvé avec des identités managées, consultez [Identités managées et plateforme Media Services](concept-managed-identities.md).

Pour plus d’informations sur les services Microsoft approuvés, consultez [Configurer des pare-feux et des réseaux virtuels dans Stockage Azure](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur ce que les identités managées peuvent faire pour vous et vos applications Azure, consultez [Identités managées Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
