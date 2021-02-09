---
title: Identités managées
description: La plateforme Media Services peut être utilisée avec des identités managées Azure.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 71a2b8f0734de80f71dbb2372f8600b464d6c606
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258437"
---
# <a name="managed-identities"></a>Identités managées

La gestion des secrets et des informations d’identification pour sécuriser la communication entre les différents services constitue un défi courant pour les développeurs. Sur Azure, les identités managées évitent aux développeurs d’avoir à gérer les informations d’identification en fournissant une identité pour la ressource Azure dans Azure AD et en l’utilisant pour obtenir des jetons Azure Active Directory (Azure AD).

Il existe actuellement deux scénarios où des identités managées peuvent être utilisées avec la plateforme Media Services :

- utiliser l’identité managée du compte Media Services pour accéder à des comptes de stockage ;

- utiliser l’identité managée du compte Media Services pour accéder au Key Vault afin d’accéder à des clés client.

Les deux sections suivantes décrivent les étapes des deux scénarios.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Utiliser l’identité managée du compte Media Services pour accéder à des comptes de stockage

1. Créez un compte Media Services avec une identité managée.
1. Accordez au principal de l’identité managée l’accès à un compte de stockage que vous possédez.
1. La plateforme Media Services peut alors accéder au compte de stockage en votre nom à l’aide de l’identité managée.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Utiliser l’identité managée du compte Media Services pour accéder au Key Vault afin d’accéder à des clés client

1. Créez un compte Media Services avec une identité managée.
1. Accordez au principal de l’identité managée l’accès à un Key Vault que vous possédez.
1. Configurez le compte Media Services pour utiliser un chiffrement de compte basé sur une clé client.
1. La plateforme Media Services accède au Key Vault en votre nom à l’aide de l’identité managée.

Pour plus d’informations sur les clés managées par le client et le Key Vault, consultez [Utiliser des clés BYOK (clé gérées par le client) avec Media Services](concept-use-customer-managed-keys-byok.md).

## <a name="tutorials"></a>Tutoriels

Ces tutoriels incluent les deux scénarios mentionnés ci-dessus.

- [Utiliser le portail Azure pour utiliser des clés managées par le client ou BYOK avec la plateforme Media Services](tutorial-byok-portal.md)
- [Utiliser des clés managées par le client ou BYOK avec l’API REST Media Services](tutorial-byok-postman.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur ce que les identités managées peuvent faire pour vous et vos applications Azure, consultez [Identités managées Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).
