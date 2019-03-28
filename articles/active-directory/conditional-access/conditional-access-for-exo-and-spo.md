---
title: Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90cd381dbe3feaad110c7f10ae328915c051d0a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517723"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Procédure : Configurer SharePoint Online et Exchange Online pour l’accès conditionnel Azure Active Directory 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](overview.md), vous pouvez contrôler la façon dont les utilisateurs accédent à vos applications cloud. Si vous voulez utiliser l’accès conditionnel pour contrôler l’accès à SharePoint et Exchange online, vous devez :

- Vérifier si votre scénario d’accès conditionnel est pris en charge.
- Empêcher les applications clientes de contourner la mise en œuvre de vos stratégies d’accès conditionnel.   

Cet article explique comment traiter ces deux cas.


## <a name="what-you-need-to-know"></a>Bon à savoir

Vous pouvez utiliser l’accès conditionnel Azure AD pour protéger les applications cloud quand une tentative d’authentification provient de :

- Un navigateur web

- Une application cliente qui utilise l’[authentification moderne](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Certaines applications cloud prennent également en charge des protocoles d’authentification hérités. C’est le cas, par exemple, de SharePoint Online et Exchange Online. Quand une application cliente peut utiliser un protocole d’authentification hérité pour accéder à une application cloud, Azure AD ne peut pas appliquer de stratégie d’accès conditionnel sur cette tentative d’accès. Pour empêcher une application cliente de contourner la mise en œuvre des stratégies, vous devez vérifier s’il est possible d’activer uniquement l’authentification moderne sur les applications cloud concernées. 

Voici des exemples d’applications clientes auxquelles l’accès conditionnel ne s’applique pas :

- Office 2010 et versions antérieures

- Office 2013 quand l’authentification moderne n’est pas activée



 
## <a name="control-access-to-sharepoint-online"></a>Contrôler l’accès à SharePoint Online

En plus de l’authentification moderne, SharePoint Online prend en charge des protocoles d’authentification hérités. Si les protocoles d’authentification hérités sont activés, vos stratégies d’accès conditionnel pour SharePoint ne sont pas appliquées pour les clients qui n’utilisent pas l’authentification moderne.

Vous pouvez désactiver les protocoles d’authentification hérités pour l’accès SharePoint à l’aide de l’applet de commande **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)**  : 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Contrôler l’accès à Exchange Online

Quand vous configurez des stratégies d’accès conditionnel pour Exchange Online, vous devez passer en revue les éléments suivants :

- Exchange ActiveSync

- Protocoles d’authentification hérités



### <a name="exchange-activesync"></a>Exchange ActiveSync

Même si Exchange Active Sync prend en charge l’authentification moderne, des limitations existent dans la prise en charge des scénarios d’accès conditionnel :

- Lorsque vous sélectionnez **clients Exchange Active Sync** dans votre stratégie, vous ne pouvez pas configurer d’autres conditions.  

    ![Plateformes d’appareils](./media/conditional-access-for-exo-and-spo/05.png)

- La définition de la spécification de l’authentification multifacteur n’est pas prise en charge  

    ![Accès conditionnel](./media/conditional-access-for-exo-and-spo/01.png)

Pour protéger efficacement l’accès à Exchange Online à partir d’Exchange ActiveSync, vous pouvez effectuer les opérations suivantes :

- Configurer une stratégie d’accès conditionnel prise en charge en procédant comme suit :

    a. Sélectionnez simplement **Office 365 Exchange Online** en tant qu’application cloud.  

    ![Accès conditionnel](./media/conditional-access-for-exo-and-spo/04.png)

    b. Sélectionnez **Exchange Active Sync** comme **application cliente**.  

    ![Plateformes d’appareils](./media/conditional-access-for-exo-and-spo/03.png)

- Bloquer Exchange ActiveSync à l’aide de règles des services de fédération Active Directory (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protocoles d’authentification hérités

En plus de l’authentification moderne, Exchange Online prend en charge des protocoles d’authentification hérités. Si les protocoles d’authentification hérités sont activés, vos stratégies d’accès conditionnel pour Exchange Online ne sont pas appliquées pour les clients qui n’utilisent pas l’authentification moderne.

Vous pouvez désactiver les protocoles d’authentification hérités pour Exchange Online en définissant des règles AD FS. Ainsi, l’accès est bloqué à partir des éléments suivants :

- Anciens clients Office, comme Office 2013, pour lesquels l’authentification moderne n’est pas activée 

- Versions antérieures d’Office


## <a name="set-up-ad-fs-rules"></a>Configurer des règles AD FS

Vous pouvez utiliser les règles d’autorisation d’émission suivantes pour activer ou bloquer le trafic au niveau AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Bloquer le trafic hérité provenant de l’extranet

En appliquant les trois règles suivantes : 

- Vous activez l’accès pour :
    - Le trafic Exchange ActiveSync
    - Le trafic de navigateur
    - Le trafic d’authentification moderne
- Vous bloquez l’accès pour : 
    - Les applications clientes héritées de l’extranet

**Règle 1 :**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Règle 2 :**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Règle 3 :**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Bloquer le trafic hérité provenant de partout

En appliquant les trois règles suivantes :

- Vous activez l’accès pour : 
    - Le trafic Exchange ActiveSync
    - Le trafic de navigateur
    - Le trafic d’authentification moderne
- Vous bloquez l’accès pour : 
    - Les applications héritées provenant de partout

##### <a name="rule-1"></a>Règle 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Règle 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Règle 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Qu’est-ce que l’accès conditionnel dans Azure Active Directory](overview.md).

Pour obtenir des instructions sur la configuration des règles de revendication, consultez [Configurer les règles de revendication](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules). 






