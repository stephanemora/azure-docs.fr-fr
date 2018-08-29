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
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 9feb6ef5b708813c2f73a70a930cabfd69dff114
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143469"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Que sont les conditions dans l’accès conditionnel Azure Active Directory ? 

Vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud avec l’[accès conditionnel Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Dans une stratégie d’accès conditionnel, vous définissez la réponse (« Ensuite faire ») sur la raison du déclenchement de votre stratégie (« Quand cela se produit »). 

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

* L’option **Tous les utilisateurs invités** permet de cibler une stratégie sur des utilisateurs invités B2B. Cette condition recherche tous les comptes d’utilisateur dont l’attribut **userType** a la valeur **invité**. Vous pouvez utiliser ce paramètre quand une stratégie doit être appliquée dès la création du compte dans un flux d’invitation dans Azure AD.

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

- Applications cloud individuelles pour cibler des services spécifiques par stratégie. Par exemple, vous pouvez demander aux utilisateurs d’avoir un [Appareil conforme](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) pour accéder à SharePoint Online. Cette stratégie s’applique aussi à d’autres services lorsqu’ils accèdent à des contenus SharePoint. Microsoft Teams en est un exemple. 

Vous pouvez exclure des applications spécifiques d’une stratégie. Toutefois, ces applications sont toujours soumises aux stratégies appliquées aux services auxquels elles accèdent. 



## <a name="sign-in-risk"></a>Risque à la connexion

Un risque à la connexion est une indication de la probabilité (haute, moyenne ou faible) qu’une tentative de connexion n’émane pas du propriétaire légitime d’un compte d’utilisateur. Azure AD calcule le niveau de risque à la connexion lors de la connexion d’un utilisateur. Vous pouvez utiliser le niveau de risque de connexion calculé en tant que condition dans une stratégie d’accès conditionnel.

![Niveaux de risque à la connexion](./media/conditions/22.png)

Pour utiliser cette condition, vous devez avoir [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) activé.
 
Les cas d’utilisation courants pour cette condition sont des stratégies qui offrent les protections suivantes : 

- Bloquer les utilisateurs présentant un risque élevé. Cette protection empêche les utilisateurs potentiellement illégitimes d’accéder à vos applications cloud. 
- Exiger une authentification multifacteur pour les utilisateurs présentant un risque moyen. En appliquant l’authentification multifacteur, vous pouvez fournir l’assurance supplémentaire que la connexion est effectuée par le propriétaire légitime d’un compte.

Pour plus d’informations, consultez [Connexions risquées](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins).  

## <a name="device-platforms"></a>Plateformes d’appareils

La plateforme d’appareils se caractérise par le système d’exploitation qui s’exécute sur votre appareil. Azure AD identifie la plateforme à l’aide des informations fournies par l’appareil, telles que l’agent utilisateur. Ces informations ne sont pas vérifiées. Nous recommandons d’appliquer une stratégie à toutes les plateformes. La stratégie doit bloquer l’accès, veiller à la conformité avec les stratégies Microsoft Intune ou exiger que l’appareil soit joint à un domaine. Le comportement par défaut consiste à appliquer une stratégie à toutes les plateformes d’appareils. 


![Configurer des plateformes d’appareils](./media/conditions/24.png)

Pour obtenir la liste des plateformes d’appareils prises en charge, consultez [Condition de plateforme d’appareil](technical-reference.md#device-platform-condition).


Un cas d’usage commun pour cette condition est une stratégie qui limite l’accès à vos applications cloud aux [appareils gérés](require-managed-devices.md). Pour d’autres scénarios, y compris les conditions de plateforme d’appareil, consultez [Accès conditionnel basé sur les applications Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>État de l’appareil

La condition d’état de l’appareil exclut les appareils hybrides joints à Azure AD et les appareils marqués comme conformes d’une stratégie d’accès conditionnel. Cette condition s’avère utile quand une stratégie doit s’appliquer uniquement un appareil non géré pour des raisons de renforcement de la sécurité des sessions. Par exemple, appliquez uniquement le contrôle de session Microsoft Cloud App Security quand un appareil n’est pas géré. 


![Configurer l’état de l’appareil](./media/conditions/112.png)

Pour bloquer l’accès des appareils non gérés, implémentez un [accès conditionnel en fonction de l’appareil](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Emplacements

En utilisant les emplacements, vous pouvez définir des conditions en fonction de l’endroit où une connexion a été tentée. 

![Configurer les emplacements](./media/conditions/25.png)

Les cas d’utilisation courants pour cette condition sont des stratégies qui offrent les protections suivantes :

- Exigez l’authentification multifacteur pour les utilisateurs accédant à un service lorsqu’ils sont en dehors du réseau d’entreprise.  

- Bloquez l’accès pour les utilisateurs accédant à un service à partir de pays ou régions spécifiques. 

Pour plus d’informations, consultez [Qu’est-ce que la condition d’emplacement dans l’accès conditionnel Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Applications clientes

En utilisant la condition d’applications client, vous pouvez appliquer une stratégie à différents types d’applications. Il peut s’agir de sites web, de services, d’applications mobiles et d’applications de bureau. 



Une application entre dans les catégories suivantes :

- Site web ou service, si elle utilise les protocoles d’authentification unique web, SAML, WS-Fed ou OpenID Connect pour un client confidentiel.

- Application mobile ou de bureau, si elle utilise l’application mobile OpenID Connect pour un client natif.

Pour obtenir la liste des applications clientes utilisables dans une stratégie d’accès conditionnel, consultez [Client apps condition](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) (Condition des applications clientes) dans la référence technique sur l’accès conditionnel Azure Active Directory.

Les cas d’utilisation courants pour cette condition sont des stratégies qui offrent les protections suivantes : 

- Exigez un [appareil conforme](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) pour les applications mobiles et de bureau qui téléchargent de grandes quantités de données sur l’appareil. En même temps, autorisez l’accès au navigateur à partir de n’importe quel appareil.

- Bloquez l’accès aux applications web, mais autorisez l’accès à partir des applications de bureau et mobiles.

Vous pouvez appliquer cette condition aux protocoles d’authentification modernes et d’authentification unique web. Vous pouvez également l’appliquer aux applications de messagerie qui utilisent Microsoft Exchange ActiveSync. Par exemple, aux applications de messagerie natives sur la plupart des smartphones. 

Vous ne pouvez choisir la condition d’applications clientes que si Microsoft Office 365 Exchange Online est la seule application cloud que vous avez sélectionnée.

![Applications cloud](./media/conditions/32.png)

La sélection d’**Exchange ActiveSync** comme condition d’applications clientes n’est prise en charge que si vous n’avez pas d’autres conditions configurées dans une stratégie. Vous pouvez toutefois limiter l’étendue de cette condition pour qu’elle ne s’applique qu’aux plateformes prises en charge.

 
![Appliquer la stratégie uniquement aux plateformes prises en charge](./media/conditions/33.png)

L’application de cette condition uniquement aux plateformes prises en charge équivaut à toutes les plateformes d’appareils dans une [condition de plateforme d’appareil](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Configurer des plateformes d’appareils](./media/conditions/34.png)


 Pour plus d’informations, voir les articles suivants :

- [Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)
 
- [Accès conditionnel basé sur les applications Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 


### <a name="legacy-authentication"></a>Authentification héritée  

L’accès conditionnel s’applique désormais aux clients Microsoft Office plus anciens qui ne prennent pas en charge l’authentification moderne. Il s’applique également aux clients qui utilisent des protocoles de messagerie tels que POP, IMAP et SMTP. À l’aide de l’authentification héritée, vous pouvez configurer des stratégies, telles que **bloquer l’accès à partir d’autres clients**.


![Configurer les applications clientes](./media/conditions/160.png)  


#### <a name="known-issues"></a>Problèmes connus

- La configuration d’une stratégie pour **d’autres clients** bloque l’organisation entière à partir de certains clients tels que SPConnect. Ce blocage se produit, car les clients plus anciens s’authentifient de manière inattendue. Ce problème ne concerne pas aux principales applications Office, telles que les anciens clients Office. 

- L’entrée en vigueur de la stratégie peut prendre jusqu’à 24 heures. 


#### <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q :** Cette authentification bloquera-t-elle Microsoft Exchange Web Services ?

Cela dépend du protocole d’authentification utilisé par Exchange Web Services. Si l’application Exchange Web Services utilise une authentification moderne, elle est couverte par l’application cliente **Applications mobiles et clients de bureau**. L’authentification de base est couverte par l’application cliente **Autres clients**.


**Q :** Quels contrôles puis-je utiliser pour **Autres clients** ?

N’importe quel contrôle peut être configuré pour **Autres clients**. Toutefois, l’expérience de l’utilisateur final bloquera l’accès dans tous les cas. Les **Autres clients** ne gèrent pas les contrôles tels que l’authentification multifacteur, la conformité de l’appareil et la jonction de domaine. 
 
**Q :** Quelles conditions puis-je utiliser pour **Autres clients** ?

N’importe quelle condition peut être configurée pour **Autres clients**.

**Q :** Exchange ActiveSync prend-il en charge toutes les conditions et tous les contrôles ?

Non. La liste suivante résume la prise en charge Exchange ActiveSync : 

- Exchange ActiveSync prend uniquement en charge le ciblage de groupe et d’utilisateur. Il ne prend pas en charge les invités et les rôles. Si une condition d’invité ou de rôle est configurée, tous les utilisateurs sont bloqués. Exchange ActiveSync bloque tous les utilisateurs, car il ne peut pas déterminer si la stratégie doit s’appliquer à l’utilisateur ou non.

- Exchange ActiveSync fonctionne uniquement avec Microsoft Exchange Online en tant qu’application cloud. 

- Exchange ActiveSync ne prend pas en charge les conditions, à l’exception de l’application cliente elle-même. 

- Exchange ActiveSync peut être configuré avec n’importe quel contrôle. Tous les contrôles, à l’exception de la conformité des appareils, entraînent un blocage.

**Q :** Les stratégies s’appliquent-elles par défaut à toutes les applications clientes à partir de maintenant ?

Non. Il n’existe aucun changement de comportement dans la stratégie par défaut. Les stratégies continuent de s’appliquer au navigateur et aux applications mobiles/clients bureau par défaut.



## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Démarrage rapide - Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).

- Pour configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md). 

