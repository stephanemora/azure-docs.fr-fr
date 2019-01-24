---
title: Guide pratique pour exiger une authentification multifacteur (MFA) pour l’accès à partir de réseaux non approuvés disposant d’un accès conditionnel Azure Active Directory (Azure AD) | Microsoft Docs
description: Découvrez comment configurer une stratégie d’accès conditionnel dans Azure Active Directory (Azure AD) pour les tentatives d’accès provenant de réseaux non approuvés.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 7ffb3bfb4985f56e7e2e81a2a6d08a6ff7469fdb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445403"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Activation Exiger une authentification multifacteur (MFA) pour l’accès à partir de réseaux non approuvés disposant d’un accès conditionnel   

Azure Active Directory (Azure AD) active l’authentification unique aux appareils, applications et des services depuis n’importe quel endroit. Vos utilisateurs peuvent accéder à vos applications cloud non seulement à partir du réseau de votre organisation, mais aussi à partir de n’importe quel emplacement Internet non approuvé. Une meilleure pratique courante pour l’accès à partir de réseaux non approuvés consiste à exiger une authentification multifacteur (MFA).

Cet article vous donne les informations dont vous avez besoin pour configurer une stratégie d’accès conditionnel qui requiert l’authentification MFA pour l’accès à partir de réseaux non approuvés. 

## <a name="prerequisites"></a>Prérequis

Cet article suppose de connaître : 

- Les [concepts de base](overview.md) de l’accès conditionnel Azure AD 
- Les [meilleures pratiques](best-practices.md) de configuration des stratégies d’accès conditionnel sur le Portail Azure



## <a name="scenario-description"></a>Description du scénario

Pour maîtriser l’équilibre entre sécurité et productivité, il peut suffire d’exiger un mot de passe pour les connexions depuis le réseau de votre organisation. Toutefois, en cas d’accès à partir d’un emplacement réseau non approuvé, le risque de connexion par des utilisateurs non légitimes est accentué. Pour résoudre ce problème, vous pouvez bloquer l’accès à partir de réseaux non approuvés. Il est également possible d’exiger l’authentification multifacteur (MFA) pour obtenir une garantie supplémentaire indiquant que la tentative a été effectuée par le propriétaire légitime du compte. 

L’accès conditionnel Azure AD permet de répondre à cette exigence avec une seule stratégie qui accorde l’accès : 

- à certaines applications cloud ;

- pour certains utilisateurs et groupes ;  

- en exigeant l’authentification multifacteur ; 

- Lorsque l’accès émane : 

    - d’un emplacement non approuvé.


## <a name="implementation"></a>Implémentation

Le défi de ce scénario consiste à traduire *l’accès à partir d’un emplacement réseau non approuvé* en une condition d’accès conditionnel. Dans une stratégie d’accès conditionnelle, vous pouvez configurer la [condition d’emplacements](location-condition.md) de façon à gérer les scénarios liés aux emplacements réseau. Cette condition permet de sélectionner des emplacements nommés, qui sont des regroupements logiques de plages d’adresses IP, de pays et de régions.  

En règle générale, une organisation possède au moins une plage d’adresses, par exemple, 199.30.16.0-199.30.16.24.
Pour configurer un emplacement nommé :

- Spécifiez cette plage (199.30.16.0/24). 

- Attribuez un nom descriptif, par exemple, **Réseau d’entreprise**. 


Au lieu de tenter de définir tous les emplacements non approuvés, vous pouvez :

- Inclure n’importe quel emplacement 

    ![Accès conditionnel](./media/untrusted-networks/02.png)

- Exclure tous les emplacements approuvés 

    ![Accès conditionnel](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Déploiement de stratégie

Suivant l’approche décrite dans cet article, vous pouvez maintenant configurer une stratégie d’accès conditionnel pour les emplacements non approuvés. Pour vous assurer que votre stratégie fonctionne comme prévu, la meilleure pratique recommandée consiste à la tester avant de la déployer en production. Dans l’idéal, utilisez un locataire de test pour vérifier si votre nouvelle stratégie fonctionne comme prévu. Pour plus d’informations, consultez [Guide pratique pour déployer une nouvelle stratégie](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’accès conditionnel, voir [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md).