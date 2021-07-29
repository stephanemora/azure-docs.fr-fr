---
title: Identités managées
description: La plateforme Media Services peut être utilisée avec des identités managées Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 02f832c7fc25003950d6a112d951c07d1332e08a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454365"
---
# <a name="managed-identities"></a>Identités managées

La gestion des secrets et des informations d’identification pour sécuriser la communication entre les différents services constitue un défi courant pour les développeurs. Sur Azure, les identités managées évitent aux développeurs d’avoir à gérer les informations d’identification en fournissant une identité pour la ressource Azure dans Azure AD et en l’utilisant pour obtenir des jetons Azure Active Directory (Azure AD).

## <a name="media-services-managed-identity-scenarios"></a>Scénarios d’identité managée de la plateforme Media Services

Il existe actuellement trois scénarios où des identités managées peuvent être utilisées avec la plateforme Media Services :

- [Octroi à un compte Media Services d’un accès à Key Vault pour activer les clés gérées par le client](security-encrypt-data-managed-identity-cli-tutorial.md)
- [Octroi à un compte Media Services d’un accès aux comptes de stockage pour permettre à Media Services de contourner les listes de contrôle d’accès réseau Azure Storage](security-access-storage-managed-identity-cli-tutorial.md)
- Permettre à d’autres services (par exemple, des machines virtuelles ou des [Azure Functions](security-function-app-managed-identity-cli-tutorial.md)) d’accéder à Media Services

Dans les deux premiers scénarios, l’identité gérée est utilisée pour accorder au *compte Media Services* l’accès à d’autres services.  Dans le troisième scénario, *le service* a une identité gérée qui est utilisée pour accéder à Media Services.

:::image type="content" source="media/diagrams/managed-identities-scenario-comparison.svg" alt-text="Comparaison des scénarios d’identités gérées":::

> [!NOTE]
> Il est possible de combiner ces scénarios en fonction des besoins. Vous pouvez créer des identités gérées pour le compte Media Services (par exemple, pour accéder aux clés gérées par le client) et la ressource Azure Functions pour accéder à Media Services compte.

## <a name="tutorials-and-how-tos"></a>Tutoriels et articles de procédures

Essayez ces didacticiels pour obtenir une expérience pratique de l’utilisation d’une identité gérée avec Media Services.

- [CLI : chiffrer les données dans un compte Media Services à l’aide d’une clé dans Key Vault](security-encrypt-data-managed-identity-cli-tutorial.md)
- [CLI : autoriser Media Services à accéder à un compte de stockage configuré pour bloquer les demandes provenant d’adresses IP inconnues](security-access-storage-managed-identity-cli-tutorial.md)
- [CLI : accorder à un Function App l’accès au compte Media Services](security-function-app-managed-identity-cli-tutorial.md)
- [PORTAL : Utiliser le portail Azure pour utiliser des clés managées par le client ou BYOK avec la plateforme Media Services](security-customer-managed-keys-portal-tutorial.md)
- [POSTMAN/REST : Utiliser des clés managées par le client ou BYOK avec l’API REST de Media Services](security-customer-managed-keys-rest-postman-tutorial.md)

## <a name="further-reading"></a>Pour aller plus loin

Pour en savoir plus sur ce que les identités managées peuvent faire pour vous et vos applications Azure, consultez [Identités managées Azure AD](../../active-directory/managed-identities-azure-resources/overview.md).

Pour en savoir plus sur Azure Functions, consultez la [À propos d’Azure Functions](../../azure-functions/functions-overview.md)
