---
title: Que sont les conditions dans l’accès conditionnel Azure Active Directory ? | Microsoft Docs
description: Découvrez comment les conditions sont utilisées dans l’accès conditionnel Azure Active Directory pour déclencher une stratégie.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: f5b0472481a953b42d8dd89c1766e2c8064b3241
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449723"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Que sont les conditions dans l’accès conditionnel Azure Active Directory ? 

Vous pouvez contrôler la façon dont les utilisateurs accèdent à vos applications cloud avec l’[accès conditionnel Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Dans une stratégie d’accès conditionnel, vous définissez la réponse (« Ensuite faire ») sur la raison du déclenchement de votre stratégie (« Quand cela se produit »). 

![Raison et réponse](./media/conditions/10.png)


Dans le contexte de l’accès conditionnel, **Lorsque cela se produit** est appelé une **condition**. **Faire** est un **contrôle d’accès**. Une stratégie d’accès conditionnel combine des conditions et des contrôles d’accès.

![Stratégie d’accès conditionnel](./media/conditions/61.png)


Les conditions que vous n’avez pas configurées dans une stratégie d’accès conditionnel ne sont pas appliquées. Certaines conditions sont [obligatoires](best-practices.md) pour appliquer une stratégie d’accès conditionnel à un environnement. 

Cet article décrit les conditions et la façon dont elles sont utilisées dans une stratégie d’accès conditionnel. 

## <a name="users-and-groups"></a>Utilisateurs et groupes

La condition d’utilisateurs et de groupes est obligatoire dans une stratégie d’accès conditionnel. Dans votre stratégie, vous pouvez soit sélectionner **Tous les utilisateurs** soit sélectionner des utilisateurs et groupes spécifiques.

![Utilisateurs et groupes](./media/conditions/111.png)

Lorsque vous sélectionnez **Tous les utilisateurs**, votre stratégie est appliquée à tous les utilisateurs de l’annuaire, y compris les utilisateurs invités.

Lorsque vous **sélectionnez des utilisateurs et des groupes**, vous pouvez définir les options suivantes :

* L’option **Tous les utilisateurs invités** permet de cibler une stratégie sur des utilisateurs invités B2B. Cette condition recherche tous les comptes d’utilisateur dont l’attribut **userType** a la valeur **invité**. Utilisez ce paramètre quand une stratégie doit être appliquée dès la création du compte dans un flux d’invitation dans Azure AD.

* Les **rôles d’annuaire** permettent de cibler une stratégie selon l’attribution de rôle d’un utilisateur. Cette condition prend en charge les rôles d’annuaire comme **administrateur général** ou **administrateur de mots de passe**.

* L’option **Utilisateurs et groupes** cible des ensembles spécifiques d’utilisateurs. Par exemple, vous pouvez sélectionner un groupe qui contient tous les membres du service RH lorsqu’une application RH est sélectionnée en tant qu’application cloud. Un groupe peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés.

Vous pouvez également exclure des utilisateurs ou groupes spécifiques d’une stratégie. Les comptes de service sont un cas d’utilisation courant si votre stratégie met en œuvre l’authentification multifacteur (MFA). 

Le ciblage d’ensembles spécifiques d’utilisateurs est utile pour le déploiement d’une nouvelle stratégie. Dans une nouvelle stratégie, vous devez cibler uniquement un ensemble initial d’utilisateurs pour valider le comportement de stratégie. 



## <a name="cloud-apps"></a>Applications cloud 

Une application cloud est un site web ou un service. Les sites web protégés par le proxy d’application Azure AD sont également des applications cloud. Pour une description détaillée des applications cloud prises en charge, consultez [Affectations des applications cloud](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

La condition **Applications de cloud** est obligatoire dans une stratégie d’accès conditionnel. Dans votre stratégie, vous pouvez soit sélectionner **Toutes les applications cloud** soit sélectionner des applications spécifiques.

![Inclure les applications cloud](./media/conditions/03.png)

Sélectionnez :

- **Toutes les applications cloud** pour que les stratégies de base s’appliquent à toute l’organisation. Procédez ainsi pour les stratégies qui requièrent l’authentification multifacteur lorsqu’un risque à la connexion est détecté sur une application cloud. Une stratégie appliquée à **toutes les applications cloud** concerne l’accès à tous les services et sites web. Ce paramètre n’est pas limité aux applications cloud de la liste **Sélection des applications**. 

- **Sélectionnez des applications** pour cibler des services spécifiques avec votre stratégie. Par exemple, vous pouvez demander aux utilisateurs d’avoir un [Appareil conforme](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) pour accéder à SharePoint Online. Cette stratégie s’applique aussi à d’autres services lorsqu’ils accèdent à des contenus SharePoint. Microsoft Teams en est un exemple. 

Vous pouvez exclure des applications spécifiques d’une stratégie. Toutefois, ces applications sont toujours soumises aux stratégies appliquées aux services auxquels elles accèdent. 



## <a name="sign-in-risk"></a>Risque à la connexion

Un risque à la connexion est une indication de la probabilité (haute, moyenne ou faible) qu’une connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur. Azure AD calcule le niveau de risque à la connexion lors de la connexion d’un utilisateur. Vous pouvez utiliser le niveau de risque de connexion calculé en tant que condition dans une stratégie d’accès conditionnel.

![Niveaux de risque à la connexion](./media/conditions/22.png)

Pour utiliser cette condition, vous devez avoir [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) activé.
 
Les cas d’utilisation courants pour cette condition sont des stratégies qui offrent les protections suivantes : 

- Bloquer les utilisateurs présentant un risque élevé. Cette protection empêche les utilisateurs potentiellement illégitimes d’accéder à vos applications cloud. 
- Exiger une authentification multifacteur pour les utilisateurs présentant un risque moyen. En appliquant l’authentification multifacteur, vous pouvez fournir l’assurance supplémentaire que la connexion est effectuée par le propriétaire légitime d’un compte.

Pour plus d’informations, consultez [Bloquer l’accès lorsqu’un risque de session est détecté](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Plateformes d’appareils

La plateforme d’appareils se caractérise par le système d’exploitation qui s’exécute sur votre appareil. Azure AD identifie la plateforme à l’aide des informations fournies par l’appareil, telles que l’agent utilisateur. Ces informations ne sont pas vérifiées. Nous recommandons d’appliquer une stratégie à toutes les plateformes. La stratégie doit bloquer l’accès, veiller à la conformité avec les stratégies Microsoft Intune ou exiger que l’appareil soit joint à un domaine. Le comportement par défaut consiste à appliquer une stratégie à toutes les plateformes d’appareils. 


![Configurer des plateformes d’appareils](./media/conditions/24.png)

Pour obtenir la liste des plateformes d’appareils prises en charge, consultez [Condition de plateforme d’appareil](technical-reference.md#device-platform-condition).


Un cas d’usage commun pour cette condition est une stratégie qui limite l’accès à vos applications cloud aux [appareils gérés](require-managed-devices.md). Pour d’autres scénarios, y compris les conditions de plateforme d’appareil, consultez [Accès conditionnel basé sur les applications Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>État de l’appareil

La condition d’état de l’appareil exclut les appareils hybrides joints à Azure AD et les appareils marqués comme conformes d’une stratégie d’accès conditionnel. 


![Configurer l’état de l’appareil](./media/conditions/112.png)

Cette condition s’avère utile quand une stratégie doit s’appliquer uniquement un appareil non géré pour des raisons de renforcement de la sécurité des sessions. Par exemple, appliquez uniquement le contrôle de session Microsoft Cloud App Security quand un appareil n’est pas géré. 

## <a name="locations"></a>Emplacements

En utilisant les emplacements, vous pouvez définir des conditions en fonction de l’endroit où une connexion a été tentée. 

![Configurer les emplacements](./media/conditions/25.png)

Les cas d’utilisation courants pour cette condition sont des stratégies qui offrent les protections suivantes :

- Exigez l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise.  

- Bloquez l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques. 

Pour plus d’informations, consultez [Qu’est-ce que la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Applications clientes

Par défaut, une stratégie d’accès conditionnel s’applique aux applications suivantes :

- **[Applications de navigateur](technical-reference.md#supported-browsers)** - Parmi les applications de navigateur figurent les sites web qui utilisent le protocole d’authentification unique web SAML, WS-Federation ou OpenID Connect. Cette stratégie s’applique également à tout site ou service web inscrit en tant que client confidentiel OAuth. Par exemple : le site web Office 365 SharePoint. 

- **[Applications de bureau et mobiles utilisant l’authentification moderne](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - Parmi ces applications figurent les applications de bureau et de téléphone Office. 


Vous pouvez également cibler une stratégie à des applications clientes spécifiques qui n’utilisent pas l’authentification moderne, par exemple :

- **[Clients Exchange ActiveSync](conditions.md#exchange-activesync-clients)** - Quand une stratégie bloque l’utilisation d’Exchange ActiveSync, les utilisateurs affectés reçoivent un e-mail de quarantaine leur indiquant pourquoi ils sont bloqués. Si nécessaire, l’e-mail fournit des instructions sur l’inscription d’appareils à Intune.

- **[Autres clients](block-legacy-authentication.md)** - Parmi ces applications figurent les clients qui utilisent l’authentification de base avec des protocoles de messagerie comme IMAP, MAPI, POP et SMTP ainsi que les applications Office plus anciennes qui n’utilisent pas l’authentification moderne. Pour plus d’informations, consultez [Fonctionnement de l’authentification moderne pour les applications clientes Office 2013 et Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Applications clientes](./media/conditions/41.png)

Les cas d’utilisation courants pour cette condition sont des stratégies avec les exigences suivantes :

- **[Exiger un appareil géré](require-managed-devices.md)** pour les applications mobiles et de bureau qui téléchargent des données sur un appareil. En même temps, autorisez l’accès au navigateur à partir de n’importe quel appareil. Ce scénario empêche l’enregistrement et la synchronisation des documents sur un appareil non géré. Avec cette méthode, vous pouvez réduire le risque de perte de données si l’appareil est perdu ou volé.

- **[Exiger un appareil géré](require-managed-devices.md)** pour les applications utilisant ActiveSync pour accéder à Exchange Online.

- **[Bloquer l’authentification héritée](block-legacy-authentication.md)** à Azure AD (autres clients)

- Bloquez l’accès aux applications web, mais autorisez l’accès à partir des applications de bureau et mobiles.



### <a name="exchange-activesync-clients"></a>Clients Exchange ActiveSync

Vous pouvez uniquement sélectionner **Clients Exchange ActiveSync** si :


- Microsoft Office 365 Exchange Online est la seule application cloud que vous avez sélectionnée.

    ![Applications cloud](./media/conditions/32.png)

- Vous n’avez pas d’autres conditions configurées dans une stratégie. Vous pouvez toutefois limiter l’étendue de cette condition pour qu’elle ne s’applique qu’aux [plateformes prises en charge](technical-reference.md#device-platform-condition).
 
    ![Appliquer la stratégie uniquement aux plateformes prises en charge](./media/conditions/33.png)


Quand l’accès est bloqué du fait qu’un [appareil géré](require-managed-devices.md) est exigé, les utilisateurs affectés reçoivent un message les invitant à utiliser Intune. 

Si une application approuvée est exigée, les utilisateurs affectés reçoivent des instructions pour installer et utiliser le client mobile Outlook.

Dans d’autres cas, par exemple si MFA est exigé, les utilisateurs affectés sont bloqués dans la mesure où les clients utilisant l’authentification de base ne prennent pas en charge MFA.

Vous pouvez cibler uniquement ce paramètre à des utilisateurs et à des groupes. Il ne prend pas en charge les invités et les rôles. Si une condition relative à un invité ou un rôle est configurée, tous les utilisateurs sont bloqués dans la mesure où l’accès conditionnel ne peut pas déterminer si la stratégie doit s’appliquer à l’utilisateur ou non.


 Pour plus d'informations, consultez les pages suivantes :

- [Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)
 
- [Accès conditionnel basé sur les applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 



## <a name="next-steps"></a>Étapes suivantes

- Pour apprendre à configurer une stratégie d’accès conditionnel, consultez [Démarrage rapide : Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).

- Pour configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md). 

