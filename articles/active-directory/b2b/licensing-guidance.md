---
title: Guide d’attribution de licences pour Azure Active Directory B2B Collaboration | Microsoft Docs
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
ms.openlocfilehash: e876c720e9cac4f05fd0776a3aba24976720fb15
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075837"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guide d’attribution de licences pour Azure Active Directory B2B Collaboration

Avec la collaboration business-to-business (B2B) Azure Active Directory (Azure AD), vous pouvez inviter des utilisateurs externes (ou « utilisateurs invités ») à utiliser vos services Azure AD payants. Pour chaque licence Azure AD payante que vous attribuez à un utilisateur, vous pouvez inviter jusqu’à cinq utilisateurs à l’aide d’une allocation d’utilisateur externe.

Un utilisateur invité est une personne qui n’est pas membre de votre organisation ni des filiales de celle-ci. Les utilisateurs invités sont définis par leur relation à votre organisation, et non par les informations d’identification qu’ils utilisent pour se connecter. En réalité, un utilisateur invité peut se connecter avec une identité externe ou avec les informations d’identification appartenant à votre organisation.

Les utilisateurs suivants *ne sont pas* des utilisateurs invités :
- Vos employés, ainsi que les sous-traitants ou agents sur site
- Les employés, ainsi que les sous-traitants ou agents sur site de vos filiales

L’attribution de licences aux utilisateurs invités B2B est automatiquement calculée et signalée d’après un ratio de 1 pour 5. Actuellement, il n’est pas possible d’attribuer directement des licences d’utilisateur invité B2B à des utilisateurs invités.

Il existe des cas pour lesquels un utilisateur invité n’est pas signalé à l’aide de l’allocation d’utilisateur externe d’après le ratio de 1 à 5. Si un utilisateur invité possède déjà une licence Azure AD payante dans l’organisation de l’utilisateur, il ne consomme pas l’une de vos licences d’utilisateur invité B2B. De plus, les utilisateurs invités peuvent utiliser des fonctionnalités Azure AD gratuites sans avoir besoin de licences supplémentaires. Les utilisateurs invités ont accès à des fonctionnalités Azure AD gratuites même si vous n’avez acheté aucune licence Azure AD. 

## <a name="examples-calculating-guest-user-licenses"></a>Exemples : Calculer le nombre de licences d’utilisateur invité
Une fois que vous avez déterminé le nombre d’utilisateurs invités qui a besoin d’accéder à vos services Azure AD payants, assurez-vous que vous avez suffisamment de licences Azure AD payantes pour couvrir les utilisateurs invités selon un ratio de 1 à 5. Voici quelques exemples :

- Vous souhaitez permettre à 100 utilisateurs invités d’utiliser vos applications et services Azure AD et vous voulez leur accorder une gestion et un approvisionnement des accès. Vous souhaitez également imposer à 50 d’entre eux une authentification multifacteur et un accès conditionnel. Pour prendre en charge cette combinaison, vous avez besoin de 10 licences Azure AD Basic et de 10 licences Azure AD Premium P1. Si vous envisagez d’utiliser des fonctionnalités de protection de l’identité avec vos utilisateurs invités, vous avez besoin de licences Azure AD Premium P2 pour couvrir les utilisateurs invités et ce toujours selon un ratio de 1 à 5.
- Pour inviter 60 utilisateurs et leur imposer une authentification multifacteur, vous devez donc disposer d’au moins 12 licences Azure AD Premium P1. Vous avez 10 employés détenant des licences Azure AD Premium P1, ce qui autorise 50 utilisateurs invités au maximum selon un ratio de 1 sur 5. Vous devez acheter deux licences Premium P1 supplémentaires afin de couvrir 10 utilisateurs invités supplémentaires.

## <a name="using-the-b2b-collaboration-api-to-invite-users-from-your-affiliates"></a>Utiliser l’API de collaboration B2B pour inviter des utilisateurs à partir de vos filiales

Par définition, un utilisateur invité B2B est un utilisateur externe que vous invitez à utiliser vos applications et services Azure AD payants. Un employé, un fournisseur ou un agent sur site de votre entreprise ou de l’une de vos filiales ne peut pas bénéficier de la collaboration B2B, même si les fonctionnalités B2B sont utilisées. Voici quelques exemples : 
- Vous souhaitez utiliser les informations d’identification externes (par exemple, une identité sociale) pour inviter un utilisateur qui est un employé de votre organisation. Ce scénario n’est pas conforme aux exigences d’attribution de licences définies et n’est pas autorisé. Les informations d’identification externes ne font pas d’un employé un utilisateur externe.  
- Vous souhaitez utiliser les API B2B pour inviter un utilisateur travaillant dans l’une des filiales de votre organisation. Bien que ce scénario utilise les API B2B pour inviter l’utilisateur, il n’est pas considéré comme une collaboration B2B. Il n’est pas conforme aux conditions d’attribution de licences définies, car un utilisateur de votre filiale n’est pas un utilisateur externe. 

Dans ces deux scénarios, la meilleure solution consiste à utiliser l’API B2B pour inviter les utilisateurs en tant que membres (invitedUserType = Membre) et leur attribuer une licence Azure AD. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la collaboration Azure AD B2B :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](what-is-b2b.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](faq.md)
