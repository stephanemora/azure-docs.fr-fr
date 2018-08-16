---
title: Guide pratique pour configurer des stratégies d’accès conditionnel Azure Active Directory pour les tentatives d’accès provenant de réseaux non approuvés | Microsoft Docs
description: Découvrez comment configurer une stratégie d’accès conditionnel dans Azure Active Directory (Azure AD) pour les tentatives d’accès provenant de réseaux non approuvés.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5ddde65b2a68e71d86af6ce3dcd2847736cf5823
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627183"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Guide pratique pour configurer des stratégies d’accès conditionnel pour les tentatives d’accès provenant de réseaux non approuvés   

Tout d’abord, dans un appareil où mobilité et cloud occupent le premier plan, Azure Active Directory (Azure AD) autorise une authentification unique sur les appareils, applications et services depuis n’importe où. Par conséquent, vos utilisateurs peuvent accéder à vos applications cloud non seulement à partir du réseau de votre organisation, mais aussi à partir de n’importe quel emplacement Internet non approuvé. Avec l’[accès conditionnel Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés peuvent accéder à vos applications cloud. Dans ce contexte, il est courant de contrôler les tentatives d’accès provenant de réseaux non approuvés. Cet article fournit les informations nécessaires pour configurer une stratégie d’accès conditionnel qui gère cette exigence. 

## <a name="prerequisites"></a>Conditions préalables

Cet article suppose de connaître : 

- les concepts de base de l’accès conditionnel Azure AD ; 
- la configuration des stratégies d’accès conditionnel sur le Portail Azure.

Consultez l'article :

- [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md) pour une vue d’ensemble de l’accès conditionnel. 

- [Démarrage rapide : Exiger l’authentification multifacteur (MFA) pour certaines applications disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md) pour acquérir de l’expérience dans la configuration de stratégies d’accès conditionnel. 


## <a name="scenario-description"></a>Description du scénario

Pour maîtriser l’équilibre entre sécurité et productivité, il peut suffire d’exiger une authentification par mot de passe de la part des utilisateurs. Toutefois, lorsqu’une tentative d’accès est effectuée à partir d’un emplacement réseau non approuvé, le risque de connexion par des utilisateurs non légitimes est accentué. Pour résoudre ce problème, vous pouvez bloquer les tentatives d’accès provenant de réseaux non approuvés. Il est également possible d’exiger l’authentification multifacteur (MFA) pour obtenir une garantie supplémentaire indiquant que la tentative a été effectuée par le propriétaire légitime du compte. 

L’accès conditionnel Azure AD permet de répondre à cette exigence avec une seule stratégie qui accorde l’accès : 

- à certaines applications cloud ;

- pour certains utilisateurs et groupes ;  

- en exigeant l’authentification multifacteur ; 

- lorsque la tentative d’accès provient : 

    - d’un emplacement non approuvé.


## <a name="considerations"></a>Considérations

Le défi de ce scénario consiste à traduire *lorsque la tentative d’accès provient d’un emplacement non approuvé* en une condition d’accès conditionnel. Dans une stratégie d’accès conditionnelle, vous pouvez configurer la [condition d’emplacements](location-condition.md) de façon à gérer les scénarios liés aux emplacements réseau. Cette condition permet de sélectionner des emplacements nommés, qui représentent des regroupements logiques de plages d’adresses IP, de pays et de régions.  

En règle générale, une organisation possède au moins une plage d’adresses, par exemple, 199.30.16.0-199.30.16.24.
Pour configurer un emplacement nommé :

- Spécifiez cette plage (199.30.16.0/24). 

- Attribuez un nom descriptif, par exemple, **Réseau d’entreprise**. 


Au lieu de tenter de définir tous les emplacements non approuvés, vous pouvez :

- Inclure 

    ![Accès conditionnel](./media/untrusted-networks/02.png)

- Exclure tous les emplacements approuvés 

    ![Accès conditionnel](./media/untrusted-networks/01.png)



## <a name="implementation"></a>Implémentation

Suivant l’approche décrite dans cet article, vous pouvez maintenant configurer une stratégie d’accès conditionnel pour les emplacements non approuvés. Nous vous recommandons de tester systématiquement votre stratégie avant de la déployer en production pour vérifier qu’elle fonctionne comme prévu. Dans l’idéal, faites vos tests initiaux dans un locataire de test si possible. Pour plus d’informations, voir [Guide pratique pour déployer une nouvelle stratégie](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’accès conditionnel, voir [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md).