---
title: Comment bloquer l’authentification héritée à Azure Active Directory (Azure AD) avec l’accès conditionnel | Microsoft Docs
description: Apprenez à améliorer votre approche de sécurité en bloquant l'authentification existante à l'aide de l'accès conditionnel Azure AD.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/25/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f46e6759b0803b49316219f2ca2a6a7beb3818
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434753"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Activation Bloquer l’authentification héritée à Azure AD avec l’accès conditionnel   

Pour permettre à vos utilisateurs d’accéder facilement à vos applications cloud, Azure Active Directory (Azure AD) prend en charge un large éventail de protocoles d’authentification, notamment l’authentification héritée. Toutefois, les protocoles hérités ne prennent pas en charge l’authentification multifacteur (MFA). L’authentification multifacteur est couramment requise dans de nombreux environnements pour lutter contre l’usurpation d’identité. 


Si votre environnement est prêt à bloquer l’authentification héritée pour améliorer la protection de votre locataire, vous pouvez atteindre cet objectif grâce à l’accès conditionnel. Cet article explique comment configurer les stratégies d’accès conditionnel bloquant l’authentification héritée pour votre locataire.



## <a name="prerequisites"></a>Conditions préalables

Cet article suppose de connaître : 

- Les [concepts de base](overview.md) de l’accès conditionnel Azure AD 
- Les [meilleures pratiques](best-practices.md) de configuration des stratégies d’accès conditionnel sur le Portail Azure



## <a name="scenario-description"></a>Description du scénario

Azure AD prend en charge plusieurs protocoles d’authentification et d’autorisation parmi ceux les plus couramment utilisés, notamment l’authentification héritée. L’authentification héritée concerne les protocoles qui utilisent l’authentification de base. En règle générale, ces protocoles ne peuvent pas appliquer n’importe quel type d’authentification à deux facteurs. Pour les applications qui reposent sur l’authentification héritée, citons :

- Applications Microsoft Office plus anciennes

- Les applications utilisant des protocoles de messagerie tels que POP, IMAP et SMTP

l’authentification à un seul facteur (par exemple le nom d’utilisateur et le mot de passe) ne suffit pas de nos jours. Les mots de passe sont insuffisants, car ils sont faciles à deviner et nous (les humains) ne savons pas choisir de bons mots de passe. Les mots de passe sont également vulnérables à diverses attaques comme le hameçonnage et la pulvérisation de mots de passe. L’un des moyens les plus simples pour vous protéger contre les menaces de vol de mot de passe consiste à implémenter l’authentification multifacteur. Avec l’authentification multifacteur, même si une personne malveillante obtient le mot de passe d’un utilisateur, ce seul mot de passe n’est pas suffisant pour s’authentifier et accéder aux données.

Comment pouvez-vous empêcher les applications utilisant l’authentification héritée d’accéder aux ressources de votre locataire ? Il est simplement recommandé de les bloquer à l’aide d’une stratégie d’accès conditionnel. Si nécessaire, vous pouvez autoriser uniquement certains utilisateurs et des emplacements réseau spécifiques à utiliser les applications s’appuyant sur l’authentification héritée.

Des stratégies d’accès conditionnel sont appliquées au terme de la première authentification à un facteur. L’accès conditionnel ne constitue donc pas une première ligne de défense dans des scénarios comme les attaques par déni de service, mais il peut utiliser des signaux émanant de ces événements (par exemple, le niveau de risque de connexion, l’emplacement de la requête, etc.) pour déterminer l’accès.




## <a name="implementation"></a>Implémentation

Cette section explique comment configurer une stratégie d’accès conditionnel pour bloquer l’authentification héritée. 

### <a name="block-legacy-authentication"></a>Bloquer l’authentification héritée 

Dans une stratégie d’accès conditionnel, vous pouvez définir une condition liée aux applications clientes utilisées pour accéder à vos ressources. Cette condition vous permet de limiter la portée aux applications utilisant l’authentification héritée en sélectionnant **Autres clients** pour les **Applications mobiles et clients de bureau**.

![Autres clients](./media/block-legacy-authentication/01.png)

Pour bloquer l’accès à ces applications, vous devez sélectionner **Bloquer l’accès**.

![Bloquer l’accès](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Sélectionner des utilisateurs et des applications cloud

Pour bloquer l’authentification héritée pour votre organisation, vous seriez peut-être tenté de sélectionner les éléments suivants :

- Tous les utilisateurs

- Toutes les applications cloud

- Bloquer l’accès
 

![Affectations](./media/block-legacy-authentication/03.png)



Azure a une fonction de sécurité qui vous empêche de créer une telle stratégie, car cette configuration ne respecte pas les [meilleures pratiques](best-practices.md) en matière de stratégies d’accès conditionnel.
 
![Configuration de stratégie non prise en charge](./media/block-legacy-authentication/04.png)


Cette fonction de sécurité est nécessaire, car *bloquer tous les utilisateurs et toutes les applications cloud* pourrait empêcher toute votre organisation de se connecter à votre locataire. Vous devez exclure au moins un utilisateur pour respecter les meilleures pratiques minimales. Vous pouvez également exclure un rôle d’annuaire.

![Configuration de stratégie non prise en charge](./media/block-legacy-authentication/05.png)


Vous pouvez contourner cette fonction de sécurité en excluant un utilisateur de votre stratégie. Dans l’idéal, vous devez définir quelques [comptes d’administration pour l’accès en urgence dans Azure AD](../users-groups-roles/directory-emergency-access.md) et les exclure de votre stratégie.
 

## <a name="policy-deployment"></a>Déploiement de stratégie

Avant de mettre votre stratégie en production, occupez-vous des éléments suivants :
 
- **Comptes de service** : identifiez les comptes utilisateurs utilisés comme comptes de service ou par des appareils, tels que des téléphones de salle de conférence. Assurez-vous que ces comptes ont des mots de passe forts et ajoutez-les à un groupe à exclure.
 
- **Rapports de connexion** : passez en revue le rapport de connexion et recherchez le trafic provenant d’**autres clients**. Identifiez les éléments les plus utilisés et la raison de leur utilisation. En règle générale, le trafic est généré par les clients Office plus anciens qui n’utilisent pas de méthode d’authentification moderne, ou par certaines applications de messagerie tierces. Établissez un plan pour éliminer l’utilisation de ces applications, ou en cas d’impact faible, prévenez vos utilisateurs qu’ils ne peuvent plus les utiliser.
 
Pour plus d’informations, voir [Guide pratique pour déployer une nouvelle stratégie](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>Ce que vous devez savoir

Bloque l’accès à l’aide **autres clients** bloque également Exchange Online PowerShell à l’aide d’authentification de base.

La configuration d’une stratégie pour **d’autres clients** bloque l’organisation entière à partir de certains clients tels que SPConnect. Ce blocage se produit, car les clients plus anciens s’authentifient de manière inattendue. Ce problème ne concerne pas aux principales applications Office, telles que les anciens clients Office.

L’entrée en vigueur de la stratégie peut prendre jusqu’à 24 heures.

Vous pouvez sélectionner tous les contrôles d’octroi disponibles pour la condition Autre clients. Toutefois, l’expérience de l’utilisateur final est toujours la même : un accès bloqué.

Si vous bloquez l'authentification héritée à l'aide de la condition des autres clients, vous pouvez également définir la plateforme du périphérique et la condition de localisation. Par exemple, si vous souhaitez uniquement bloquer l’authentification héritée des appareils mobiles, définissez la condition des **plateformes d’appareils** en sélectionnant :

- Android

- iOS

- Windows Phone

![Configuration de stratégie non prise en charge](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Étapes suivantes

- Si vous ne savez pas encore configurer des stratégies d’accès conditionnel, consultez [Exiger l’authentification multifacteur (MFA) pour certaines applications disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md) pour en avoir un exemple.

- Pour plus d’informations sur la prise en charge l’authentification moderne, consultez [modernes de fonctionnement de l’authentification pour les applications clientes Office 2013 et Office 2016](https://docs.microsoft.com/en-us/office365/enterprise/modern-auth-for-office-2013-and-2016) 
