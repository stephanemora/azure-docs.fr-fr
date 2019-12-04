---
title: Stratégies de protection des applications avec l’accès conditionnel – Azure Active Directory
description: Découvrez comment exiger une stratégie de protection des applications pour l’accès aux applications cloud à l’aide de l’accès conditionnel dans Azure Active Directory.
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
ms.openlocfilehash: b3cbb6afb96ccea32aa78d1f587377e5d67e1a5b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381022"
---
# <a name="require-app-protection-policy-for-cloud-app-access-with-conditional-access-preview"></a>Exiger une stratégie de protection des applications pour l’accès aux applications cloud à l’aide de l’accès conditionnel (préversion)

Vos employés utilisent des appareils mobiles pour des tâches à la fois personnelles et professionnelles. Tout en veillant à ce que vos employés restent productifs, vous voulez aussi éviter toute perte de données. Avec l’accès conditionnel Azure Active Directory (Azure AD), vous pouvez protéger vos données d’entreprise en restreignant l’accès à vos applications cloud. Utilisez d’abord les applications clientes pour lesquelles est appliquée une stratégie de protection des applications.

Cet article explique comment configurer des stratégies d’accès conditionnel qui peuvent exiger la mise en place d’une stratégie de protection des applications pour l’accès aux données.

## <a name="overview"></a>Vue d'ensemble

Avec l’[accès conditionnel Azure AD](overview.md), vous pouvez préciser la façon dont chaque utilisateur autorisé peut accéder aux ressources. Par exemple, vous pouvez limiter l’accès à vos applications cloud aux appareils approuvés.

Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise. Les stratégies Intune App Protection ne nécessitent pas de solution de gestion des appareils mobiles. Vous pouvez protéger les données de votre entreprise sans inscrire vos appareils dans une solution de gestion des appareils.

L’accès conditionnel Azure Active Directory n’autorise l’accès à vos applications cloud qu’aux applications clientes qu’Intune a signalées à Azure AD comme ayant accepté une stratégie de protection des applications. Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook ayant une stratégie Intune App Protection.

En terminologie d’accès conditionnel, on dit que ces applications clientes sont protégées par une *stratégie de protection des applications*.  

![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)

Pour obtenir la liste des applications clientes protégées par une stratégie, consultez [Exigence relative à la stratégie de protection des applications](technical-reference.md#approved-client-app-requirement).

Vous pouvez combiner des stratégies d’accès conditionnel basées sur la protection des applications avec d’autres stratégies, telles que les [stratégies d’accès conditionnel basées sur les appareils](require-managed-devices.md). De cette façon, vous pouvez adapter la protection des données selon qu’elles se trouvent sur des appareils personnels ou des appareils d’entreprise.

> [!NOTE]
> Les stratégies de protection des applications à accès conditionnel ne peuvent pas être appliquées aux utilisateurs B2B, car l’organisation qui lance l’invitation n’a aucune visibilité sur l’organisation de l’utilisateur B2B.

## <a name="benefits-of-app-protection-based-conditional-access-requirement"></a>Avantages de l’accès conditionnel basé sur la protection des applications

De la même façon qu’Intune pour iOS et Android signalent qu’un appareil géré est conforme, Intune avertit désormais Azure AD lorsqu’une stratégie de protection des applications est appliquée. L’accès conditionnel peut utiliser cette stratégie pour vérifier l’accès. La nouvelle stratégie d’accès conditionnel que constitue la stratégie de protection des applications accroît le niveau de sécurité. Elle permet d’éviter les erreurs d’administration, telles que les suivantes :

- Les utilisateurs qui n’ont pas de licence Intune
- Les utilisateurs qui ne peuvent pas recevoir une stratégie Intune App Protection
- Les applications concernées par la stratégie Intune App Protection qui ne sont pas configurées pour recevoir une stratégie

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose de connaître :

- Les informations techniques de référence sur les [exigences des stratégies de protection des applications](technical-reference.md#app-protection-policy-requirement).
- Les informations techniques de référence intitulées [Spécification d’application cliente approuvée](technical-reference.md#approved-client-app-requirement).
- Les concepts de base de l’[accès conditionnel dans Azure Active Directory](overview.md).
- Comment [configurer une stratégie d’accès conditionnel](app-based-mfa.md).

## <a name="prerequisites"></a>Prérequis

Pour créer une stratégie d’accès conditionnel basée sur la protection des applications, vous devez :

- Disposer d’un abonnement Premium Enterprise Mobility + Security ou Azure Active Directory + Intune
- Vérifier que les utilisateurs ont une licence pour Enterprise Mobility + Security ou Azure AD + Intune
- Vérifier que l’application cliente est configurée dans Intune pour recevoir une stratégie de protection des applications
- Vérifier que les utilisateurs sont configurés dans Intune pour recevoir une stratégie Intune App Protection

## <a name="app-protection-based-policy-for-exchange-online"></a>Stratégie basée sur la protection des applications pour Exchange Online

Ce scénario implique une stratégie d’accès conditionnel basée sur la protection des applications qui permet d’accéder à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
- Reçoit un e-mail qui indique que l’accès est disponible uniquement à l’aide de l’application Outlook
- Télécharge l’application avec le lien
- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD
- Est invité à installer soit l’**application Microsoft Authenticator** soit le **portail d’entreprise Intune** pour continuer.
- Installe l’application et revient à l’application Outlook pour continuer
- Est invité à inscrire un appareil
- Peut recevoir une stratégie Intune App Protection
- Peut accéder aux e-mails

Les stratégies Intune App Protection doivent être appliquées à l’application pour permettre l’accès aux données d’entreprise. Les stratégies peuvent inviter l’utilisateur à redémarrer l’application ou à utiliser un code PIN supplémentaire. Ce sera le cas si les stratégies sont configurées pour l’application et la plateforme.

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/01.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**.

   ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

1. Dans **Conditions**, configurez **Plateformes d’appareils** et **Applications clientes (préversion)**  :
   1. Dans **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

1. Sous **Contrôles d’accès**, sélectionnez **Exiger une stratégie de protection des applications (préversion)** .

   ![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync (EAS)**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**.

   ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

1. Dans **Conditions**, configurez **Applications clientes (préversion)** . 

   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

   1. Sous **Contrôles d’accès**, sélectionnez **Exiger une stratégie de protection des applications (préversion)** .

      ![Accès conditionnel](./media/app-protection-based-conditional-access/05.png)

**Étape 3 : Configurer la stratégie Intune App Protection pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-compliant-device-policy-for-exchange-online"></a>Stratégie basée sur la protection des applications ou sur la conformité des appareils pour Exchange Online

Ce scénario implique une stratégie d’accès conditionnel basée sur la protection des applications ou la conformité des appareils pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe que :
 
- Un utilisateur a déjà été inscrit (avec ou sans appareils d’entreprise)
- Les utilisateurs qui ne sont pas inscrits auprès d’Azure AD à l’aide d’une application protégée doivent inscrire un appareil pour accéder aux ressources.
- Les utilisateurs inscrits qui utilisent l’application protégée ne sont pas obligés de réinscrire l’appareil.
- L’utilisateur peut recevoir une stratégie Intune App Protection s’il n’est pas inscrit.
- L’utilisateur peut accéder aux e-mails avec Outlook et une stratégie Intune App Protection s’il n’est pas inscrit.
- L’utilisateur peut accéder aux e-mails avec Outlook si l’appareil est inscrit.

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/62.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**. 

   ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

1. Dans **Conditions**, configurez **Plateformes d’appareils** et **Applications clientes (préversion)** . 
 
   1. Dans **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

5. Sous **Contrôles d’accès**, sélectionnez les options suivantes :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander un des contrôles sélectionnés**   
 
      ![Accès conditionnel](./media/app-protection-based-conditional-access/11.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**. 

   ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

1. Dans **Conditions**, configurez **Applications clientes (préversion)** . 

   Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

   ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

1. Sous **Contrôles d’accès**, sélectionnez les options suivantes :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander un des contrôles sélectionnés**

      ![Accès conditionnel](./media/app-protection-based-conditional-access/11.png)

**Étape 3 : Configurer la stratégie Intune App Protection pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-and-compliant-device-policy-for-exchange-online"></a>Stratégie basée sur la protection des applications et sur la conformité des appareils pour Exchange Online

Ce scénario implique une stratégie d’accès conditionnel basée sur la protection des applications et sur la conformité des appareils pour l’accès à Exchange Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :
 
- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
- Reçoit un e-mail qui indique que l’accès exige l’inscription de l’appareil
- Télécharge le portail d’entreprise Intune et s’y connecte
- Vérifie ses e-mails et est invité à utiliser l’application Outlook
- Télécharge l’application Outlook
- Ouvre l’application Outlook et entre les informations d’identification utilisées lors de l’inscription
- Peut recevoir une stratégie Intune App Protection
- Peut accéder aux e-mails avec Outlook et une stratégie Intune App Protection

Les stratégies Intune App Protection sont activées avant que l’accès aux données d’entreprise ne soit accordé. Les stratégies peuvent inviter l’utilisateur à redémarrer l’application ou à utiliser un code PIN supplémentaire. Ce sera le cas si les stratégies sont configurées pour l’application et la plateforme.

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/01.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**. 

   ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

1. Dans **Conditions**, configurez **Plateformes d’appareils** et **Applications clientes (préversion)** . 
   1. Dans **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

1. Sous **Contrôles d’accès**, sélectionnez les options suivantes :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander tous les contrôles sélectionnés**   
 
      ![Accès conditionnel](./media/app-protection-based-conditional-access/13.png)

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/06.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**. 

   ![Accès conditionnel](./media/app-protection-based-conditional-access/07.png)

1. Dans **Conditions**, configurez **Applications clientes (préversion)** . 

   Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.

   ![Accès conditionnel](./media/app-protection-based-conditional-access/92.png)

1. Sous **Contrôles d’accès**, sélectionnez les options suivantes :
   - **Exiger que l’appareil soit marqué comme conforme**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander tous les contrôles sélectionnés**   
 
      ![Accès conditionnel](./media/app-protection-based-conditional-access/13.png)

**Étape 3 : Configurer la stratégie Intune App Protection pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="app-protection-based-or-app-based-policy-for-exchange-online-and-sharepoint-online"></a>Stratégie basée sur la protection des applications ou sur l’approbation des applications pour Exchange Online et SharePoint Online

Ce scénario implique une stratégie basée sur la protection des applications ou sur l’approbation des applications pour l’accès à Exchange Online et à SharePoint Online.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure les applications clientes qui sont sur la liste des applications prenant en charge la stratégie de protection des applications ou la stratégie d’approbation des applications  
- Utilise des applications clientes qui répondent aux exigences de la stratégie de protection des applications et peuvent recevoir une stratégie Intune App Protection
- Utilise des applications clientes qui répondent aux exigences de la stratégie d’approbation des applications qui prend en charge la stratégie Intune App Protection
- Ouvre l’application à accéder aux e-mails ou aux documents
- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD
- Est invité à installer Microsoft Authenticator pour iOS ou le portail d’entreprise Intune pour Android, s’ils ne sont pas déjà installés
- Installe l’application et peut revenir à l’application Outlook pour continuer
- Est invité à inscrire un appareil
- Peut recevoir une stratégie Intune App Protection
- Peut accéder aux e-mails avec Outlook et une stratégie Intune App Protection
- Peut accéder aux sites et aux documents avec une application n’appliquant pas la stratégie de protection des applications mais appliquant la stratégie d’approbation des applications.

Les stratégies Intune App Protection doivent être appliquées avant que l’accès aux données d’entreprise ne soit accordé. Les stratégies peuvent inviter l’utilisateur à redémarrer l’application ou à utiliser un code PIN supplémentaire. Ce sera le cas si les stratégies sont configurées pour l’application et la plateforme.

**Remarques**

- Vous pouvez utiliser ce scénario si vous souhaitez prendre en charge à la fois la stratégie d’accès conditionnel basée sur la protection et celle basée sur l’approbation des applications.
- Dans cette stratégie *conditionnelle*, les applications qui appliquent une stratégie de protection des applications sont évaluées avant celles qui appliquent la stratégie d’approbation des applications.

### <a name="configuration"></a>Configuration

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

![Accès conditionnel](./media/app-protection-based-conditional-access/62.png)

1. Entrez le nom de votre stratégie d’accès conditionnel.
1. Sous **Affectations**, dans **Utilisateurs et groupes**, sélectionnez au moins un utilisateur ou groupe pour chaque stratégie d’accès conditionnel.
1. Dans **Applications cloud**, sélectionnez **Office 365 Exchange Online**. 

   ![Accès conditionnel](./media/app-protection-based-conditional-access/02.png)

1. Dans **Conditions**, configurez **Plateformes d’appareils** et **Applications clientes (préversion)** . 
   1. Dans **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/03.png)

   1. Dans **Applications clientes (préversion)** , sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.

      ![Accès conditionnel](./media/app-protection-based-conditional-access/91.png)

1. Sous **Contrôles d’accès**, sélectionnez les options suivantes :
   - **Demander une application cliente approuvée**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander un des contrôles sélectionnés**
 
      ![Accès conditionnel](./media/app-protection-based-conditional-access/12.png)

**Étape 2 : Configurer la stratégie Intune App Protection pour les applications clientes iOS et Android**

![Accès conditionnel](./media/app-protection-based-conditional-access/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel Azure Active Directory](app-based-mfa.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [bonnes pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).
