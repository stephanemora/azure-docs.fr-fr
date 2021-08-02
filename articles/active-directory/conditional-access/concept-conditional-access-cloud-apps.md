---
title: Applications, actions et contexte d’authentification cloud dans une stratégie d’accès conditionnel – Azure Active Directory
description: Que sont les applications, les actions et le contexte d’authentification cloud dans une stratégie d’accès conditionnel Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99da9afc9afb3c6eb19caf696c6b9802aed6a2dd
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953669"
---
# <a name="conditional-access-cloud-apps-actions-and-authentication--context"></a>Accès conditionnel : applications, actions et contexte d’authentification cloud

Les applications, les actions et le contexte d’authentification cloud sont des signaux clés dans une stratégie d’accès conditionnel. Les stratégies d’accès conditionnel permettent aux administrateurs d’affecter des contrôles à des applications, à des actions ou à un contexte d’authentification spécifiques.

- Les administrateurs peuvent choisir à partir de la liste des applications qui comprend des applications Microsoft intégrées et des [application Azure AD intégrée](../manage-apps/what-is-application-management.md), entre autres des applications de la galerie et hors galerie ainsi que des applications publiées via le [proxy d’application](../app-proxy/what-is-application-proxy.md).
- Les administrateurs peuvent choisir de définir une stratégie non basée sur une application cloud mais sur une [action utilisateur](#user-actions) telle que **Inscrire des informations de sécurité** ou **Inscrire ou joindre des appareils (préversion)** , autorisant l’accès conditionnel à appliquer des contrôles autour de ces actions.
- Les administrateurs peuvent utiliser un [contexte d’authentification](#authentication-context-preview) pour fournir une couche supplémentaire de sécurité à l’intérieur des applications. 

![Définir une stratégie d’accès conditionnel et spécifier des applications cloud](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Applications cloud Microsoft

La plupart des applications cloud Microsoft existantes sont incluses dans la liste des applications à partir de laquelle vous pouvez effectuer votre sélection. 

Les administrateurs peuvent affecter une stratégie d’accès conditionnel aux applications cloud suivantes à partir de Microsoft. Certaines applications comme Office 365 et Gestion Microsoft Azure comprennent plusieurs applications ou services enfants associés. Comme nous ajoutons continuellement d’autres applications, la liste suivante n’est pas exhaustive et est susceptible de changer.

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- Azure Event Hubs
- Azure Service Bus
- [Azure SQL Database et Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM en ligne
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Gestion Microsoft Azure](#microsoft-azure-management)
- Gestion des abonnements Microsoft Azure
- Microsoft Cloud App Security
- Portail de contrôle des accès aux outils de Microsoft Commerce
- Service d’authentification des outils Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Inscription à Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Planificateur Microsoft
- Microsoft PowerApps
- Recherche Microsoft Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Service Power BI
- Project Online
- Skype Entreprise Online
- Réseau privé virtuel (VPN)
- Windows Defender ATP

Les applications disponibles pour l’accès conditionnel ont suivi un processus d’intégration et de validation. Cette liste n’inclut pas toutes les applications Microsoft, car nombre d’entre elles sont des services principaux non censés être régis par une stratégie qui leur est directement appliquée. Si vous recherchez une application manquante, vous pouvez contacter l’équipe d’application spécifique ou formuler une demande sur [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=167259).

### <a name="office-365"></a>Office 365

Microsoft 365 fournit des services de collaboration et de productivité informatiques comme Exchange, SharePoint et Microsoft Teams. Les services cloud Microsoft 365 sont profondément intégrés pour garantir des expériences fluides et collaboratives. Cette intégration peut entraîner une confusion lors de la création de stratégies, car certaines applications, telles que Microsoft Teams, ont des dépendances par rapport à d’autres, comme SharePoint ou Exchange.

L’application Office 365 permet de cibler tous ces services en même temps. Pour éviter les problèmes de [dépendances de service](service-dependencies.md), nous vous conseillons d’utiliser la nouvelle application Office 365 plutôt que de cibler les applications cloud individuellement. Le ciblage de ce groupe d’applications permet d’éviter les problèmes pouvant survenir en raison de stratégies et de dépendances incohérentes.

S’ils le souhaitent, les administrateurs peuvent choisir d’exclure des applications spécifiques de la stratégie en incluant l’application Office 365 et en excluant ces applications particulières de leur choix dans la stratégie.

Applications clés incluses dans l’application cliente Office 365 :

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Service de recherche Microsoft 365
   - Yammer
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
   - Azure CLI
   - Portail de l’administrateur d’abonnements Visual Studio
   - Azure DevOps
   - Portail Azure Data Factory

> [!NOTE]
> L’application Gestion Microsoft Azure s’applique à Azure PowerShell, qui appelle l’API Azure Resource Manager. Elle ne s’applique pas à Azure AD PowerShell, qui appelle Microsoft Graph.

### <a name="other-applications"></a>Autres applications

Outre les applications Microsoft, les administrateurs peuvent ajouter aux stratégies d’accès conditionnel n’importe quelle application inscrite auprès d’Azure AD. Parmi celles-ci : 

- Des applications publiées via le [proxy d’application Azure AD](../app-proxy/what-is-application-proxy.md)
- [Des applications ajoutées à partir de la galerie](../manage-apps/add-application-portal.md)
- [Des applications personnalisées qui ne se trouvent pas dans la galerie](../manage-apps/view-applications-portal.md)
- [Des applications héritées qui sont publiées par le biais de réseaux et de contrôleurs de livraison d’applications](../manage-apps/secure-hybrid-access.md)
- Des applications qui utilisent [l’authentification unique par mot de passe](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

> [!NOTE]
> Étant donné que la stratégie d’accès conditionnel définit les conditions requises pour accéder à un service, vous n’êtes pas en mesure de l’appliquer à une application cliente (publique/native). En d’autres termes, la stratégie n’est pas définie directement sur une application cliente (publique/native), mais elle est appliquée lorsqu’un client appelle un service. Par exemple, une stratégie définie sur un service SharePoint s’applique aux clients qui appellent SharePoint. Une stratégie définie sur Exchange s’applique à la tentative d’accès à la messagerie à l’aide du client Outlook. C’est pourquoi les applications clientes (publiques/natives) ne sont pas disponibles dans le sélecteur d’applications Cloud et l’option d’accès conditionnel n’est pas disponible dans les paramètres d’application pour l’application cliente (publique/native) inscrite dans votre locataire. 

## <a name="user-actions"></a>Actions utilisateur

Les actions utilisateur sont des tâches qui peuvent être effectuées par un utilisateur. Actuellement, l’accès conditionnel prend en charge deux actions utilisateur : 

- **Enregistrer les informations de sécurité** : cette action utilisateur permet d’appliquer la stratégie d’accès conditionnel lorsque les utilisateurs pour lesquels l’inscription combinée est activée tentent d’enregistrer leurs informations de sécurité. Vous trouverez plus d’informations dans l’article [Enregistrement des informations de sécurité combinées](../authentication/concept-registration-mfa-sspr-combined.md).

- **Inscrire ou joindre des appareils (préversion)** : cette action utilisateur permet aux administrateurs d’appliquer la stratégie d’accès conditionnel lorsque les utilisateurs [inscrivent](../devices/concept-azure-ad-register.md) ou [joignent](../devices/concept-azure-ad-join.md) des appareils à Azure AD. Elle apporte de la granularité à la configuration de l’authentification multifacteur pour l’inscription ou la jonction d’appareils au lieu d’une stratégie à l’échelle du locataire telle qu’elle existe actuellement. Il existe trois points clés à prendre en compte pour cette action de l’utilisateur : 
   - `Require multi-factor authentication` est le seul contrôle d’accès disponible avec cette action utilisateur. Tous les autres sont désactivés. Cette restriction empêche les conflits avec les contrôles d’accès qui dépendent d’Azure AD Device Registration ou ne s’y appliquent pas. 
   - Les conditions `Client apps` et `Device state` ne sont pas disponibles avec cette action de l’utilisateur, car elles dépendent de l’inscription d’appareil Azure AD pour appliquer les stratégies d’accès conditionnel.
   - Quand une stratégie d’accès conditionnel est activée avec cette action utilisateur, vous devez définir **Azure Active Directory** > **Appareils** > **Paramètres d’appareil** - `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` sur **Non**. Dans le cas contraire, la stratégie d’accès conditionnel avec cette action d’utilisateur n’est pas appliquée correctement. Pour plus d’informations sur ce paramètre d’appareil, consultez [Configuration des paramètres d’appareil](../devices/device-management-azure-portal.md#configure-device-settings). 

## <a name="authentication-context-preview"></a>Contexte d’authentification (préversion)

Un contexte d’authentification permet de renforcer la sécurité des données et des actions dans des applications. Il peut s’agir de vos propres applications personnalisées, d’applications métier personnalisées, d’applications telles que SharePoint, ou d’applications protégées par Microsoft Cloud App Security (MCAS). 

Par exemple, une organisation peut conserver dans des sites SharePoint des fichiers tels que le menu du déjeuner ou sa recette secrète de sauce barbecue. Tout le monde peut avoir accès au site du menu du déjeuner, mais les utilisateurs qui ont accès au site de la recette secrète de sauce barbecue peuvent être tenus d’y accéder à partir d’un appareil géré et d’accepter des conditions d’utilisation spécifiques.

### <a name="configure-authentication-contexts"></a>Configurer des contextes d’authentification

Les contextes d’authentification sont gérés dans le portail Azure sous **Azure Active Directory** > **Sécurité** > **Accès conditionnel** > **Contexte d’authentification**.

![Gérer un contexte d’authentification dans le portail Azure](./media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-get-started.png)

> [!WARNING]
> * La suppression de définitions de contexte d’authentification n’est pas possible pendant la période de préversion. 
> * La préversion est limitée à 25 définitions de contexte d’authentification dans le portail Azure.

Créez de nouvelles définitions de contexte d’authentification en sélectionnant **Nouveau contexte d’authentification** dans le portail Azure. Configurez les attributs suivants :

- Le **Nom d’affichage** est le nom utilisé pour identifier le contexte d’authentification dans Azure AD et dans les applications qui utilisent des contextes d’authentification. Nous recommandons de choisir des noms utilisables dans des ressources, comme « appareils de confiance », afin de réduire le nombre de contextes d’authentification requis. Le fait de disposer d’un ensemble de contextes réduit limite le nombre de redirections et offre une meilleure expérience utilisateur final.
- La **Description** fournit des informations supplémentaires sur les stratégies. Elle est utilisée par les administrateurs Azure AD et les personnes qui appliquent des contextes d’authentification à des ressources.
- La case à cocher **Publier sur les applications**, quand elle est activée, publie le contexte d’authentification sur les applications et rend celle-ci disponibles pour affectation. Si elle n’est pas activée, le contexte d’authentification n’est pas disponible pour les ressources en aval. 
- L’ **ID**, en lecture seule, est utilisé dans des jetons et des applications pour des définitions de contexte d’authentification spécifiques d’une demande. Il est indiqué ici à des fins de résolution des problèmes et de développement. 

Les administrateurs peuvent ensuite sélectionner des contextes d’authentification publiés dans leurs stratégies d’accès conditionnel sous **Affectations** > **Applications ou actions cloud** > **Contexte d’authentification**.

### <a name="tag-resources-with-authentication-contexts"></a>Étiqueter des ressources avec des contextes d’authentification 

Pour plus d’informations sur l’utilisation d’un contexte d’authentification dans des applications, consultez les articles suivants.

- [SharePoint Online](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites?view=o365-worldwide#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Microsoft Cloud App Security](/cloud-app-security/session-policy-aad?branch=pr-en-us-2082#require-step-up-authentication-authentication-context)
- Applications personnalisées

## <a name="next-steps"></a>Étapes suivantes

- [Accès conditionnel : Conditions](concept-conditional-access-conditions.md)
- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)
- [Dépendances des applications clientes](service-dependencies.md)