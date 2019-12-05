---
title: Applications clientes approuvées avec l’accès conditionnel - Azure Active Directory
description: Découvrez comment exiger des applications clientes approuvées pour l’accès aux applications cloud avec l’accès conditionnel dans Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c173d0e17166911e28fea3d1c5820879d17af4a8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381117"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Procédure : Exiger des applications clientes approuvées pour l’accès aux applications cloud avec l’accès conditionnel 

Vos employés utilisent des appareils mobiles pour des tâches à la fois personnelles et professionnelles. Tout en veillant à ce que vos employés restent productifs, vous voulez aussi éviter toute perte de données. Avec l’accès conditionnel Azure Active Directory (Azure AD), vous pouvez restreindre l’accès à vos applications cloud à des applications clientes approuvées capables de protéger vos données d’entreprise.  

Cette rubrique explique comment configurer des stratégies d’accès conditionnel qui exigent des applications clientes approuvées.

## <a name="overview"></a>Vue d'ensemble

Avec l’[accès conditionnel Azure AD](overview.md), vous pouvez préciser la façon dont chaque utilisateur autorisé peut accéder aux ressources. Par exemple, vous pouvez limiter l’accès à vos applications cloud aux appareils approuvés.

Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise. Les stratégies de protection des applications Intune n’exigent pas de solution de gestion des appareils mobiles (MDM), ce qui vous permet de protéger les données de votre entreprise avec ou sans inscription des appareils dans une solution de gestion des appareils.

L’accès conditionnel Azure Active Directory vous permet de limiter l’accès à vos applications cloud à des applications clientes qui prennent en charge les stratégies Intune App Protection. Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook.

Dans la terminologie relative à l’accès conditionnel, ces applications clientes sont appelées **applications clientes approuvées**.  

![Accès conditionnel](./media/app-based-conditional-access/05.png)

Pour obtenir la liste des applications clientes approuvées, consultez [Spécification d’application cliente approuvée](technical-reference.md#approved-client-app-requirement).

Vous pouvez combiner des stratégies d’accès conditionnel basé sur les applications avec d’autres stratégies, telles que les [stratégies d’accès conditionnel au niveau de l’appareil](require-managed-devices.md), afin de bénéficier d’une plus grande flexibilité en matière de protection des données pour les appareils personnels et d’entreprise.

## <a name="before-you-begin"></a>Avant de commencer

Cette rubrique part du principe que vous connaissez :

- Les informations techniques de référence intitulées [Spécification d’application cliente approuvée](technical-reference.md#approved-client-app-requirement).
- Les concepts de base de l’[accès conditionnel dans Azure Active Directory](overview.md).
- Comment [configurer une stratégie d’accès conditionnel](app-based-mfa.md).
- Comment [migrer des stratégies d’accès conditionnel](best-practices.md#policy-migration).

## <a name="prerequisites"></a>Prérequis

Pour créer une stratégie d’accès conditionnel basé sur les applications, vous devez avoir un abonnement à Enterprise Mobility + Security ou à Azure Active Directory Premium, et les utilisateurs doivent disposer d’une licence pour EMS ou Azure AD. 

## <a name="exchange-online-policy"></a>Stratégie Exchange Online 

Ce scénario consiste en une stratégie d’accès conditionnel basé sur les applications pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
- Reçoit un e-mail qui indique que l’accès est disponible uniquement à l’aide de l’application Outlook
- Télécharge l’application avec le lien
- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD
- Est invité à installer Authenticator (iOS) ou le Portail d’entreprise (Android) pour continuer
- Installe l’application et peut revenir à l’application Outlook pour continuer
- Est invité à inscrire un appareil
- Est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune sont activées au moment de l’accès aux données d’entreprise, et peuvent inviter l’utilisateur à redémarrer l’application, à utiliser un code PIN supplémentaire, etc. (si cette fonction est configurée pour l’application et la plateforme).

### <a name="configuration"></a>Configuration 

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.
1. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :
   1. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et applications de bureau** et **Clients de l’authentification moderne**.
1. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

   ![Accès conditionnel](./media/app-based-conditional-access/05.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.
1. **Conditions :** Comme **Conditions**, configurez **Applications clientes (préversion)** . 
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.
   1. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

      ![Accès conditionnel](./media/app-based-conditional-access/05.png)

**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="exchange-online-and-sharepoint-online-policy"></a>Stratégie Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’accès conditionnel avec la gestion des applications mobiles pour l’accès à Exchange Online et SharePoint Online, avec les applications approuvées.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Tente d’utiliser l’application SharePoint pour se connecter à ses sites d’entreprise et les afficher
- Tente de se connecter avec les mêmes informations d’identification que celles de l’application Outlook
- N’est pas obligé de se réinscrire et peut accéder aux ressources

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 
1. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :
   1. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.
1. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

   ![Accès conditionnel](./media/app-based-conditional-access/05.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. En ligne 
1. **Conditions :** Comme **Conditions**, configurez **Applications clientes** :
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.
   1. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

      ![Accès conditionnel](./media/app-based-conditional-access/05.png)

**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur les applications ou stratégie d’appareil conforme pour Exchange Online et SharePoint Online

Ce scénario consiste en une stratégie d’accès conditionnel basé sur les applications ou la conformité des appareils pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe que :
 
- Certaines utilisateurs ont déjà été inscrits (avec ou sans appareils d’entreprise)
- Les utilisateurs qui ne sont pas enregistrés et inscrits auprès d’Azure AD à l’aide d’une application protégée approuvée doivent inscrire un appareil pour accéder aux ressources
- Les utilisateurs inscrits à l’aide de l’application protégée approuvée ne sont pas obligés de réinscrire l’appareil

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 
1. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
   1. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.
1. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Demander une application cliente approuvée (préversion)**
   - **Demander un des contrôles sélectionnés**   
 
      ![Accès conditionnel](./media/app-based-conditional-access/11.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 
1. **Conditions :** Comme **Conditions**, configurez **Applications clientes**. 
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.
1. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.
 
   ![Accès conditionnel](./media/app-based-conditional-access/11.png)

**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur les applications et stratégie d’appareil conforme pour Exchange Online et SharePoint Online

Ce scénario consiste en une stratégie d’accès conditionnel basé sur les applications et la conformité des appareils pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :
 
- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
- Reçoit un e-mail qui indique que l’accès exige l’inscription de l’appareil
- Télécharge le portail d’entreprise et s’y connecte
- Vérifie ses e-mails et est invité à utiliser l’application Outlook
- Télécharge l’application Outlook
- Ouvre l’application Outlook et entre les informations d’identification utilisées lors de l’inscription
- L’utilisateur est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune sont activées au moment de l’accès aux données d’entreprise, et peuvent inviter l’utilisateur à redémarrer l’application, à utiliser un code PIN supplémentaire, etc. (si ceci est configuré pour l’application et la plateforme)

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 
1. **Conditions :** Comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
   1. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et applications de bureau** et **Clients de l’authentification moderne**.
1. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Demander une application cliente approuvée (préversion)**
   - **Demander tous les contrôles sélectionnés**   
 
      ![Accès conditionnel](./media/app-based-conditional-access/13.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

1. Le **nom** de votre stratégie d’accès conditionnel.
1. **Utilisateurs et groupes** : Au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.
1. **Applications cloud :** Comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 
1. **Conditions :** Comme **Conditions**, configurez **Applications clientes (préversion)** . 
   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

   ![Accès conditionnel](./media/app-based-conditional-access/92.png)

1. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Demander une application cliente approuvée (préversion)**
   - **Demander tous les contrôles sélectionnés**   

**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, voir [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).

Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md). 
