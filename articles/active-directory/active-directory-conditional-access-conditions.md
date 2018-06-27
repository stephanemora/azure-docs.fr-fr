---
title: Que sont les conditions dans l’accès conditionnel Azure Active Directory ? | Microsoft Docs
description: Découvrez comment les conditions sont utilisées dans l’accès conditionnel Azure Active Directory pour déclencher une stratégie.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 42792170593dbd94d0eae9b408c70f326891508a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232020"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Que sont les conditions dans l’accès conditionnel Azure Active Directory ? 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Dans une stratégie d’accès conditionnel, vous définissez la réponse (« faire ») sur la raison du déclenchement de votre stratégie (« quand cela se produit »). 

![Contrôle](./media/active-directory-conditional-access-conditions/10.png)


Dans le contexte de l’accès conditionnel :

- « **When this happens** » est une **instruction de condition**. 
- « **Faire** » est un **contrôle d’accès**.

Une stratégie d’accès conditionnel combine une condition à des contrôles d’accès.

![Contrôle](./media/active-directory-conditional-access-conditions/61.png)


Les conditions que vous n’avez pas configurées dans une stratégie d’accès conditionnel ne sont pas appliquées. Certaines conditions sont [obligatoires](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) pour appliquer une stratégie d’accès conditionnel à un environnement. 

Cet article vous donne une vue d’ensemble des conditions et de la façon dont elles sont utilisées dans une stratégie d’accès conditionnel. 

## <a name="users-and-groups"></a>Utilisateurs et groupes

La condition d’utilisateurs et de groupes est obligatoire dans une stratégie d’accès conditionnel. Dans votre stratégie, vous pouvez soit sélectionner **Tous les utilisateurs** soit sélectionner des utilisateurs et groupes spécifiques.

![Contrôle](./media/active-directory-conditional-access-conditions/111.png)

Lorsque vous sélectionnez :

- **Tous les utilisateurs**, votre stratégie est appliquée à tous les utilisateurs de l’annuaire. Cela inclut les utilisateurs invités.

- Lorsque vous **sélectionnez des utilisateurs et des groupes**, vous pouvez définir les options suivantes :

    - **Tous les utilisateurs invités** : vous permet de cibler une stratégie sur des utilisateurs invités B2B. Cette condition recherche tous les comptes d’utilisateur dont l’attribut *userType* a la valeur *invité*. Vous pouvez utiliser ce paramètre quand une stratégie doit être appliquée dès que le compte est créé dans un flux d’invitation dans Azure AD.

    - **Rôles d’annuaire** : vous permet de cibler une stratégie selon l’attribution de rôle d’un utilisateur. Cette condition prend en charge les rôles d’annuaire comme *administrateur général* ou *administrateur de mots de passe*.

    - **Utilisateurs et groupes** : vous permet de cibler des ensembles d’utilisateurs spécifiques. Par exemple, vous pouvez sélectionner un groupe qui contient tous les membres du département Ressources humaines, lorsque vous avez une application RH sélectionnée en tant qu’application cloud.

Un groupe, qui peut être n’importe quel type de groupe dans Azure AD, y compris les groupes de sécurité et de distribution dynamiques ou affectés.

Vous pouvez également exclure des utilisateurs ou groupes spécifiques d’une stratégie. Les comptes de service sont un cas d’utilisation courant si votre stratégie met en œuvre l’authentification multifacteur (MFA). 

Le ciblage d’ensembles spécifiques d’utilisateurs est utile pour le déploiement d’une nouvelle stratégie. Dans une nouvelle stratégie, vous devez cibler uniquement un ensemble initial d’utilisateurs pour valider le comportement de stratégie. 



## <a name="cloud-apps"></a>Applications cloud 

Une application cloud est un site web ou un service. Cela inclut les sites web protégés par le proxy d’application Azure. Pour une description détaillée des applications cloud prises en charge, consultez [Affectation des applications cloud](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

La condition d’applications de cloud est obligatoire dans une stratégie d’accès conditionnel. Dans votre stratégie, vous pouvez soit sélectionner **Toutes les applications cloud** soit sélectionner des applications spécifiques.

![Contrôle](./media/active-directory-conditional-access-conditions/03.png)

Vous pouvez sélectionner :

- **Toutes les applications cloud** pour les stratégies de base à appliquer à toute l’organisation. Un cas d’usage courant pour cette sélection est une stratégie qui requiert l’authentification multifacteur lorsqu’un risque d’ouverture de session est détecté sur quelle application cloud. Une stratégie appliquée à **Toutes les applications cloud** concerne l’accès à tous les services et sites web. Ce paramètre n’est pas limité aux applications cloud de la liste **Sélection des applications cloud**.

- Applications cloud individuelles pour cibler des services spécifiques par stratégie. Par exemple, vous pouvez demander aux utilisateurs d’avoir un [Appareil conforme](active-directory-conditional-access-policy-connected-applications.md) pour accéder à SharePoint Online. Cette stratégie s’applique aussi à d’autres services lorsqu’ils accèdent à des contenus SharePoint, par exemple, Microsoft Teams. 

Vous pouvez également exclure des applications spécifiques d’une stratégie. Toutefois, ces applications sont toujours soumis aux stratégies appliquées aux services auxquels elles accèdent. 



## <a name="sign-in-risk"></a>Risque à la connexion

Un risque de connexion est une indication de la probabilité (haute, moyenne ou faible) qu’une tentative de connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur. Azure AD calcule le niveau de risque de connexion lors de la connexion d’un utilisateur. Vous pouvez utiliser le niveau de risque de connexion calculé en tant que condition dans une stratégie d’accès conditionnel. 

![Conditions](./media/active-directory-conditional-access-conditions/22.png)

Pour utiliser cette condition, vous devez avoir [Azure Active Directory Identity Protection](active-directory-identityprotection.md) activé.
 
Les cas d’utilisation courants pour cette condition sont des stratégies qui :

- Empêchez les utilisateurs avec un risque élevé de se connecter pour empêcher de potentiels utilisateurs non légitimes d’accéder à vos applications cloud. 
- Exigez l’authentification multifacteur pour les personnes présentant un risque de connexion moyen. En appliquant l’authentification multifacteur, vous pouvez fournir l’assurance supplémentaire que la connexion est effectuée par le propriétaire légitime d’un compte.

Pour plus d’informations, consultez [Connexions risquées](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Plateformes d’appareils

La plateforme d’appareils se caractérise par le système d’exploitation qui s’exécute sur votre appareil. Azure AD identifie la plateforme à l’aide des informations fournies par l’appareil, telles que l’agent utilisateur. Étant donné que ces informations ne sont pas vérifiées, il est recommandé que toutes les plateformes aient une stratégie appliquée, soit en bloquant l’accès, ce qui nécessite la compatibilité avec les stratégies Intune soit en nécessitant que l’appareil soit joint à un domaine. La valeur par défaut est d’appliquer la stratégie à toutes les plateformes d’appareils. 


![Conditions](./media/active-directory-conditional-access-conditions/24.png)

Pour obtenir la liste complète des plateformes d’appareils prises en charge, consultez [Condition de plateforme d’appareil](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Un cas d’usage commun pour cette condition est une stratégie qui limite l’accès à vos applications cloud aux [appareils gérés](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Pour d’autres scénarios, y compris les conditions de plateforme d’appareil, consultez [Accès conditionnel basé sur les applications Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>État de l’appareil

La condition d’état de l’appareil permet d’exclure des appareils joints à Azure AD hybrides marqués comme conformes d’une stratégie d’accès conditionnel. Cette exclusion s’avère utile quand une stratégie doit s’appliquer uniquement à des appareils non gérés pour des raisons de renforcement de la sécurité des sessions. Par exemple, appliquez uniquement le contrôle de session Microsoft Cloud App Security quand un appareil n’est pas géré. 


![Conditions](./media/active-directory-conditional-access-conditions/112.png)

Pour bloquer l’accès des appareils non gérés, vous devez implémenter un [accès conditionnel en fonction de l’appareil](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Emplacements

Les emplacements vous permettent de définir des conditions en fonction de l’endroit à partir duquel a été effectuée une tentative de connexion. 
     
![Conditions](./media/active-directory-conditional-access-conditions/25.png)

Les cas d’utilisation courants pour cette condition sont des stratégies qui :

- Exigez l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise.  

- Bloquez l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques. 

Pour plus d’informations, consultez [Qu’est-ce que la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Applications clientes

La condition d’applications client vous permet d’appliquer une stratégie à différents types d’applications, par exemple :

- Sites et services web
- Applications mobiles et de bureau. 



Une application est classée comme :

- Un site ou service web si elle utilise le protocole d’authentification unique, SAML, WS-Fed ou OpenID Connect pour un client confidentiel.

- Une application de bureau si elle utilise l’application mobile OpenID Connect pour un client natif.

Pour obtenir la liste complète des applications clientes utilisables dans une stratégie d’accès conditionnel, consultez [Client apps condition](active-directory-conditional-access-technical-reference.md#client-apps-condition) (Condition des applications clientes) dans la référence technique sur l’accès conditionnel Azure Active Directory.

Les cas d’utilisation courants pour cette condition sont des stratégies qui :

- Exigez un [appareil conforme](active-directory-conditional-access-policy-connected-applications.md) pour les applications mobiles et de bureau qui téléchargent de grandes quantités de données sur l’appareil, tout en autorisant l’accès par navigateur à partir de n’importe quel appareil.

- Bloquez l’accès aux applications web, mais autorisez l’accès à partir des applications de bureau et mobiles.

Outre l’utilisation de l’authentification unique web et des protocoles d’authentification modernes, vous pouvez appliquer cette condition aux applications de messagerie qui utilisent Exchange ActiveSync, comme les applications de messagerie natives sur la plupart des smartphones. Actuellement, les applications clientes utilisant des protocoles hérités doivent être sécurisées à l’aide d’AD FS.

Vous ne pouvez sélectionner cette condition que si **Office 365 Exchange Online** est la seule application cloud que vous avez sélectionnée.

![Applications cloud](./media/active-directory-conditional-access-conditions/32.png)

La sélection d’**Exchange ActiveSync** comme condition des applications clientes n’est prise en charge que si vous n’avez pas d’autres conditions configurées dans une stratégie. Vous pouvez toutefois limiter l’étendue de cette condition pour qu’elle ne s’applique qu’aux plateformes prises en charge.

 
![Plateformes prises en charge](./media/active-directory-conditional-access-conditions/33.png)

L’application de cette condition aux plateformes prises en charge uniquement équivaut à toutes les plateformes d’appareils dans un [condition de plateforme d’appareil](active-directory-conditional-access-conditions.md#device-platforms).

![Plateformes prises en charge](./media/active-directory-conditional-access-conditions/34.png)


 Pour plus d'informations, consultez les pages suivantes :

- [Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Accès conditionnel basé sur les applications Azure Active Directory](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Authentification héritée  

L’accès conditionnel s’applique désormais aux anciens clients Office qui ne prennent pas en charge l’authentification moderne, ainsi qu’aux clients qui utilisent des protocoles de messagerie tels que POP, IMAP, SMTP, etc. Cela vous permet de configurer des stratégies telles que **bloquer l’accès d’autres clients**.


![Authentification héritée](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Problèmes connus

- La configuration d’une stratégie pour **d’autres clients** bloque l’organisation entière à partir de certains clients tels que SPConnect. Ceci est dû au fait que ces anciens clients s’authentifient de manière inattendue. Ce problème ne s’applique pas aux principales applications Office telles que les anciens clients Office. 

- L’entrée en application de la stratégie peut prendre jusqu’à 24 heures. 


#### <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Exchange Web Services (EWS) sera-t-il bloqué ?**

Cela dépend du protocole d’authentification utilisé par EWS. Si l’application EWS utilise une authentification moderne, elle sera couverte par l’application cliente « Applications mobiles et clients de bureau ». Si l’application EWS utilise une authentification de base, elle sera couverte par l’application cliente « Autres clients ».


**Quels contrôles puis-je utiliser pour les autres clients ?**

N’importe quel contrôle peut être configuré pour « Autres clients ». Toutefois, l’expérience de l’utilisateur final bloquera l’accès dans tous les cas. Les « Autres clients » ne gèrent pas les contrôles tels que l’authentification multifacteur, la conformité de l’appareil, la jonction de domaine, etc. 
 
**Quelles conditions puis-je utiliser pour les autres clients ?**

N’importe quelle condition peut être configurée pour « Autres clients ».

**Exchange ActiveSync prend-il en charge toutes les conditions et tous les contrôles ?**

Non. Voici le résumé de la prise en charge Exchange ActiveSync (EAS) :

- EAS prend uniquement en charge le ciblage d’utilisateur et de groupe. Il ne prend pas en charge les invités et les rôles. Si la condition invité/rôle est configurée, tous les utilisateurs seront bloqués dans la mesure où nous ne pouvons pas déterminer si la stratégie doit s’appliquer à l’utilisateur ou non.

- EAS ne fonctionne qu’avec Exchange en tant qu’application cloud. 

- EAS ne prend pas en charge toute condition à l’exception de l’application cliente elle-même.

- EAS peut être configuré avec n’importe quel contrôle (tous sauf la conformité de l’appareil qui entraîne un blocage).

**Les stratégies s’appliquent-elles par défaut à toutes les applications clientes à venir ?**

Non. Il n’existe aucun changement de comportement dans la stratégie par défaut. Les stratégies continuent de s’appliquer au navigateur et aux applications mobiles/clients bureau par défaut.



## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 

