---
title: Référence des paramètres d’accès conditionnel Azure Active Directory | Microsoft Docs
description: Découvrez les paramètres pris en charge dans une stratégie d’accès conditionnel Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae09b5aeed05dfb09f2b998de805ef0607e7f39
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823545"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Référence des paramètres d’accès conditionnel Azure Active Directory

Vous pouvez utiliser [l’accès conditionnel Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) pour contrôler les méthodes d’accès des utilisateurs autorisés aux ressources.

Cet article fournit des informations de prise en charge sur les options de configuration suivantes dans une stratégie d’accès conditionnel :

- Affectations des applications cloud
- Condition de plateforme d’appareil
- Condition des applications clientes
- Exigence d’une application cliente approuvée

Si ce n’est pas les informations que vous recherchez, laissez un commentaire à la fin de cet article.

## <a name="cloud-apps-assignments"></a>Affectations des applications cloud

Les stratégies d’accès conditionnel vous permettent de contrôler la façon dont les utilisateurs accèdent à vos [applications cloud](conditions.md#cloud-apps-and-actions). Quand vous configurez une stratégie d’accès conditionnel, vous devez sélectionner au moins une application cloud. 

![Sélectionner les applications cloud pour votre stratégie](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Applications cloud Microsoft

Vous pouvez affecter une stratégie d’accès conditionnel aux applications cloud Microsoft  suivantes :

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database et Data Warehouse - [en savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM en ligne
- Microsoft Application Insights Analytique
- Microsoft Azure Information Protection - [en savoir plus](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure Management - [en savoir plus](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Gestion des abonnements Microsoft Azure
- Microsoft Cloud App Security
- Portail de contrôle d’accès des outils de Microsoft Commerce
- Service d’authentification Microsoft Commerce outils
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Inscription à Microsoft Intune
- Planificateur Microsoft
- Microsoft Power BI
- Microsoft PowerApps
- Recherche de Microsoft dans Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Plonger Office
- Office Sway
- Outlook Groups
- Project Online
- Skype Entreprise Online
- Réseau privé virtuel (VPN)
- Visual Studio App Center
- Windows Defender ATP

### <a name="other-applications"></a>Autres applications

Outre les applications cloud Microsoft, vous pouvez affecter une stratégie d’accès conditionnel aux types d’applications cloud suivants :

- applications connectées à Azure AD ;
- application software as a service (SaaS) fédérée préintégrée ;
- applications qui utilisent l’authentification unique (SSO) par mot de passe ;
- applications métier ;
- applications utilisant le proxy d’application Azure AD.

## <a name="device-platform-condition"></a>Condition de plateforme d’appareil

Dans une stratégie d’accès conditionnel, vous pouvez configurer la condition de plateforme d’appareil pour lier la stratégie au système d’exploitation d’un client. L’accès conditionnel Azure AD prend en charge les plateformes suivantes :

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Lier la stratégie d’accès au système d’exploitation client](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Condition d’applications clientes

Dans votre stratégie d’accès conditionnel, vous pouvez configurer la condition [Applications clientes](conditions.md#client-apps) pour lier la stratégie à l’application cliente à l’origine d’une tentative d’accès. Configurez la condition d’applications clientes afin d’accorder ou de bloquer l’accès en cas de tentative d’accès à partir des types d’applications clientes suivantes :

- Browser
- Applications mobiles et de bureau

![Contrôler l’accès pour les applications clientes](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Navigateurs pris en charge

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Navigateurs** comme application cliente.

![Contrôler l’accès pour les navigateurs pris en charge](./media/technical-reference/05.png)

Ce paramètre fonctionne avec tous les navigateurs. Toutefois, pour satisfaire à une stratégie d’appareil, telle qu’une exigence d’appareil conforme, les systèmes d’exploitation et navigateurs suivants sont pris en charge :

| SE                     | Navigateurs                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8 / 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Microsoft Edge, Intune Managed Browser |
| Android                | Chrome, Microsoft Edge, Intune Managed Browser |
| Windows Phone          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Pourquoi un certificat invite dans le navigateur

Sur Windows 7, iOS, Android et macOS, Azure AD identifie l’appareil à l’aide d’un certificat client qui est approvisionné quand l’appareil est inscrit auprès d’Azure AD.  Lorsqu’un utilisateur tout d’abord se connecte via le navigateur, l’utilisateur est invité à sélectionner le certificat. L’utilisateur doit sélectionner ce certificat avant d’utiliser le navigateur.

#### <a name="chrome-support"></a>Prise en charge Chrome

Dans **Windows 10 Creators Update (version 1703)** ou version ultérieure, la prise en charge de Chrome nécessite l’installation de [cette extension](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Pour déployer automatiquement cette extension sur les navigateurs Chrome, créez la clé de Registre suivante :

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Pour la prise en charge de Chrome dans **Windows 8.1 et 7**, créez la clé de Registre suivante :

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (String) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Ces navigateurs prennent en charge l’authentification des appareils, ce qui permet de les identifier et de les valider par rapport à une stratégie. La vérification de l’appareil échoue si le navigateur est en cours d’exécution en mode privé.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Applications mobiles et clients de bureau pris en charge

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Applications mobiles et clients de bureau** comme application cliente.

![Contrôler l’accès pour les applications mobiles ou les clients de bureau pris en charge](./media/technical-reference/06.png)

Ce paramètre a un impact sur les tentatives d’accès provenant des applications mobiles et des clients de bureau suivants :

| Applications clientes | Service cible | Plateforme |
| --- | --- | --- |
| Application Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS et Android |
| Application de messagerie/calendrier/contacts, Outlook 2016, Outlook 2013 (avec l’authentification moderne)| Office 365 Exchange Online | Windows 10 |
| Stratégie MFA et d’emplacement pour les applications. Les stratégies basées sur les appareils ne sont pas prises en charge.| Tout service d’application Mes applications| Android et iOS |
| Services Microsoft Teams, soit tous les services qui prennent en charge Microsoft Teams et toutes ses applications clientes : Bureau Windows, iOS, Android, WP et client web | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android et macOS |
| Applications Office 2016, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Applications Office 2016, applications Universal Office, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), prise en charge à venir des groupes Office et de l’application SharePoint | Office 365 SharePoint Online | Windows 10 |
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

## <a name="app-protection-policy-requirement"></a>Exigence de la stratégie App protection 

Dans votre stratégie d’accès conditionnel, vous pouvez exiger qu'une stratégie de protection d’application est présent sur l’application cliente avant de l’accès est disponible pour les applications cloud sélectionnées. 

![Contrôler l’accès via la stratégie de protection des applications](./media/technical-reference/22.png)

Ce paramètre s’applique aux applications clientes suivantes :

- Microsoft OneDrive
- Microsoft Outlook

**Remarques**

- Applications pour la stratégie de protection des applications prennent en charge la fonctionnalité de gestion d’applications mobiles Intune avec protection de la stratégie.
- Le **nécessitent la stratégie de protection des applications** exigences :
    - elle prend uniquement en charge iOS et Android pour la [condition de plate-forme de périphérique](#device-platform-condition).

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une vue d’ensemble de l’accès conditionnel sur la page [Présentation de l’accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel dans votre environnement, consultez les [Pratiques recommandées d’accès conditionnel dans Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
