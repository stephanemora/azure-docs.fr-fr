---
title: Guide d’attribution de licences pour B2B Collaboration - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B Collaboration ne nécessite pas de licences Azure AD payées, mais vous pouvez également obtenir des fonctionnalités payantes pour les utilisateurs invités B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d02160163da4081ad8adbe233b27fee970a0df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87907174"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guide d’attribution de licences pour Azure Active Directory B2B Collaboration

Avec la collaboration business-to-business (B2B) Azure Active Directory (Azure AD), vous pouvez inviter des utilisateurs externes (ou « utilisateurs invités ») à utiliser vos services Azure AD payants. Certaines fonctionnalités sont gratuites, mais pour les fonctionnalités Azure AD payantes, vous pouvez inviter jusqu’à cinq utilisateurs invités pour la licence de chaque édition d’Azure AD que vous possédez pour un employé ou un utilisateur non invité de votre locataire.

> [!NOTE]
> Pour plus d’informations sur les tarifs d’Azure AD et les fonctionnalités de collaboration B2B, consultez [Tarifs d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

L’attribution de licences aux utilisateurs invités B2B est automatiquement calculée et signalée d’après un ratio de 1 pour 5. 

De plus, les utilisateurs invités peuvent utiliser des fonctionnalités Azure AD gratuites sans avoir besoin de licences supplémentaires. Les utilisateurs invités ont accès à des fonctionnalités Azure AD gratuites même si vous n’avez acheté aucune licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemples : Calculer le nombre de licences d’utilisateur invité
Une fois que vous avez déterminé le nombre d’utilisateurs invités qui a besoin d’accéder à vos services Azure AD payants, assurez-vous que vous avez suffisamment de licences Azure AD payantes pour couvrir les utilisateurs invités selon un ratio de 1 à 5. Voici quelques exemples :

- Vous souhaitez permettre à 100 utilisateurs invités d’utiliser vos applications ou services Azure AD et fournir la gestion et le provisionnement des accès. Vous souhaitez également imposer à 50 d’entre eux une authentification multifacteur et un accès conditionnel, et vous avez besoin de 10 licences Azure AD Premium P1 pour ces fonctionnalités. Si vous envisagez d’utiliser des fonctionnalités de protection de l’identité avec vos utilisateurs invités, vous avez besoin de licences Azure AD Premium P2 pour couvrir les utilisateurs invités et ce toujours selon un ratio de 1 à 5.
- Pour inviter 60 utilisateurs et leur imposer une authentification multifacteur, vous devez donc disposer d’au moins 12 licences Azure AD Premium P1. Vous avez 10 employés détenant des licences Azure AD Premium P1, ce qui autorise 50 utilisateurs invités au maximum selon un ratio de 1 sur 5. Vous devez acheter deux licences Premium P1 supplémentaires afin de couvrir 10 utilisateurs invités supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la collaboration Azure AD B2B :

* [Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](faq.md)
