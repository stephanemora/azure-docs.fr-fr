---
title: Exiger la stratégie de protection des applications pour accéder aux applications de cloud avec l’accès conditionnel dans Azure Active Directory | Microsoft Docs
description: Découvrez comment exiger la stratégie de protection des applications pour accéder aux applications de cloud avec l’accès conditionnel dans Azure Active Directory.
services: active-directory
keywords: accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 4/4/2019
ms.author: joflore
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2250449c0ef342332945b80cb10cb9a02885b259
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496928"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Exiger la stratégie de protection des applications pour accéder aux applications de cloud avec l’accès conditionnel (version préliminaire)

Vos employés utilisent des appareils mobiles pour des tâches à la fois personnelles et professionnelles. Tout en veillant à ce que vos employés restent productifs, vous voulez aussi éviter toute perte de données. Avec l’accès conditionnel Azure Active Directory (Azure AD), vous pouvez protéger vos données d’entreprise en limitant l’accès à vos applications cloud. Utilisez d’abord les applications clientes à une stratégie de protection d’application.

Cet article explique comment configurer des stratégies d’accès conditionnel qui peuvent nécessiter une stratégie de protection d’application avant de pouvoir accéder aux données.

## <a name="overview"></a>Présentation

Avec l’[accès conditionnel Azure AD](overview.md), vous pouvez préciser les méthodes d’accès des utilisateurs autorisés aux ressources. Par exemple, vous pouvez limiter l’accès à vos applications cloud aux appareils approuvés.

Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise. Stratégies de protection d’application ne nécessitent pas une solution de gestion des appareils mobiles. Vous pouvez protéger les données de votre entreprise avec ou sans l’inscription des appareils dans une solution de gestion des appareils.

Accès conditionnel Azure Active Directory limite l’accès à vos applications cloud aux applications clientes qui Intune a signalé à Azure AD comme étant la réception d’une stratégie de protection d’application. Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook qui a une stratégie de protection d’application Intune.

Dans la terminologie de l’accès conditionnel, ces applications clientes sont connues pour être stratégie protégé avec un *stratégie de protection des applications*.  

![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)

Pour obtenir la liste des applications clientes protégé par stratégie, consultez [exigence de stratégie de protection application](technical-reference.md#approved-client-app-requirement).

Vous pouvez combiner les app-protection-stratégies d’accès conditionnel avec d’autres stratégies, telles que [stratégies d’accès conditionnel basé sur le périphérique](require-managed-devices.md). De cette façon, vous pouvez fournir une flexibilité dans la protection des données pour les appareils personnels et d’entreprise.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Avantages de la spécification de l’accès conditionnel basé sur la protection d’application

Similaire à la conformité est signalée par Intune pour iOS et Android pour un appareil géré, Intune maintenant des rapports à Azure AD si une stratégie de protection d’application est appliquée. Accès conditionnel peut utiliser cette stratégie en tant qu’une vérification d’accès. Cette nouvelle stratégie d’accès conditionnel, la stratégie de protection, accroît la sécurité. Il protège contre les erreurs d’administration, telles que :

- Utilisateurs qui n’ont pas une licence Intune.
- Utilisateurs qui ne peut pas recevoir une stratégie de protection d’application Intune.
- Intune app protection stratégie des applications qui ne sont pas configurées pour recevoir une stratégie.


## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose de connaître :

- Le [exigence de stratégie de protection application](technical-reference.md#app-protection-policy-requirement) informations techniques de référence.
- Les informations techniques de référence intitulées [Spécification d’application cliente approuvée](technical-reference.md#approved-client-app-requirement).
- Les concepts de base de l’[accès conditionnel dans Azure Active Directory](overview.md)
- Comment [configurer votre stratégie d’accès conditionnel](app-based-mfa.md)


## <a name="prerequisites"></a>Conditions préalables

Pour créer une stratégie d’accès conditionnel basé sur la protection des applications, vous devez :

- Avoir un Enterprise Mobility + Security ou un abonnement premium de Azure Active Directory + Intune.
- Assurez-vous que les utilisateurs sont concédés sous licence pour Enterprise Mobility + Security ou Azure AD + Intune.
- Assurez-vous que l’application cliente est configurée dans Intune pour recevoir une stratégie de protection d’application.
- Assurez-vous que les utilisateurs sont configurés dans Intune pour recevoir une stratégie de protection d’application Intune.

## <a name="app-protection-based-policy-for-exchange-online"></a>Stratégie basée sur la protection des applications pour Exchange Online

Ce scénario se compose d’une stratégie d’accès conditionnel basé sur la protection application pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange.
- Reçoit un e-mail qui indique que l’accès est disponible uniquement à l’aide de l’application Outlook.
- Télécharge l’application avec le lien.
- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD.
- Est invité à installer Microsoft Authenticator pour une utilisation d’iOS ou portail d’entreprise Intune pour Android utilisent continuer.
- Installe l’application et retourne à l’application Outlook pour continuer.
- Est invité à inscrire un appareil.
- Peut recevoir une stratégie de protection d’application Intune.
- Peut accéder à la messagerie.

Les stratégies de protection d’application Intune doivent être sur l’application pour accéder aux données d’entreprise. Les stratégies peuvent inviter l’utilisateur à redémarrer l’application ou utiliser un code PIN supplémentaire. C’est le cas si les stratégies sont configurées pour l’application et de la plateforme.

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/01.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.

3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. Dans **Conditions**, configurer **plateformes d’appareils** et **les applications clientes (version préliminaire)**:

    a. Dans **plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Sous **contrôles d’accès**, sélectionnez **nécessitent la stratégie de protection des applications (version préliminaire)**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)
 

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync (EAS)**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.


3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. Dans **Conditions**, configurer **les applications clientes (version préliminaire)**. 

    a. Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients Exchange ActiveSync**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

    b. Sous **contrôles d’accès**, sélectionnez **nécessitent la stratégie de protection des applications (version préliminaire)**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)


**Étape 3 : Configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [protéger les applications et données avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Stratégie de périphérique compatible ou protection des applications pour Exchange Online

Ce scénario se compose d’une stratégie d’accès conditionnel appareil conforme ou protection en application pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe que :
 
- Un utilisateur est déjà inscrit, avec ou sans appareils d’entreprise.
- Les utilisateurs qui ne sont pas inscrits et inscrit auprès d’Azure AD à l’aide d’une application protégée approuvée doivent inscrire un appareil pour accéder aux ressources.
- Les utilisateurs inscrits qui utilisent l’application protégée approuvée n’êtes pas obligé de réinscrire l’appareil.
- L’utilisateur peut recevoir une stratégie Intune app protection si elle n’est pas inscrit.
- L’utilisateur peut accéder à la messagerie avec Outlook et une stratégie Intune app protection si elle n’est pas inscrit.
- L’utilisateur peut accéder à la messagerie avec Outlook si l’appareil est inscrit.


### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/62.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.

3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**. 

     ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. Dans **Conditions**, configurer **plateformes d’appareils** et **les applications clientes (version préliminaire)**. 
 
    a. Dans **plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Sous **contrôles d’accès**, sélectionnez les options suivantes :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger la stratégie de protection des applications (version préliminaire)**

   - **Demander un des contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/11.png)



**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.

3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. Dans **Conditions**, configurer **les applications clientes (version préliminaire)**. 

    Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients Exchange ActiveSync**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

5. Sous **contrôles d’accès**, sélectionnez les options suivantes :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger la stratégie de protection des applications (version préliminaire)**

   - **Demander un des contrôles sélectionnés**

     ![Accès conditionnel](./media/app-protection-based-conditional-access/11.png)



**Étape 3 : Configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [protéger les applications et données avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Stratégie d’appareil basé sur la protection et conforme application pour Exchange Online

Ce scénario se compose d’une stratégie d’accès conditionnel appareil conforme et protection-en fonction des applications pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :
 
-   Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange.
-   Reçoit un e-mail qui indique que l’accès exige leur appareil à inscrire.
-   Téléchargements du portail d’entreprise Intune et se connecte au portail.
-   Vérifie ses e-mails et est invité à utiliser l’application Outlook.
-   Télécharge l’application Outlook.
-   Ouvre l’application Outlook et entre les informations d’identification utilisées lors de l’inscription.
-   Peut recevoir une stratégie de protection d’application Intune.
-   Peut accéder à la messagerie avec Outlook et une stratégie de protection d’application Intune.

Les stratégies de protection d’application Intune sont activés avant de pouvoir accéder aux données d’entreprise. Les stratégies peuvent inviter l’utilisateur à redémarrer l’application ou utiliser un code PIN supplémentaire. C’est le cas si les stratégies sont configurées pour l’application et de la plateforme.


### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/01.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.

3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**. 

     ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. Dans **Conditions**, configurer **plateformes d’appareils** et **les applications clientes (version préliminaire)**. 
 
    a. Dans **plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Sous **contrôles d’accès**, sélectionnez les options suivantes :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger la stratégie de protection des applications (version préliminaire)**

   - **Demander tous les contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/13.png)



**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.

3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. Dans **Conditions**, configurer **les applications clientes (version préliminaire)**. 

    Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients Exchange ActiveSync**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

5. Sous **contrôles d’accès**, sélectionnez les options suivantes :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger la stratégie de protection des applications (version préliminaire)**

   - **Demander tous les contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/13.png)




**Étape 3 : Configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [protéger les applications et données avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur une application ou protection d’application pour Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’applications approuvées ou basée sur la protection d’application pour accéder à Exchange Online et SharePoint Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure les applications clientes qui sont sur la liste des applications qui prennent en charge la spécification de stratégie de protection application ou l’exigence d’applications approuvées.  
- Utilise des applications clientes qui répond à la condition de stratégie de protection application et peuvent recevoir une stratégie de protection d’application Intune.
- Utilise des applications clientes qui répond à la condition de stratégie d’applications approuvées qui prend en charge de la stratégie de protection d’application Intune.
- Ouvre l’application à accéder aux e-mails ou des documents.
- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD.
- Est invité à installer Microsoft Authenticator pour une utilisation d’iOS ou portail d’entreprise Intune pour Android utilisent s’ils ne sont pas déjà installés.
- Installe l’application et peut retourne à l’application Outlook pour continuer.
- Est invité à inscrire un appareil.
- Peut recevoir une stratégie de protection d’application Intune.
- Peut accéder à la messagerie avec Outlook et une stratégie de protection d’application Intune.
- Peut accéder aux sites et documents avec une application pas sur le besoin de la stratégie de protection application mais répertoriés dans la demande d’application approuvée.

Les stratégies de protection d’application Intune sont nécessaires avant de pouvoir accéder aux données d’entreprise. Les stratégies peuvent inviter l’utilisateur à redémarrer l’application ou utiliser un code PIN supplémentaire. C’est le cas si les stratégies sont configurées pour l’application et de la plateforme.

**Remarques**

- Vous pouvez utiliser ce scénario si vous souhaitez prendre en charge les deux stratégies de l’accès conditionnel basées sur l’application et protection des applications.
- Dans ce *ou* stratégie, les applications avec une exigence de stratégie de protection application sont évaluées pour l’accès avant l’exigence d’applications clientes approuvées.

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel dans cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/62.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.

2. Sous **affectations**, dans **utilisateurs et groupes**, sélectionnez au moins un utilisateur ou le groupe pour chaque stratégie d’accès conditionnel.

3. Dans **applications Cloud**, sélectionnez **Office 365 Exchange Online**. 

     ![Accès conditionnel](./media/app-protection-based-conditional-access/02.png)

4. Dans **Conditions**, configurer **plateformes d’appareils** et **les applications clientes (version préliminaire)**. 
 
    a. Dans **plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **les applications clientes (version préliminaire)**, sélectionnez **des applications mobiles et clients de bureau** et **les clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Sous **contrôles d’accès**, sélectionnez les options suivantes :

   - **Demander une application cliente approuvée**

   - **Exiger la stratégie de protection des applications (version préliminaire)**

   - **Demander un des contrôles sélectionnés**
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/12.png)


**Étape 2 : Configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [protéger les applications et données avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md). 