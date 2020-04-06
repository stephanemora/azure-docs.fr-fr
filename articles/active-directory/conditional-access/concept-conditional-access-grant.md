---
title: Contrôles d’octroi dans une stratégie d’accès conditionnel - Azure Active Directory
description: Que sont les contrôles d’octroi dans une stratégie d’accès conditionnel Azure AD ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331838"
---
# <a name="conditional-access-grant"></a>Accès conditionnel : Accorder

Dans une stratégie d’accès conditionnel, un administrateur peut utiliser des contrôles d’accès pour accorder ou bloquer l’accès aux ressources.

![Stratégie d’accès conditionnel avec un contrôle d’octroi nécessitant l’authentification multifacteur](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Bloquer l’accès

Le contrôle de blocage prend en compte les affectations existantes et empêche l’accès en fonction de la configuration de la stratégie d’accès conditionnel.

Le blocage est un puissant contrôle qui doit être utilisé moyennant les connaissances appropriées. Les administrateurs doivent le tester en [mode Rapport uniquement](concept-conditional-access-report-only.md) avant de l’implémenter.

## <a name="grant-access"></a>Accorder l'accès

Les administrateurs peuvent choisir d’appliquer un ou plusieurs contrôles lors de l’octroi de l’accès. Ces contrôles incluent les options suivantes : 

- [Exiger une authentification multifacteur (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Exiger que l’appareil soit marqué comme conforme (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Exiger un appareil joint à une version hybride d’Azure AD](../devices/concept-azure-ad-join-hybrid.md)
- [Demander une application cliente approuvée](app-based-conditional-access.md)
- [Exiger une stratégie de protection des applications](app-protection-based-conditional-access.md)

Si les administrateurs souhaitent combiner ces options, ils peuvent choisir les méthodes suivantes :

- Demander tous les contrôles sélectionnés (contrôle **ET** contrôle)
- Demander un des contrôles sélectionnés (contrôle **OU** contrôle)

Par défaut, l’accès conditionnel exige tous les contrôles sélectionnés.

### <a name="require-multi-factor-authentication"></a>Exiger une authentification multifacteur

Quand cette case est cochée, les utilisateurs doivent effectuer une authentification multifacteur Azure MFA. Pour plus d’informations sur le déploiement d’Azure Multi-Factor Authentication (MFA), consultez l’article [Planification d’un déploiement Azure Multi-Factor Authentication basé sur le cloud](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Exiger que l’appareil soit marqué comme conforme

Les organisations qui ont déployé Microsoft Intune peuvent utiliser les informations retournées par leurs appareils pour identifier les appareils qui remplissent les conditions de conformité spécifiques. Ces informations de conformité de la stratégie sont transmises d’Intune à Azure AD, où l’accès conditionnel peut prendre des décisions pour accorder ou bloquer l’accès aux ressources. Pour plus d’informations sur les stratégies de conformité, consultez l’article [Définir des règles sur les appareils pour autoriser l’accès aux ressources de votre organisation à l’aide d’Intune](/intune/protect/device-compliance-get-started).

Un appareil peut être marqué comme conforme par Intune (pour n’importe quel système d’exploitation d’appareil), ou par un système GPM tiers pour les appareils Windows 10. Jamf Pro est le seul système MDM tiers pris en charge. Pour plus d’informations sur l’intégration, consultez l’article [Intégrer JAMF Pro à Intune pour assurer la conformité](/intune/protect/conditional-access-integrate-jamf).

Les appareils doivent être inscrits dans Azure AD pour pouvoir être marqués comme conformes. Pour plus d’informations sur l’inscription des appareils, consultez l’article [Qu’est-ce qu’une identité d’appareil ?](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Exiger un appareil joint à Azure AD hybride

Les organisations peuvent choisir d’utiliser l’identité de l’appareil dans le cadre de leur stratégie d’accès conditionnel. Elles peuvent utiliser cette case à cocher pour exiger que les appareils soient joints à Azure AD hybride. Pour plus d’informations sur les identités d’appareils, consultez l’article [Qu’est-ce qu’une identité d’appareil ?](../devices/overview.md)

### <a name="require-approved-client-app"></a>Demander une application cliente approuvée

Les organisations peuvent exiger que toute tentative d’accès aux applications cloud sélectionnées provienne d’une application cliente approuvée. Ces applications clientes approuvées prennent en charge les [stratégies de protection des applications Intune](/intune/app-protection-policy), quelle que soit votre solution de gestion des périphériques mobiles (GPM).

Pour tirer parti de ce contrôle d’octroi, l’accès conditionnel exige que l’appareil soit inscrit dans Azure Active Directory, qui lui-même nécessite l’utilisation d’une application de répartiteur. L’application de répartiteur peut être Microsoft Authenticator pour iOS ou le portail d’entreprise Microsoft pour les appareils Android. Si aucune application de répartiteur n’est installée sur l’appareil lorsque l’utilisateur tente de s’authentifier, l’utilisateur est redirigé vers l’App Store pour installer cette application.

Ce paramètre s’applique aux applications iOS et Android suivantes :

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft Hub Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Planificateur Microsoft
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Entreprise
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Tableau blanc collaboratif Microsoft

**Remarques**

- Les applications clientes approuvées prennent en charge la fonctionnalité de gestion des applications mobiles Intune.
- Exigence **Nécessite une application cliente approuvée** :
   - elle prend uniquement en charge iOS et Android pour la condition de plateforme d’appareil.
   - Une application de répartiteur est nécessaire pour inscrire l’appareil. Sur iOS, l’application de répartiteur est Microsoft Authenticator, sur Android, il s’agit de l’application Portail d’entreprise Intune.
- L’accès conditionnel ne peut considérer Microsoft Edge en mode InPrivate en tant qu'application cliente approuvée.

Consultez l’article [Guide pratique pour exiger des applications clientes approuvées pour l’accès aux applications cloud avec l’accès conditionnel](app-based-conditional-access.md) donnant des exemples de configuration.

### <a name="require-app-protection-policy"></a>Exiger une stratégie de protection des applications

Dans votre stratégie d’accès conditionnel, vous pouvez exiger qu’une [stratégie de protection des applications Intune](/intune/app-protection-policy) soit présente sur l’application cliente pour qu’il soit possible d’accéder aux applications cloud sélectionnées. 

Pour tirer parti de ce contrôle d’octroi, l’accès conditionnel exige que l’appareil soit inscrit dans Azure Active Directory, qui lui-même nécessite l’utilisation d’une application de répartiteur. L’application de répartiteur peut être Microsoft Authenticator pour iOS ou le portail d’entreprise Microsoft pour les appareils Android. Si aucune application de répartiteur n’est installée sur l’appareil lorsque l’utilisateur tente de s’authentifier, l’utilisateur est redirigé vers l’App Store pour installer cette application.

Ce paramètre s’applique aux applications clientes suivantes :

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Planificateur Microsoft

**Remarques**

- Les applications associées à la stratégie de protection des applications prennent en charge la fonctionnalité de gestion d’applications mobiles Intune avec la protection des stratégies.
- Exigences relatives à la stratégie **Exiger une stratégie de protection des applications** :
    - elle prend uniquement en charge iOS et Android pour la condition de plateforme d’appareil.
    - Une application de répartiteur est nécessaire pour inscrire l’appareil. Sur iOS, l’application de répartiteur est Microsoft Authenticator, sur Android, il s’agit de l’application Portail d’entreprise Intune.

Consultez l’article [Guide pratique pour exiger une stratégie de protection d’application et une application cliente approuvée pour l’accès aux applications cloud avec l’accès conditionnel](app-protection-based-conditional-access.md) donnant des exemples de configuration.

### <a name="terms-of-use"></a>Conditions d’utilisation

Si votre organisation a créé des conditions d’utilisation, des options supplémentaires peuvent être visibles sous les contrôles d’octroi. Ces options permettent aux administrateurs de demander l’acceptation des conditions d’utilisation comme condition d’accès aux ressources protégées par la stratégie. Pour plus d’informations sur les conditions d’utilisation, consultez l’article [Conditions d’utilisation d’Azure Active Directory](terms-of-use.md).

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : Contrôles de session](concept-conditional-access-session.md)

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

- [Mode Rapport uniquement](concept-conditional-access-report-only.md)
