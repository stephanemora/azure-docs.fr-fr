---
title: Actions ou applications cloud dans une stratégie d’accès conditionnel - Azure Active Directory
description: Que sont les actions ou les applications cloud dans une stratégie d’accès conditionnel Azure AD ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9d2780e09c099d76aa2ef4ec2638a410793481d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186383"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Accès conditionnel : Applications ou actions cloud

Les applications cloud ou les actions représentent un signal clé dans une stratégie d’accès conditionnel. Les stratégies d’accès conditionnel permettent aux administrateurs d’affecter des contrôles à des applications ou des actions spécifiques.

- Les administrateurs peuvent choisir à partir de la liste des applications qui comprend des applications Microsoft intégrées et des [application Azure AD intégrée](../manage-apps/what-is-application-management.md), entre autres des applications de la galerie et hors galerie ainsi que des applications publiées via le [proxy d’application](../manage-apps/what-is-application-proxy.md).
- Les administrateurs peuvent choisir de définir une stratégie qui n’est pas basée sur une application cloud, mais sur une action de l’utilisateur. La seule action prise en charge est Enregistrer les informations de sécurité (préversion), qui autorise l’accès conditionnel à appliquer des contrôles dans le cadre de [l’expérience d’inscription d’informations de sécurité combinée](../authentication/howto-registration-mfa-sspr-combined.md).

![Définir une stratégie d’accès conditionnel et spécifier des applications cloud](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Applications cloud Microsoft

La plupart des applications cloud Microsoft existantes sont incluses dans la liste des applications à partir de laquelle vous pouvez effectuer votre sélection. 

Les administrateurs peuvent affecter une stratégie d’accès conditionnel aux applications cloud suivantes à partir de Microsoft. Certaines applications comme Office 365 (préversion) et Gestion Microsoft Azure comprennent plusieurs applications ou services enfants associés. La liste suivante n’est pas exhaustive et peut faire l’objet de modifications.

- [Office 365 (préversion)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database et Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM en ligne
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestion Microsoft Azure](#microsoft-azure-management)
- Gestion des abonnements Microsoft Azure
- Microsoft Cloud App Security
- Portail de contrôle des accès aux outils de Microsoft Commerce
- Service d’authentification des outils Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inscription à Microsoft Intune](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
- Planificateur Microsoft
- Microsoft PowerApps
- Recherche Microsoft Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Service Power BI
- Project Online
- Skype Entreprise Online
- Réseau privé virtuel (VPN)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365 (préversion)

Office 365 fournit des services de collaboration et de productivité informatiques comme Exchange, SharePoint et Microsoft Teams. Les services cloud Office 365 sont profondément intégrés pour garantir des expériences fluides et collaboratives. Cette intégration peut entraîner une confusion lors de la création de stratégies, car certaines applications, telles que Microsoft Teams, ont des dépendances par rapport à d’autres, comme SharePoint ou Exchange.

L’application Office 365 (préversion) permet de cibler ces services en même temps. Nous conseillons d’utiliser la nouvelle application Office 365 (préversion) plutôt que de cibler des applications cloud individuelles. Le ciblage de ce groupe d’applications permet d’éviter les problèmes pouvant survenir en raison de stratégies et de dépendances incohérentes.

S’ils le souhaitent, les administrateurs peuvent choisir d’exclure des applications spécifiques de la stratégie en incluant l’application Office 365 (préversion) et en excluant ces applications particulières de leur choix dans la stratégie.

Applications clés incluses dans l’application cliente Office 365 (préversion) :

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Service de recherche Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype Entreprise Online
   - Sway

### <a name="microsoft-azure-management"></a>Gestion Microsoft Azure

L’application Gestion Microsoft Azure comprend plusieurs services sous-jacents. 

   - Portail Azure
   - Fournisseur Azure Resource Manager
   - API du modèle de déploiement Classic
   - Azure PowerShell
   - Portail de l’administrateur d’abonnements Visual Studio
   - Azure DevOps
   - Portail Azure Data Factory

> [!NOTE]
> L’application Gestion Microsoft Azure s’applique à Azure PowerShell, qui appelle l’API Azure Resource Manager. Elle ne s’applique pas à Azure AD PowerShell, qui appelle Microsoft Graph.

## <a name="other-applications"></a>Autres applications

Outre les applications Microsoft, les administrateurs peuvent ajouter aux stratégies d’accès conditionnel n’importe quelle application inscrite auprès d’Azure AD. Parmi celles-ci : 

- Des applications publiées via le [proxy d’application Azure AD](../manage-apps/what-is-application-proxy.md)
- [Des applications ajoutées à partir de la galerie](../manage-apps/add-application-portal.md)
- [Des applications personnalisées qui ne se trouvent pas dans la galerie](../manage-apps/add-non-gallery-app.md)
- [Des applications héritées qui sont publiées par le biais de réseaux et de contrôleurs de livraison d’applications](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Actions utilisateur

Les actions utilisateur sont des tâches qui peuvent être effectuées par un utilisateur. La seule action actuellement prise en charge est **Enregistrer les informations de sécurité (préversion)** , qui permet d’appliquer la stratégie d’accès conditionnel lorsque les utilisateurs pour lesquels l’inscription combinée est activée tentent d’enregistrer leurs informations de sécurité. Vous trouverez plus d’informations dans l’article [Enregistrement des informations de sécurité combinées (préversion)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : Conditions](concept-conditional-access-conditions.md)

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)
- [Dépendances des applications clientes](service-dependencies.md)
