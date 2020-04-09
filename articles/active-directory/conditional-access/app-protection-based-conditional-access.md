---
title: Stratégies de protection des applications avec l’accès conditionnel – Azure Active Directory
description: Découvrez comment exiger une stratégie de protection des applications pour l’accès aux applications cloud à l’aide de l’accès conditionnel dans Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631892"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Procédure : Exiger une stratégie de protection d’application pour l’accès aux applications cloud avec l’accès conditionnel

Les appareils mobiles sont régulièrement utilisés pour effectuer des tâches aussi bien personnelles que professionnelles. Tout en veillant à ce que le personnel puisse être productif, les organisations veulent également empêcher la perte de données depuis des applications potentiellement non sécurisées. Avec l’accès conditionnel, les organisations peuvent limiter l’accès aux seules applications clientes approuvées (avec une authentification moderne) auxquelles des stratégies Intune App Protection sont appliquées.

Cet article présente deux scénarios permettant de configurer des stratégies d’accès conditionnel pour des ressources comme Office 365, Exchange Online et SharePoint Online.

- [Scénario 1 : Les applications Office 365 demandent des applications approuvées avec des stratégies de protection d’application](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scénario 2 : Exchange Online et SharePoint Online demandent une application cliente approuvée et une stratégie de protection d’application](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

Dans l’accès conditionnel, on dit que ces applications clientes sont protégées par une stratégie de protection d’application. Pour obtenir plus d’informations sur les stratégies de protection d’application, consultez l’article [Présentation des stratégies de protection d’application](/intune/apps/app-protection-policy)

Pour obtenir la liste des applications clientes éligibles, consultez [Présence obligatoire d’une stratégie de protection d’application](concept-conditional-access-grant.md).

> [!NOTE]
>    La clause OR est utilisée dans la stratégie pour permettre aux utilisateurs d’utiliser les applications prenant en charge les contrôles d'octroi **Exiger une stratégie de protection des applications** ou **Demander une application cliente approuvée**. Pour plus d’informations sur les applications prenant en charge le contrôle d'octroi **Exiger une stratégie de protection des applications**, consultez [Présence obligatoire d’une stratégie de protection des applications](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scénario 1 : Les applications Office 365 demandent des applications approuvées avec des stratégies de protection d’application

Dans ce scénario, Contoso a décidé que tout accès mobile aux ressources Office 365 doit utiliser des applications clientes approuvées, telles que Outlook Mobile, OneDrive et Microsoft Teams, protégées par une stratégie de protection d’application avant de recevoir l’accès. Tous les utilisateurs de Contoso se connectent déjà à l’aide d’informations d’identification Azure AD et disposent des licences qui leur sont attribuées, notamment Azure AD Premium P1 ou P2 et Microsoft Intune.

Les organisations doivent effectuer les étapes suivantes pour exiger l’utilisation d’une application cliente approuvée sur des appareils mobiles.

**Étape 1 : Configurer une stratégie d’accès conditionnel Azure AD pour Office 365**

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs** ou les **Utilisateurs et groupes** particuliers auxquels vous souhaitez appliquer cette stratégie. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications cloud ou actions** > **Inclure**, sélectionnez **Office 365 (préversion)** .
1. Sous **Conditions**, sélectionnez **Plateformes d’appareils**.
   1. Définissez **Configurer** sur **Oui**.
   1. Incluez **Android** et **iOS**.
1. Sous **Conditions**, sélectionnez **Applications clientes (préversion)** .
   1. Définissez **Configurer** sur **Oui**.
   1. Sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.
1. Sous **Contrôles d’accès** > **Octroyer**, sélectionnez les options suivantes :
   - **Demander une application cliente approuvée**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander tous les contrôles sélectionnés**
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer et activer votre stratégie.

**Étape 2 : Configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec ActiveSync (EAS)**

Pour les besoins de la stratégie d’accès conditionnel de cette étape, configurez les composants suivants :

1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs** ou les **Utilisateurs et groupes** particuliers auxquels vous souhaitez appliquer cette stratégie. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications cloud ou actions** > **Inclure**, sélectionnez **Office 365 Exchange Online**.
1. Sous **Conditions** :
   1. **Applications clientes (préversion)**  :
      1. Définissez **Configurer** sur **Oui**.
      1. Sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.
1. Sous **Contrôles d’accès** > **Octroyer**, sélectionnez **Accorder l’accès**, **Exiger une stratégie de protection des applications** et sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer et activer votre stratégie.

**Étape 3 : Configurer la stratégie Intune App Protection pour les applications clientes iOS et Android**

Consultez l’article [Guide pratique pour créer et assigner des stratégies de protection d’application](/intune/apps/app-protection-policies) afin de connaître les étapes de création des stratégies de protection d’application pour Android et iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scénario 2 : Exchange Online et SharePoint Online demandent une application cliente approuvée et une stratégie de protection d’application

Dans ce scénario, Contoso a décidé que les utilisateurs peuvent uniquement accéder aux e-mails et aux données SharePoint sur les appareils mobiles, à condition qu’ils utilisent une application cliente approuvée, comme Outlook Mobile, protégée par une stratégie de protection d’application avant de recevoir l’accès. Tous les utilisateurs de Contoso se connectent déjà à l’aide d’informations d’identification Azure AD et disposent des licences qui leur sont attribuées, notamment Azure AD Premium P1 ou P2 et Microsoft Intune.

Les organisations doivent effectuer les trois étapes suivantes pour exiger l’utilisation d’une application cliente approuvée sur des appareils mobiles et des clients ActiveSync.

**Étape 1 : Stratégie pour les clients à authentification moderne basés sur Android et iOS, nécessitant l’utilisation d’une application cliente approuvée et une stratégie de protection d’application lors de l’accès à Exchange Online et SharePoint Online**

1. Connectez-vous au **portail Microsoft Azure** en tant qu’administrateur général, administrateur de sécurité ou administrateur de l’accès conditionnel.
1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs** ou les **Utilisateurs et groupes** particuliers auxquels vous souhaitez appliquer cette stratégie. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications cloud ou actions** > **Inclure**, sélectionnez **Office 365 Exchange Online** et **Office 365 SharePoint Online**.
1. Sous **Conditions**, sélectionnez **Plateformes d’appareils**.
   1. Définissez **Configurer** sur **Oui**.
   1. Incluez **Android** et **iOS**.
1. Sous **Conditions**, sélectionnez **Applications clientes (préversion)** .
   1. Définissez **Configurer** sur **Oui**.
   1. Sélectionnez **Applications mobiles et clients de bureau** et **Clients de l’authentification moderne**.
1. Sous **Contrôles d’accès** > **Octroyer**, sélectionnez les options suivantes :
   - **Demander une application cliente approuvée**
   - **Exiger une stratégie de protection des applications (préversion)**
   - **Demander un des contrôles sélectionnés**
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer et activer votre stratégie.

**Étape 2 : Stratégie pour les clients Exchange ActiveSync nécessitant l’utilisation d’une application cliente approuvée**

1. Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel.**
1. Sélectionnez **Nouvelle stratégie**.
1. Donnez un nom à votre stratégie. Nous recommandons aux organisations de créer une norme explicite pour les noms de leurs stratégies.
1. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**
   1. Sous **Inclure**, sélectionnez **Tous les utilisateurs** ou les **Utilisateurs et groupes** particuliers auxquels vous souhaitez appliquer cette stratégie. 
   1. Sélectionnez **Terminé**.
1. Sous **Applications cloud ou actions** > **Inclure**, sélectionnez **Office 365 Exchange Online**.
1. Sous **Conditions** :
   1. **Applications clientes (préversion)**  :
      1. Définissez **Configurer** sur **Oui**.
      1. Sélectionnez **Applications mobiles et clients de bureau** et **Clients Exchange ActiveSync**.
1. Sous **Contrôles d’accès** > **Octroyer**, sélectionnez **Accorder l’accès**, **Exiger une stratégie de protection des applications** et sélectionnez **Sélectionner**.
1. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.
1. Sélectionnez **Créer** pour créer et activer votre stratégie.

**Étape 3 : Configurer la stratégie Intune App Protection pour les applications clientes iOS et Android**

Consultez l’article [Guide pratique pour créer et assigner des stratégies de protection d’application](/intune/apps/app-protection-policies) afin de connaître les étapes de création des stratégies de protection d’application pour Android et iOS. 

## <a name="next-steps"></a>Étapes suivantes

[Qu’est-ce que l’accès conditionnel ?](overview.md)

[Composants de l’accès conditionnel](concept-conditional-access-policies.md)

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

