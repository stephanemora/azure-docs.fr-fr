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
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: ec59383f9ca2b71ec9f4b6df3ab2e24c6b52473b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881363"
---
# <a name="tenancy-in-azure-active-directory"></a>Location dans Azure Active Directory

Azure Active Directory (Azure AD) organise des objets comme des utilisateurs et des applications dans des groupes appelés *locataires*. Les locataires permettent à un administrateur de définir des stratégies sur les utilisateurs au sein de l’organisation et les applications appartenant à l’organisation pour répondre à leurs stratégies de sécurité et opérationnelles. 

## <a name="who-can-sign-in-to-your-app"></a>Qui peut se connecter à votre application ?

Lorsqu’il s’agit de développer des applications, les développeurs peuvent choisir de configurer leur application pour être mono-locataire ou multi-locataire lors de l’inscription d’application dans le [portail Azure](https://portal.azure.com).
* Les applications mono-locataires ne sont disponibles que dans le locataire dans lequel elles ont été inscrites, également appelé leur locataire de base.
* Les applications multi-locataires sont accessibles aux utilisateurs dans leur locataire de base et d’autres locataires.

Dans le portail Azure, vous pouvez configurer votre application pour être mono-locataire ou multi-locataire en définissant l’audience comme suit.

| Public visé | Mono/Multi-locataire | Qui peut se connecter | 
|----------|--------| ---------|
| Comptes dans cet annuaire uniquement | Locataire unique | Tous les comptes d’utilisateur et d’invité dans votre annuaire peuvent utiliser votre application ou API.<br>*Utilisez cette option si votre audience cible est interne à votre organisation.* |
| Comptes dans n’importe quel annuaire Azure AD | Multi-locataire | Tous les utilisateurs et invités avec un compte professionnel ou scolaire Microsoft peuvent utiliser votre application ou API. Ceci inclut les établissements scolaires et les entreprises qui utilisent Office 365.<br>*Utilisez cette option si votre audience cible est constituée de clients d’entreprise ou du secteur éducatif.* |
| Comptes dans n’importe quel annuaire Azure AD et des comptes Microsoft personnels (tels que Skype, Xbox, Outlook.com) | Multi-locataire | Tous les utilisateurs avec un compte professionnel ou scolaire, ou un compte personnel Microsoft, peuvent utiliser votre application ou API. Ceci inclut les établissements scolaires et les entreprises qui utilisent Office 365, ainsi que les comptes personnels utilisés pour se connecter à des services tels que Xbox et Skype.<br>*Utilisez cette option pour cibler l’ensemble plus large de comptes Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Meilleures pratiques pour les applications multi-locataires

La création de formidables applications multi-locataires peut s’avérer difficile en raison du nombre de stratégies différentes que les administrateurs informatiques peuvent définir dans leurs locataires. Si vous choisissez de créer une application multi-locataire, suivez ces meilleures pratiques :

* Testez votre application dans un locataire dans lequel des [stratégies d’accès conditionnel](../azuread-dev/conditional-access-dev-guide.md) sont configurées.
* Suivez le principe de moindre accès utilisateur pour vous assurer que votre application demande uniquement des autorisations dont elle a réellement besoin. Évitez de demander des autorisations qui nécessitent le consentement de l’administrateur car ceci peut empêcher les utilisateurs d’acquérir votre application dans certaines organisations. 
* Fournissez les noms et descriptions appropriés de toutes les autorisations que vous exposez dans le cadre de votre application. Cela permet aux utilisateurs et administrateurs de savoir ce qui sont autorisés à faire lorsqu’ils tentent d’utiliser les API de votre application. Pour plus d’informations, consultez la section des meilleures pratiques dans le [guide des autorisations](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Étapes suivantes

* [Comment convertir une application multi-locataire](howto-convert-app-to-be-multi-tenant.md)
