---
title: Collaboration B2B licences aide - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B Collaboration ne nécessite pas de licences Azure AD payées, mais vous pouvez également obtenir des fonctionnalités payantes pour les utilisateurs invités B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fa01a6bf522061e54e9622cb9201f81c699a8ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412435"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guide d’attribution de licences pour Azure Active Directory B2B Collaboration

Avec la collaboration business-to-business (B2B) Azure Active Directory (Azure AD), vous pouvez inviter des utilisateurs externes (ou « utilisateurs invités ») à utiliser vos services Azure AD payants. Pour chaque licence Azure AD payante que vous attribuez à un utilisateur, vous pouvez inviter jusqu’à cinq utilisateurs à l’aide d’une allocation d’utilisateur externe.

L’attribution de licences aux utilisateurs invités B2B est automatiquement calculée et signalée d’après un ratio de 1 pour 5. Actuellement, il n’est pas possible d’attribuer directement des licences d’utilisateur invité B2B à des utilisateurs invités.

De plus, les utilisateurs invités peuvent utiliser des fonctionnalités Azure AD gratuites sans avoir besoin de licences supplémentaires. Les utilisateurs invités ont accès à des fonctionnalités Azure AD gratuites même si vous n’avez acheté aucune licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemples : Calculer le nombre de licences d’utilisateur invité
Une fois que vous avez déterminé le nombre d’utilisateurs invités qui a besoin d’accéder à vos services Azure AD payants, assurez-vous que vous avez suffisamment de licences Azure AD payantes pour couvrir les utilisateurs invités selon un ratio de 1 à 5. Voici quelques exemples :

- Vous souhaitez permettre à 100 utilisateurs invités d’utiliser vos applications et services Azure AD et vous voulez leur accorder une gestion et un approvisionnement des accès. Vous souhaitez également imposer à 50 d’entre eux une authentification multifacteur et un accès conditionnel. Pour prendre en charge cette combinaison, vous avez besoin de 10 licences Azure AD Basic et de 10 licences Azure AD Premium P1. Si vous envisagez d’utiliser des fonctionnalités de protection de l’identité avec vos utilisateurs invités, vous avez besoin de licences Azure AD Premium P2 pour couvrir les utilisateurs invités et ce toujours selon un ratio de 1 à 5.
- Pour inviter 60 utilisateurs et leur imposer une authentification multifacteur, vous devez donc disposer d’au moins 12 licences Azure AD Premium P1. Vous avez 10 employés détenant des licences Azure AD Premium P1, ce qui autorise 50 utilisateurs invités au maximum selon un ratio de 1 sur 5. Vous devez acheter deux licences Premium P1 supplémentaires afin de couvrir 10 utilisateurs invités supplémentaires.

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la collaboration Azure AD B2B :

* [Tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](faq.md)
