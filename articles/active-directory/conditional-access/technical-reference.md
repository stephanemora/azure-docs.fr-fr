---
title: Référence des paramètres d’accès conditionnel Azure Active Directory | Microsoft Docs
description: Découvrez les paramètres pris en charge dans une stratégie d’accès conditionnel Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: reference
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6baf288471ef762689dfa2175ba464ef2091ed7
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544036"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Référence des paramètres d’accès conditionnel Azure Active Directory

Vous pouvez utiliser la fonction [d’accès conditionnel Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) pour contrôler les méthodes d’accès des utilisateurs autorisés aux ressources.

Cet article fournit des informations de prise en charge sur les options de configuration suivantes dans une stratégie d’accès conditionnel :

- Affectations des applications cloud
- Condition de plateforme d’appareil
- Condition des applications clientes
- Exigence d’une application cliente approuvée

## <a name="cloud-apps-assignments"></a>Affectations des applications cloud

Les stratégies d’accès conditionnel vous permettent de contrôler la façon dont les utilisateurs accèdent à vos [applications cloud](conditions.md#cloud-apps-and-actions). Lorsque vous configurez une stratégie d’accès conditionnel, vous devez sélectionner au moins une application cloud. 

![Sélectionner les applications cloud pour votre stratégie](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Applications cloud Microsoft

Vous pouvez affecter une stratégie d’accès conditionnel aux applications cloud Microsoft suivantes :

- Office 365 (préversion)
- Azure Analysis Services
- Azure DevOps
- Azure SQL Database et Data Warehouse - [En savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM en ligne
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection - [En savoir plus](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Gestion Microsoft Azure - [En savoir plus](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Gestion des abonnements Microsoft Azure
- Microsoft Cloud App Security
- Portail de contrôle des accès aux outils de Microsoft Commerce
- Service d’authentification des outils Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Inscription à Microsoft Intune
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

Office 365 fournit des services de collaboration et de productivité informatiques comme Exchange, SharePoint et Microsoft Teams. Les services cloud Office 365 sont profondément intégrés pour garantir des expériences fluides et collaboratives. L’application Office 365 (préversion) permet de cibler ces services en même temps. Nous vous recommandons d’utiliser la nouvelle application Office 365 (préversion) plutôt que de cibler des applications cloud individuelles comme Office 365 Exchange Online et Office 365 SharePoint Online afin d’éviter les problèmes susceptibles de survenir en raison de stratégies incohérentes et de dépendances de service.

Applications clés incluses dans l’application cliente Office 365 (préversion) :

- Office 365 Exchange Online
- Office 365 SharePoint Online
- Microsoft Teams
- Office 365 Yammer
- Office.com
- Microsoft Forms
- Microsoft Power Automate
- Planificateur Microsoft
- Microsoft PowerApps

### <a name="other-applications"></a>Autres applications

Outre les applications cloud Microsoft, vous pouvez affecter une stratégie d’accès conditionnel aux types d’applications cloud suivants :

- applications connectées à Azure AD ;
- application software as a service (SaaS) fédérée préintégrée ;
- applications qui utilisent l’authentification unique (SSO) par mot de passe ;
- applications métier ;
- applications utilisant le proxy d’application Azure AD.

## <a name="device-platform-condition"></a>Condition de plateforme d’appareil

Dans une stratégie d’accès conditionnel, vous pouvez configurer la condition de plateforme d’appareil pour lier la stratégie au système d’exploitation d’un client. L’accès conditionnel Azure AD prend en charge les plateformes d’appareil suivantes :

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Lier la stratégie d’accès au système d’exploitation client](./media/technical-reference/41.png)

Si vous bloquez l’authentification héritée à l’aide de la condition **Autres clients**, vous pouvez également définir la condition de plateforme de l’appareil.

## <a name="client-apps-condition"></a>Condition d’applications clientes

Dans votre stratégie d’accès conditionnel, vous pouvez configurer la condition [Applications clientes](conditions.md#client-apps) pour lier la stratégie à l’application cliente à l’origine d’une tentative d’accès. Configurez la condition d’applications clientes afin d’accorder ou de bloquer l’accès en cas de tentative d’accès à partir des types d’applications clientes suivantes :

- Browser
- Applications mobiles et de bureau

![Contrôler l’accès pour les applications clientes](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Navigateurs pris en charge

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Navigateurs** en tant qu’application cliente.

![Contrôler l’accès pour les navigateurs pris en charge](./media/technical-reference/05.png)

Ce paramètre fonctionne avec tous les navigateurs. Toutefois, pour satisfaire à une stratégie d’appareil, telle qu’une exigence d’appareil conforme, les systèmes d’exploitation et navigateurs suivants sont pris en charge :

| Système d''exploitation                     | Navigateurs                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8 / 8.1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Pourquoi une invite de saisie de certificat s’affiche-t-elle dans mon navigateur ?

Sur des systèmes Windows 7, iOS, Android et macOS, Azure AD identifie l’appareil à l’aide d’un certificat client, qui est approvisionné lorsque l’appareil est inscrit auprès d’Azure AD.  Lorsqu’un utilisateur se connecte pour la première fois via le navigateur, l’utilisateur est invité à sélectionner le certificat. Il doit sélectionner ce certificat avant d’utiliser le navigateur.

#### <a name="chrome-support"></a>Prise en charge Chrome

Dans **Windows 10 Creators Update (version 1703)** ou version ultérieure, la prise en charge de Chrome nécessite l’installation de l’[extension Comptes Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Cette extension est requise lorsqu’une stratégie d’accès conditionnel requiert des détails spécifiques sur l’appareil.

Pour déployer automatiquement cette extension sur les navigateurs Chrome, créez la clé de Registre suivante :

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (String) |
| Données | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

Pour la prise en charge de Chrome dans **Windows 8.1 et 7**, créez la clé de Registre suivante :

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (String) |
| Données | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Ces navigateurs prennent en charge l’authentification des appareils, ce qui permet de les identifier et de les valider par rapport à une stratégie. La vérification de l’appareil échoue si le navigateur est en cours d’exécution en mode privé.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applications mobiles et clients de bureau pris en charge

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Applications mobiles et clients de bureau** en tant qu’application cliente.

![Contrôler l’accès pour les applications mobiles ou les clients de bureau pris en charge](./media/technical-reference/06.png)

Ce paramètre a un impact sur les tentatives d’accès provenant des applications mobiles et des clients de bureau suivants :

| Applications clientes | Service cible | Plateforme |
| --- | --- | --- |
| Application Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS et Android |
| Application de messagerie/calendrier/contacts, Outlook 2016, Outlook 2013 (avec l’authentification moderne)| Office 365 Exchange Online | Windows 10 |
| Stratégie MFA et d’emplacement pour les applications. Les stratégies basées sur les appareils ne sont pas prises en charge.| Tout service d’application Mes applications| Android et iOS |
| Services Microsoft Teams, soit tous les services qui prennent en charge Microsoft Teams et toutes ses applications clientes : Bureau Windows, iOS, Android, WP et client web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android et macOS |
| Applications Office 2016, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Applications Office 2016, applications Universal Office, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), prise en charge à venir des groupes Office et de l’application SharePoint | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, OneNote uniquement). Prise en charge de OneDrive Entreprise prévue| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Applications mobiles Office | Office 365 SharePoint Online | Android, iOS |
| Application Yammer Office | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office pour Mac OS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (avec authentification moderne), Skype Entreprise (avec authentification moderne) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Application Outlook Mobile | Office 365 Exchange Online | Android, iOS |
| Application Power BI | Service Power BI | Windows 10, Windows 8.1, Windows 7, Android et iOS |
| Skype Entreprise | Office 365 Exchange Online| Android, IOS |
| Application Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS, Android |

## <a name="support-for-legacy-authentication"></a>Prise en charge de l’authentification héritée

En sélectionnant **Autres clients**, vous pouvez spécifier une condition affectant les applications qui utilisent l’authentification de base avec des protocoles de messagerie comme IMAP, MAPI, POP et SMTP ainsi que les applications Office plus anciennes qui n’utilisent pas l’authentification moderne.  

![Autres clients](./media/technical-reference/11.png)

Pour plus d’informations, consultez les [applications clientes](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Spécification d’application cliente approuvée

Dans votre stratégie d’accès conditionnel, vous pouvez exiger que toute tentative d’accès aux applications cloud sélectionnées provienne d’une application cliente approuvée. 

![Contrôler l’accès pour les applications clientes approuvées](./media/technical-reference/21.png)

Ce paramètre s’applique aux applications clientes suivantes :

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

**Remarques**

- Les applications clientes approuvées prennent en charge la fonctionnalité de gestion des applications mobiles Intune.
- Exigence **Nécessite une application cliente approuvée** :
   - elle prend uniquement en charge iOS et Android pour la [condition de plate-forme de périphérique](#device-platform-condition).
- L’accès conditionnel ne peut considérer Microsoft Edge en mode InPrivate en tant qu'application cliente approuvée.

## <a name="app-protection-policy-requirement"></a>Présence obligatoire d’une stratégie de protection des applications 

Dans votre stratégie d’accès conditionnel, vous pouvez exiger qu’une stratégie de protection des applications soit présente sur l’application cliente pour qu’il soit possible d’accéder aux applications cloud sélectionnées. 

![Contrôler les accès avec une stratégie de protection des applications](./media/technical-reference/22.png)

Ce paramètre s’applique aux applications clientes suivantes :

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Planificateur Microsoft

**Remarques**

- Les applications associées à la stratégie de protection des applications prennent en charge la fonctionnalité de gestion d’applications mobiles Intune avec la protection des stratégies.
- Exigences relatives à la stratégie **Exiger une stratégie de protection des applications** :
    - elle prend uniquement en charge iOS et Android pour la [condition de plate-forme de périphérique](#device-platform-condition).

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une vue d’ensemble de l’accès conditionnel sur la page [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel dans votre environnement, consultez les [Pratiques recommandées en matière d’accès conditionnel dans Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
