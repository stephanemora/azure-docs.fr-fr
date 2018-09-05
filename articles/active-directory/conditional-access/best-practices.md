---
title: Meilleures pratiques l’accès conditionnel dans Azure Active Directory | Microsoft Docs
description: Découvrez-en davantage sur les éléments à connaître ce que vous devez évite lors de la configuration des stratégies d’accès conditionnel.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 4e9f5a9318db813b1a0f16d3599f74fd98e53ffc
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818055"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Meilleures pratiques l’accès conditionnel dans Azure Active Directory

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Cet article vous fournit des informations sur :

- ce que vous devez savoir ; 
- ce que vous devez éviter lors de la configuration des stratégies d’accès conditionnel. 

Cet article part du principe que vous connaissez les concepts et la terminologie décrits dans [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md).



## <a name="whats-required-to-make-a-policy-work"></a>Qu’est-ce qui est nécessaire pour faire fonctionner une stratégie ?

Lorsque vous créez une stratégie, aucun utilisateur, groupe, application ou contrôle d’accès n’est sélectionné.

![Applications cloud](./media/best-practices/02.png)


Pour que votre stratégie fonctionne, vous devez configurer les éléments suivants :


|Quoi           | Comment                                  | Pourquoi|
|:--            | :--                                  | :-- |
|**Applications cloud** |Vous devez sélectionner une ou plusieurs applications.  | Une stratégie d’accès conditionnel a pour fonction de vous permettre de contrôler la manière dont les utilisateurs autorisés peuvent accéder à vos applications cloud.|
| **Utilisateurs et groupes** | Vous devez sélectionner au moins un utilisateur ou groupe autorisé à accéder à vos applications cloud sélectionnées. | Une stratégie d’accès conditionnel, à laquelle aucun utilisateur ou groupe n’est affecté, n’est jamais déclenchée. |
| **Contrôles d’accès** | Vous devez sélectionner au moins un contrôle d'accès. | Si vos conditions sont remplies, votre processeur de stratégies doit connaître la procédure à suivre.|




## <a name="what-you-should-know"></a>Ce que vous devez savoir

### <a name="how-are-assignments-evaluated"></a>Comment les affectations sont-elles évaluées ?

Toutes les attributions sont reliées par l’opérateur logique **AND**. Si vous configurez plusieurs affectations, ces dernières doivent toutes être satisfaites pour qu’une stratégie soit déclenchée.  

Si vous souhaitez configurer une condition d’emplacement applicable à toutes les connexions non établies à partir du réseau de votre organisation, vous devez :

- inclure **Tous les emplacements** ;
- exclure **Toutes les adresses IP approuvées**.


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Que faire si votre accès au portail d’administration Azure AD est verrouillé ?

Si vous ne pouvez pas accéder au portail Azure AD en raison d’un paramètre incorrect dans une stratégie d’accès conditionnel :

- Vérifiez s’il existe d’autres administrateurs dans votre organisation dont l’accès n’a pas encore été verrouillé. Un administrateur ayant accès au portail Azure peut désactiver la stratégie qui affecte votre connexion. 

- Si aucun des administrateurs de votre organisation ne peut mettre à jour la stratégie, vous devez soumettre une demande de support. Le Support Microsoft peut consulter et mettre à jour les stratégies d’accès conditionnel qui empêchent l’accès.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Que se passe-t-il si vous avez configuré des stratégies dans le portail Azure Classic et le portail Azure ?  

Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Que se passe-t-il si vous disposez de stratégies dans le portail Intune Silverlight et dans le portail Azure ?

Azure Active Directory applique les deux stratégies et l’utilisateur n’obtient l’accès que si toutes les conditions requises sont remplies.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Que se passe-t-il si j’ai configuré plusieurs stratégies pour le même utilisateur ?  

À chaque connexion, Azure Active Directory évalue toutes les stratégies et vérifie que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur. Le blocage de l’accès prévaut sur tous les autres paramètres de configuration. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>L’accès conditionnel fonctionne-t-il avec Exchange ActiveSync ?

Oui, vous pouvez utiliser Exchange ActiveSync dans une stratégie d’accès conditionnel.






## <a name="what-you-should-avoid-doing"></a>Ce que vous devez éviter

L’infrastructure d’accès conditionnel vous offre une souplesse de configuration exceptionnelle. Toutefois, une grande souplesse signifie également que vous devez examiner soigneusement chaque stratégie de configuration avant de la mettre en œuvre afin d’éviter des résultats indésirables. Dans ce contexte, prêtez une attention particulière à l’affectation de jeux complets comme par exemple **tous les utilisateurs / groupes / applications cloud**.

Dans votre environnement, vous devez éviter les configurations suivantes :


**Pour tous les utilisateurs, toutes les applications cloud :**

- **Bloquer l’accès** : cette configuration bloque toute votre organisation, ce qui n’est pas une bonne idée.

- **Exiger un appareil conforme** : pour les utilisateurs qui n’ont pas encore inscrit leurs appareils, cette stratégie bloque tout accès, notamment l’accès au portail Intune. Si vous êtes un administrateur sans appareil inscrit, cette stratégie vous bloque et vous ne pouvez pas retourner dans le portail Azure pour modifier la stratégie.

- **Exiger la jonction de domaine** : ce blocage d’accès par stratégie peut également bloquer l’accès pour tous les utilisateurs de votre organisation si vous n’avez pas encore d’appareil joint à un domaine.


**Pour tous les utilisateurs, toutes les applications cloud, toutes les plates-formes d’appareils :**

- **Bloquer l’accès** : cette configuration bloque toute votre organisation, ce qui n’est pas une bonne idée.


## <a name="how-should-you-deploy-a-new-policy"></a>Comment déployer une nouvelle stratégie ?

Pour commencer, vous devez évaluer votre stratégie à l’aide de [l’outil de simulation](what-if-tool.md).

Lorsque vous êtes prêt à déployer une nouvelle stratégie dans votre environnement, vous devez le faire en plusieurs phases :

1. Appliquez une stratégie à un ensemble d’utilisateurs restreint et vérifiez qu’elle fonctionne comme prévu. 

2.  Lorsque vous étendez une stratégie de façon à inclure davantage d’utilisateurs, continuez à exclure tous les administrateurs de la stratégie. Cette opération garantit que les administrateurs disposent toujours d’un accès et qu’ils peuvent mettre à jour une stratégie si une modification est nécessaire.

3. N’appliquez une stratégie à tous les utilisateurs que si cette opération se révèle réellement nécessaire. 

Une bonne pratique consiste à créer un compte d’utilisateur :

- dédié à l’administration des stratégies ; 
- exclu de toutes vos stratégies.


## <a name="policy-migration"></a>Migration des stratégies

Envisagez d’effectuer la migration des stratégies que vous n’avez pas créées dans le portail Azure, car :

- Vous pouvez maintenant résoudre des scénarios que vous ne pouviez pas gérer auparavant.

- Vous pouvez réduire le nombre de stratégies que vous devez gérer en les consolidant.   

- Vous pouvez gérer toutes vos stratégies d’accès conditionnel dans un emplacement central.

- Le Portail Azure Classic a été mis hors service.   


Pour plus d’informations, consultez [Migrer les stratégies classiques dans le portail Azure](policy-migration.md).


## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).
