---
title: Applications mono-locataires ou multi-locataires dans Azure AD
titleSuffix: Microsoft identity platform
description: Découvrez les fonctionnalités et les différences entre les applications mono-locataires et multi-locataires dans Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/13/2021
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 9a21d6f4a59709c74a02dff2334402510e156918
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987724"
---
# <a name="tenancy-in-azure-active-directory"></a>Location dans Azure Active Directory

Azure Active Directory (Azure AD) organise des objets comme des utilisateurs et des applications dans des groupes appelés _locataires_. Les locataires permettent à un administrateur de définir des stratégies sur les utilisateurs au sein de l’organisation et les applications appartenant à l’organisation pour répondre à leurs stratégies de sécurité et opérationnelles.

## <a name="who-can-sign-in-to-your-app"></a>Qui peut se connecter à votre application ?

Lorsqu’il s’agit de développer des applications, les développeurs peuvent choisir de configurer leur application pour être mono-locataire ou multi-locataire lors de l’inscription d’application dans le [portail Azure](https://portal.azure.com).

- Les applications mono-locataires ne sont disponibles que dans le locataire dans lequel elles ont été inscrites, également appelé leur locataire de base.
- Les applications multi-locataires sont accessibles aux utilisateurs dans leur locataire de base et d’autres locataires.

Dans le portail Azure, vous pouvez configurer votre application pour être mono-locataire ou multi-locataire en définissant l’audience comme suit.

| Public visé                                                                                              | Mono/Multi-locataire | Qui peut se connecter                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Comptes dans cet annuaire uniquement                                                                       | Locataire unique       | Tous les comptes d’utilisateur et d’invité dans votre annuaire peuvent utiliser votre application ou API.<br>_Utilisez cette option si votre audience cible est interne à votre organisation._                                                                                                                                                         |
| Comptes dans n’importe quel annuaire Azure AD                                                                    | Multi-locataire        | Tous les utilisateurs et invités avec un compte professionnel ou scolaire Microsoft peuvent utiliser votre application ou API. Cela inclut les établissements scolaires et les entreprises qui utilisent Microsoft 365.<br>_Utilisez cette option si votre audience cible est constituée de clients d’entreprise ou du secteur éducatif._                                                                    |
| Comptes dans n’importe quel annuaire Azure AD et des comptes Microsoft personnels (tels que Skype, Xbox, Outlook.com) | Multi-locataire        | Tous les utilisateurs avec un compte professionnel ou scolaire, ou un compte personnel Microsoft, peuvent utiliser votre application ou API. Cela inclut les établissements scolaires et les entreprises qui utilisent Microsoft 365, ainsi que les comptes personnels utilisés pour se connecter à des services tels que Xbox et Skype.<br>_Utilisez cette option pour cibler l’ensemble plus large de comptes Microsoft._ |

## <a name="best-practices-for-multi-tenant-apps"></a>Meilleures pratiques pour les applications multi-locataires

La création de formidables applications multi-locataires peut s’avérer difficile en raison du nombre de stratégies différentes que les administrateurs informatiques peuvent définir dans leurs locataires. Si vous choisissez de créer une application multi-locataire, suivez ces meilleures pratiques :

- Testez votre application dans un locataire dans lequel des [stratégies d’accès conditionnel](../azuread-dev/conditional-access-dev-guide.md) sont configurées.
- Suivez le principe de moindre accès utilisateur pour vous assurer que votre application demande uniquement des autorisations dont elle a réellement besoin.
- Fournissez les noms et descriptions appropriés de toutes les autorisations que vous exposez dans le cadre de votre application. Cela permet aux utilisateurs et administrateurs de savoir ce qu’ils sont autorisés à faire quand ils tentent d’utiliser les API de votre application. Pour plus d’informations, consultez la section des meilleures pratiques dans le [guide des autorisations](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les locataires dans Azure AD, consultez :

- [Comment convertir une application multi-locataire](howto-convert-app-to-be-multi-tenant.md)
- [Activer les connexions multilocataires](howto-convert-app-to-be-multi-tenant.md)
