---
title: Comment exiger la stratégie de protection des applications pour accéder aux applications de cloud avec l’accès conditionnel dans Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: f695d50e251d0104cf9f0d38fe4489a0e66dfe15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288500"
---
# <a name="how-to-require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Procédure : Exiger la stratégie de protection des applications pour accéder aux applications de cloud avec l’accès conditionnel (version préliminaire)

Vos employés utilisent des appareils mobiles pour des tâches à la fois personnelles et professionnelles. Tout en veillant à ce que vos employés restent productifs, vous voulez aussi éviter toute perte de données. Avec l’accès conditionnel Azure Active Directory (Azure AD), vous pouvez protéger vos données d’entreprise en limitant l’accès à vos applications cloud à des applications clientes qui ont une stratégie de protection d’application initial.

Cette rubrique explique comment configurer des stratégies d’accès conditionnel qui peuvent nécessiter de stratégie de protection des applications avant l’accès aux données.

## <a name="overview"></a>Présentation

Avec l’[accès conditionnel Azure AD](overview.md), vous pouvez préciser les méthodes d’accès des utilisateurs autorisés aux ressources. Par exemple, vous pouvez limiter l’accès à vos applications cloud aux appareils approuvés.

Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise. Les stratégies de protection des applications Intune n’exigent pas de solution de gestion des appareils mobiles (MDM), ce qui vous permet de protéger les données de votre entreprise avec ou sans inscription des appareils dans une solution de gestion des appareils.

Accès conditionnel Azure Active Directory limite l’accès à vos applications cloud aux applications clientes qui Intune a signalé à Azure AD comme étant la réception d’une stratégie de protection d’application. Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook qui a une stratégie de protection d’application Intune.

Dans la terminologie de l’accès conditionnel, ces applications clientes sont connues pour être stratégie protégé avec un **stratégie de protection des applications**.  

![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)

Pour obtenir la liste de stratégie des applications client protégé, consultez [exigence de stratégie de protection application](technical-reference.md#approved-client-app-requirement).

Vous pouvez combiner les app-protection-stratégies d’accès conditionnel avec d’autres stratégies telles que [stratégies d’accès conditionnel basé sur un appareil](require-managed-devices.md) pour fournir une flexibilité dans la protection des données pour les appareils personnels et d’entreprise.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Avantages de la spécification de l’accès conditionnel basé sur la protection d’application

Similaire à la conformité signalé par Intune pour iOS et Android pour périphérique géré, Intune désormais des rapports à Azure AD si la stratégie de protection des applications est appliquée afin que l’accès conditionnel peut utiliser cela comme une vérification d’accès. Cette nouvelle stratégie d’accès conditionnel **stratégie de protection des applications** augmente la sécurité en protégeant contre les erreurs d’administration telles que :

- utilisateurs qui n’ont pas d’une licence Intune
- utilisateurs qui ne peut pas recevoir une stratégie de protection d’application Intune
- Stratégie Intune app protection des applications qui n’ont pas été configurées pour recevoir une stratégie


## <a name="before-you-begin"></a>Avant de commencer

Cette rubrique part du principe que vous connaissez :

- Le [exigence de stratégie de protection application](technical-reference.md#app-protection-policy-requirement) informations techniques de référence.

- Les informations techniques de référence intitulées [Spécification d’application cliente approuvée](technical-reference.md#approved-client-app-requirement).

- Les concepts de base de l’[accès conditionnel dans Azure Active Directory](overview.md)

- Comment [configurer votre stratégie d’accès conditionnel](app-based-mfa.md)


## <a name="prerequisites"></a>Conditions préalables

Pour créer une stratégie d’accès conditionnel basé sur la protection des applications, vous devez
- Avez Enterprise Mobility + Security ou un abonnement premium de Azure Active Directory + Intune
- Garantir que les utilisateurs sont des licences pour EMS ou Azure AD + Intune
- Vérifiez que l’application cliente a été configurée dans Intune pour recevoir une stratégie de protection d’application
- Vérifiez que les utilisateurs sont configurés dans Intune pour recevoir une stratégie de protection d’application Intune

## <a name="app-protection-based-policy-for-exchange-online"></a>Stratégie basée sur la protection des applications pour Exchange Online

Ce scénario se compose d’une stratégie d’accès conditionnel basé sur la protection application pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange

- Reçoit un e-mail qui indique que l’accès est disponible uniquement à l’aide de l’application Outlook

- Télécharge l’application avec le lien

- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD

- Est invité à installer Authenticator (iOS) ou le Portail d’entreprise (Android) pour continuer

- Installe l’application et peut revenir à l’application Outlook pour continuer

- Est invité à inscrire un appareil

- Est en mesure de recevoir une stratégie de protection d’application Intune

- Est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune doivent se trouver sur l’application d’accéder aux données d’entreprise et peuvent inviter l’utilisateur à redémarrer l’application, utilisez un etc. PIN supplémentaires (si configuré pour l’application et la plateforme).

### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/01.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :

    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et applications de bureau** et **Clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. En tant que **contrôles d’accès**, vous devez avoir **nécessitent la stratégie de protection des applications (version préliminaire)** sélectionné.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)
 

**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.


3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. **Conditions :** Comme **Conditions**, configurez **Applications clientes (préversion)**. 

    a. Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

    b. En tant que **contrôles d’accès**, vous devez avoir **nécessitent la stratégie de protection des applications (version préliminaire)** sélectionné.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)


**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Stratégie de périphérique compatible ou protection des applications pour Exchange Online

Ce scénario se compose d’une stratégie d’accès conditionnel appareil conforme ou protection en application pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe que :
 
- Un utilisateur est déjà inscrit (avec ou sans appareils d’entreprise)

- Les utilisateurs qui ne sont pas enregistrés et inscrits auprès d’Azure AD à l’aide d’une application protégée approuvée doivent inscrire un appareil pour accéder aux ressources

- Les utilisateurs inscrits à l’aide de l’application protégée approuvée ne sont pas obligés de réinscrire l’appareil

- Utilisateur peut recevoir une stratégie de protection d’application Intune si ce n’est pas inscrit

- Utilisateur peut accéder à la messagerie avec Outlook et une stratégie de protection d’application Intune si ce n’est pas inscrit

- Utilisateur peut accéder à la messagerie avec Outlook si l’appareil est inscrit.


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/62.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

     ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger une stratégie de protection des applications (préversion)**

   - **Demander un des contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/11.png)



**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. **Conditions :** Comme **Conditions**, configurez **Applications clientes**. 

    Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger une stratégie de protection des applications (préversion)**

   - **Demander un des contrôles sélectionnés**   
    ![Accès conditionnel](./media/app-protection-based-conditional-access/11.png)



**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Stratégie d’appareil basé sur la protection et conforme application pour Exchange Online

Ce scénario se compose d’une stratégie d’accès conditionnel appareil conforme et protection-en fonction des applications pour l’accès à Exchange Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :
 
-   Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
-   Reçoit un e-mail qui indique que l’accès exige l’inscription de l’appareil
-   Télécharge le portail d’entreprise et s’y connecte
-   Vérifie ses e-mails et est invité à utiliser l’application Outlook
-   Télécharge l’application Outlook
-   Ouvre l’application Outlook et entre les informations d’identification utilisées lors de l’inscription
-   Est en mesure de recevoir une stratégie de protection d’application Intune
-   Est en mesure d’accéder à la messagerie avec Outlook et une stratégie de protection d’application Intune

Les stratégies de protection d’application Intune sont activés avant de l’accès aux données d’entreprise et peuvent inviter l’utilisateur à redémarrer l’application, utilisez un etc. PIN supplémentaires (si configuré pour l’application et la plateforme)


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/01.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

     ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et applications de bureau** et **Clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Exiger une stratégie de protection des applications (préversion)**

   - **Demander tous les contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/13.png)



**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

4. **Conditions :** Comme **Conditions**, configurez **Applications clientes (préversion)**. 

    Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

   - **Exiger que l’appareil soit marqué comme conforme**

   - **Demander une application cliente approuvée (version préliminaire)**

   - **Demander tous les contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/13.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur une application ou protection d’application pour Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’applications approuvées ou basée sur la protection d’application pour accéder à Exchange Online et SharePoint Online.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure les deux applications clientes qui sont sur la liste des applications qui prennent en charge la spécification de stratégie de protection application ou l’exigence d’applications approuvées.  
- Les applications clientes utilisateur utilise qui répond à la condition de stratégie de protection application peuvent recevoir une stratégie de protection d’application Intune
- Utilisateur utilise les applications clientes qui répond à la condition de stratégie d’applications approuvées qui prend en charge de la stratégie de protection d’application Intune
- Ouvre l’application à accéder aux e-mails ou des documents
- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD
- Est invité à installer Authenticator (iOS) ou le portail d’entreprise (Android) pour continuer (si ce n’est pas installé)
- Installe l’application et peut revenir à l’application Outlook pour continuer
- Est invité à inscrire un appareil
- Est en mesure de recevoir une stratégie de protection d’application Intune
- Est en mesure d’accéder à la messagerie avec Outlook et une stratégie de protection d’application Intune
- Est en mesure d’accéder aux sites/documents avec une application pas sur le besoin de la stratégie de protection application mais répertoriés dans la demande d’application approuvée.

Les stratégies de protection d’application Intune sont exigés avant d’accéder aux données d’entreprise et peuvent inviter l’utilisateur à redémarrer l’application, utilisez un etc. PIN supplémentaires (si configuré pour l’application et la plateforme)

**Remarques**

- Vous pouvez utiliser ce scénario si vous souhaitez prendre en charge les deux stratégies de l’accès conditionnel basées sur l’application et protection des applications.

- Dans ce *ou* stratégie, les applications avec **stratégie de protection des applications** exigence sont évaluées pour l’accès avant **applications clientes approuvées** exigence.

### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/62.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

     ![Accès conditionnel](./media/app-protection-based-conditional-access/02.png)

4. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

    b. Dans **Applications clientes (préversion)**, sélectionnez **Applications mobiles et applications de bureau** et **Clients de l’authentification moderne**.

    ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

   - **Demander une application cliente approuvée**

   - **Exiger une stratégie de protection des applications (préversion)**

   - **Demander un des contrôles sélectionnés**   
 
     ![Accès conditionnel](./media/app-protection-based-conditional-access/12.png)


**Étape 2 : configurer la stratégie de protection d’application Intune pour les applications iOS et Android client**


![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).




## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).

Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md). 