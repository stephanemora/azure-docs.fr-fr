---
title: Bloquer l’authentification héritée - Azure Active Directory
description: Apprenez à améliorer votre approche de sécurité en bloquant l’authentification héritée à l’aide de l’accès conditionnel Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380861"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Activation Bloquer l’authentification héritée à Microsoft Azure AD avec l’accès conditionnel   

Pour permettre à vos utilisateurs d’accéder facilement à vos applications cloud, Azure Active Directory (Azure AD) prend en charge un large éventail de protocoles d’authentification, notamment l’authentification héritée. Toutefois, les protocoles hérités ne prennent pas en charge l’authentification multifacteur (MFA). L’authentification multifacteur est couramment requise dans de nombreux environnements pour lutter contre l’usurpation d’identité. 

Si votre environnement est prêt à bloquer l’authentification héritée pour améliorer la protection de votre locataire, vous pouvez atteindre cet objectif grâce à l’accès conditionnel. Cet article explique comment configurer des stratégies d’accès conditionnel qui bloquent l’authentification héritée pour votre locataire.

## <a name="prerequisites"></a>Prérequis

Cet article suppose de connaître : 

- [Concepts de base](overview.md) de l’accès conditionnel Azure AD 
- [Meilleures pratiques](best-practices.md) de configuration des stratégies d’accès conditionnel sur le Portail Microsoft Azure

## <a name="scenario-description"></a>Description du scénario

Azure AD prend en charge plusieurs protocoles d’authentification et d’autorisation parmi ceux les plus couramment utilisés, notamment l’authentification héritée. L’authentification héritée concerne les protocoles qui utilisent l’authentification de base. En règle générale, ces protocoles ne peuvent pas appliquer n’importe quel type d’authentification à deux facteurs. Pour les applications qui reposent sur l’authentification héritée, citons :

- Applications Microsoft Office plus anciennes
- Les applications utilisant des protocoles de messagerie tels que POP, IMAP et SMTP

l’authentification à un seul facteur (par exemple le nom d’utilisateur et le mot de passe) ne suffit pas de nos jours. Les mots de passe sont insuffisants, car ils sont faciles à deviner et nous (les humains) ne savons pas choisir de bons mots de passe. Les mots de passe sont également vulnérables à diverses attaques comme le hameçonnage et la pulvérisation de mots de passe. L’un des moyens les plus simples pour vous protéger contre les menaces de vol de mot de passe consiste à implémenter l’authentification multifacteur. Avec l’authentification multifacteur, même si une personne malveillante obtient le mot de passe d’un utilisateur, ce seul mot de passe n’est pas suffisant pour s’authentifier et accéder aux données.

Comment pouvez-vous empêcher les applications utilisant l’authentification héritée d’accéder aux ressources de votre locataire ? Il est recommandé de les bloquer à l’aide d’une stratégie d’accès conditionnel, tout simplement. Si nécessaire, vous pouvez autoriser uniquement certains utilisateurs et des emplacements réseau spécifiques à utiliser les applications s’appuyant sur l’authentification héritée.

Des stratégies d’accès conditionnel sont appliquées au terme de la première authentification à un facteur. L’accès conditionnel ne constitue donc pas une première ligne de défense dans des scénarios comme les attaques par déni de service, mais il peut utiliser des signaux émanant de ces événements (par exemple, le niveau de risque de connexion, l’emplacement de la requête, etc.) pour déterminer l’accès.

## <a name="implementation"></a>Implémentation

Cette section explique comment configurer une stratégie d’accès conditionnel afin de bloquer l’authentification héritée. 

### <a name="identify-legacy-authentication-use"></a>Une authentification héritée est utilisée

Avant de pouvoir bloquer l’authentification héritée dans votre annuaire, vous devez savoir si vos utilisateurs disposent d’applications qui utilisent l’authentification héritée, puis déterminer quel impact cela a sur l’ensemble de votre annuaire. Les journaux de connexion Azure AD peuvent servir à déterminer si vous utilisez une authentification héritée.

1. Accédez au **portail Azure** > **Azure Active Directory** > **Connexions**.
1. Si elle n’est pas affichée, ajoutez la colonne Application cliente en cliquant sur **Colonnes** > **Application cliente**.
1. **Ajouter des filtres** > **Application cliente** > sélectionnez toutes les options pour **Autres clients**, puis cliquez sur **Appliquer**.

Grâce au filtrage, vous afficherez uniquement les tentatives de connexion effectuées via des protocoles d’authentification héritée. Cliquez sur chaque tentative de connexion pour afficher des détails supplémentaires. Le champ **Application cliente** affiché sous l’onglet **Informations de base** indique quel protocole d’authentification héritée a été utilisé.

Ces journaux identifie les utilisateurs qui continuent de tirer parti de la fonction d’authentification héritée, ainsi que les applications qui utilisent les protocoles hérités pour effectuer des requêtes d’authentification. Implémentez une stratégie d’accès conditionnel réservée aux utilisateurs non affichés dans ces journaux et dont vous êtes certain qu’ils n’utilisent pas l’authentification héritée.

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

Azure inclut une fonction de sécurité qui vous empêche de créer une telle stratégie, car cette configuration ne respecte pas les [meilleures pratiques](best-practices.md) en matière de stratégies d’accès conditionnel.
 
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

En bloquant l’accès avec **Autres clients**, vous empêchez également Exchange Online PowerShell et Dynamics 365 d’utiliser l’authentification de base.

La configuration d’une stratégie pour **d’autres clients** bloque l’organisation entière à partir de certains clients tels que SPConnect. Ce blocage se produit, car les clients plus anciens s’authentifient de manière inattendue. Ce problème ne concerne pas aux principales applications Office, telles que les anciens clients Office.

L’entrée en vigueur de la stratégie peut prendre jusqu’à 24 heures.

Vous pouvez sélectionner tous les contrôles d’octroi disponibles pour la condition **Autre clients**. Toutefois, l’expérience de l’utilisateur final est toujours la même : un accès bloqué.

Si vous bloquez l’authentification héritée à l’aide de la condition **Autres clients**, vous pouvez également définir la plateforme de l’appareil et la condition de localisation. Par exemple, si vous souhaitez uniquement bloquer l’authentification héritée des appareils mobiles, définissez la condition des **plateformes d’appareils** en sélectionnant :

- Android
- iOS
- Windows Phone

![Configuration de stratégie non prise en charge](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Étapes suivantes

- Si vous ne savez pas encore configurer des stratégies d’accès conditionnel, consultez la section [Exiger l’authentification multifacteur (MFA) pour certaines applications disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md) pour en avoir un exemple.
- Pour en savoir plus sur la prise en charge de l’authentification moderne, voir [Fonctionnement de l’authentification moderne pour les applications clientes Office 2013 et Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016). 
