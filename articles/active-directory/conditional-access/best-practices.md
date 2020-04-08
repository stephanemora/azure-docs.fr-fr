---
title: Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory | Microsoft Docs
description: Découvrez-en plus sur les éléments à connaître et ce que vous devez éviter lors de la configuration des stratégies d’accès conditionnel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295356"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Cet article vous fournit des informations sur :

- ce que vous devez savoir ; 
- ce que vous devez éviter lors de la configuration des stratégies d’accès conditionnel. 

Cet article part du principe que vous connaissez les concepts et la terminologie décrits dans [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Qu’est-ce qui est nécessaire pour faire fonctionner une stratégie ?

Lorsque vous créez une stratégie, aucun utilisateur, groupe, application ou contrôle d’accès n’est sélectionné.

![Applications cloud](./media/best-practices/02.png)

Pour que votre stratégie fonctionne, vous devez configurer les éléments suivants :

| Quoi           | Comment                                  | Pourquoi |
| :--            | :--                                  | :-- |
| **Applications cloud** |Sélection d’une ou de plusieurs applications.  | Une stratégie d’accès conditionnel a pour fonction de vous permettre de contrôler la manière dont les utilisateurs autorisés peuvent accéder à des applications cloud.|
| **Utilisateurs et groupes** | Sélection d’au moins un utilisateur ou un groupe autorisé à accéder à vos applications cloud sélectionnées. | Une stratégie d’accès conditionnel, à laquelle aucun utilisateur ou groupe n’est affecté, n’est jamais déclenchée. |
| **Contrôles d’accès** | Sélection d’au moins un contrôle d’accès. | Si vos conditions sont remplies, votre processeur de stratégies doit connaître la procédure à suivre. |

## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="how-are-conditional-access-policies-applied"></a>Comment les stratégies d’accès conditionnel sont-elles appliquées ?

Plusieurs stratégies d’accès conditionnel peuvent s’appliquer lorsque vous accédez à une application cloud. Dans ce cas, toutes les stratégies qui s’appliquent doivent être satisfaites. Par exemple, si une première stratégie demande une authentification multifacteur (MFA) et qu’une autre demande un appareil conforme, vous devez procéder à la MFA et utiliser un appareil compatible. 

Toutes les stratégies sont appliquées en deux phases :

- Phase 1 : 
   - Collecte des détails : Rassemblez les détails pour identifier les stratégies qui seraient déjà satisfaites.
   - Pendant cette phase, les utilisateurs peuvent voir une invite de certificat si la conformité de l’appareil fait partie de vos stratégies d’accès conditionnel. Cette invite peut s’afficher pour les applications de navigateur lorsque le système d’exploitation de l’appareil n’est pas Windows 10.
   - La phase 1 de l’évaluation de la stratégie se produit pour toutes les stratégies activées et les stratégies en [mode Rapport uniquement](concept-conditional-access-report-only.md).
- Phase 2 :
   - Application : En tenant compte des détails collectés lors de la phase 1, demandez à l’utilisateur de satisfaire toutes les autres exigences qui n’ont pas été respectées.
   - Appliquez les résultats à la session. 
   - La phase 2 de l’évaluation de la stratégie se produit pour toutes les stratégies activées.

### <a name="how-are-assignments-evaluated"></a>Comment les affectations sont-elles évaluées ?

Toutes les attributions sont reliées par l’opérateur logique **AND**. Si vous configurez plusieurs affectations, ces dernières doivent toutes être satisfaites pour qu’une stratégie soit déclenchée.  

Si vous souhaitez configurer une condition d’emplacement applicable à toutes les connexions non établies à partir du réseau de votre organisation, vous devez :

- inclure **Tous les emplacements** ;
- exclure **Toutes les adresses IP approuvées**.

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Que faire si votre accès au portail d’administration Azure AD est verrouillé ?

Si vous ne pouvez pas accéder au portail Azure AD en raison d’un paramètre incorrect dans une stratégie d’accès conditionnel :

- Vérifiez s’il existe d’autres administrateurs dans votre organisation dont l’accès n’a pas encore été verrouillé. Un administrateur ayant accès au portail Azure peut désactiver la stratégie qui affecte votre connexion. 
- Si aucun des administrateurs de votre organisation ne peut mettre à jour la stratégie, vous devez soumettre une demande de support. Le support Microsoft peut vérifier et mettre à jour les stratégies d’accès conditionnel qui empêchent l’accès.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Que se passe-t-il si vous avez configuré des stratégies dans le portail Azure Classic et le portail Azure ?  

Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Que se passe-t-il si vous disposez de stratégies dans le portail Intune Silverlight et dans le portail Azure ?

Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Que se passe-t-il si j’ai configuré plusieurs stratégies pour le même utilisateur ?  

À chaque connexion, Azure Active Directory évalue toutes les stratégies et vérifie que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur. Le blocage de l’accès prévaut sur tous les autres paramètres de configuration. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>L’accès conditionnel fonctionne-t-il avec Exchange ActiveSync ?

Oui, vous pouvez utiliser Exchange ActiveSync dans une stratégie d’accès conditionnel.

Certaines applications cloud comme SharePoint Online et Exchange Online prennent également en charge les protocoles d’authentification existants. Quand une application cliente peut utiliser un protocole d’authentification hérité pour accéder à une application cloud, Azure AD ne peut pas appliquer de stratégie d’accès conditionnel sur cette tentative d’accès. Pour empêcher une application cliente de contourner la mise en œuvre des stratégies, vous devez vérifier s’il est possible d’activer uniquement l’authentification moderne sur les applications cloud concernées.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Comment devez-vous configurer l’accès conditionnel avec des applications Office 365 ?

Étant donné que les applications Office 365 sont interconnectées, nous vous recommandons d’affecter les applications couramment utilisées ensemble lors de la création de stratégies.

Les applications interconnectées courantes comprennent Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online et Office 365 Yammer.

C’est important pour les stratégies qui exigent des interactions utilisateur, comme l’authentification multifacteur, quand l’accès est contrôlé au début d’une session ou d’une tâche. Si vous ne suivez pas cette recommandation, les utilisateurs ne peuvent pas effectuer certaines tâches au sein d’une application. Par exemple, si vous avez besoin d’une authentification multifacteur sur des appareils non gérés pour accéder à SharePoint, mais pas à la messagerie, les utilisateurs qui travaillent dans leur messagerie ne peuvent pas joindre de fichiers SharePoint à un message. Vous trouverez plus d’informations dans l’article [Quelles sont les dépendances de service dans l’accès conditionnel Azure Active Directory ?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Ce que vous devez éviter

L’infrastructure d’accès conditionnel vous offre une souplesse de configuration exceptionnelle. Toutefois, une grande souplesse signifie également que vous devez examiner soigneusement chaque stratégie de configuration avant de la mettre en œuvre afin d’éviter des résultats indésirables. Dans ce contexte, prêtez une attention particulière à l’affectation de jeux complets comme par exemple **tous les utilisateurs / groupes / applications cloud**.

Dans votre environnement, vous devez éviter les configurations suivantes :

**Pour tous les utilisateurs, toutes les applications cloud :**

- **Bloquer l’accès** : cette configuration bloque toute votre organisation, ce qui n’est pas une bonne idée.
- **Exiger un appareil conforme** : pour les utilisateurs qui n’ont pas encore inscrit leurs appareils, cette stratégie bloque tout accès, notamment l’accès au portail Intune. Si vous êtes un administrateur sans appareil inscrit, cette stratégie vous bloque et vous ne pouvez pas retourner dans le portail Azure pour modifier la stratégie.
- **Exiger la jonction de domaine** : ce blocage d’accès par stratégie peut également bloquer l’accès pour tous les utilisateurs de votre organisation si vous n’avez pas encore d’appareil joint à un domaine.
- **Exiger une stratégie de protection des applications** : ce blocage d’accès par stratégie peut également bloquer l’accès pour tous les utilisateurs de votre organisation si vous n’avez pas encore de stratégie Intune. Si vous êtes administrateur sans application cliente dotée d’une stratégie de protection des applications Intune, cette stratégie vous empêche de revenir aux portails comme Intune et Azure.

**Pour tous les utilisateurs, toutes les applications cloud, toutes les plates-formes d’appareils :**

- **Bloquer l’accès** : cette configuration bloque toute votre organisation, ce qui n’est pas une bonne idée.

## <a name="how-should-you-deploy-a-new-policy"></a>Comment déployer une nouvelle stratégie ?

Pour commencer, vous devez évaluer votre stratégie à l’aide de [l’outil de simulation](what-if-tool.md).

Lorsque de nouvelles stratégies sont prêtes pour votre environnement, déployez-les en phases :

1. Appliquez une stratégie à un ensemble d’utilisateurs restreint et vérifiez qu’elle fonctionne comme prévu. 
1. Lorsque vous développez une stratégie pour inclure davantage d’utilisateurs. Continuez à exclure tous les administrateurs de la stratégie pour vous assurer qu’ils ont toujours accès et qu’ils peuvent mettre à jour une stratégie si une modification est nécessaire.
1. Appliquez une stratégie à tous les utilisateurs uniquement si c’est nécessaire. 

Une bonne pratique consiste à créer un compte d’utilisateur :

- dédié à l’administration des stratégies ; 
- exclu de toutes vos stratégies.

## <a name="policy-migration"></a>Migration des stratégies

Envisagez d’effectuer la migration des stratégies que vous n’avez pas créées dans le portail Azure, car :

- Vous pouvez maintenant résoudre des scénarios que vous ne pouviez pas gérer auparavant.
- Vous pouvez réduire le nombre de stratégies que vous devez gérer en les consolidant.   
- Vous pouvez gérer toutes vos stratégies d’accès conditionnel depuis un emplacement central.
- Le Portail Azure Classic a été mis hors service.   

Pour plus d’informations, consultez [Migrer les stratégies classiques dans le portail Azure](policy-migration.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez savoir :

- Pour en savoir plus sur la configuration d’une stratégie d’accès conditionnel, consultez [Exiger une authentification MFA pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).
- Pour en savoir plus sur la planification de vos stratégies d’accès conditionnel, consultez [Guide pratique pour planifier votre déploiement de l’accès conditionnel dans Azure Active Directory](plan-conditional-access.md).
