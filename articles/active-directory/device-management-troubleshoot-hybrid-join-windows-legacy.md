---
title: Dépanner des appareils hybrides de bas niveau joints à Azure Active Directory | Microsoft Docs
description: Dépannez des appareils hybrides de bas niveau joints à Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b99a82b59cbba22d30a4325e246c308a2042ad5
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136305"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Dépanner des appareils hybrides de bas niveau joints à Azure Active Directory 

Cet article s’applique uniquement aux appareils suivants : 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Pour Windows 10 ou Windows Server 2016, consultez la page [Dépanner des appareils hybrides Windows 10 et Windows Server 2016 joints à Azure Active Directory](device-management-troubleshoot-hybrid-join-windows-current.md).

Cet article suppose que vous avez [configuré des appareils hybrides joints à Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md) de façon à prendre en charge les scénarios suivants :

- Accès conditionnel basé sur les appareils

- [Itinérance d’entreprise des paramètres](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello Entreprise](active-directory-azureadjoin-passport-deployment.md) 





Cet article vous fournit des conseils sur la façon de résoudre les problèmes potentiels.  

**Bon à savoir :** 

- Le nombre maximal d’appareils par utilisateur dépend de l’appareil. Par exemple, si *jdoe* et *jharnett* se connectent à un appareil, une inscription (DeviceID) distincte est créée pour chacun d’eux dans l’onglet d’informations **UTILISATEUR**.  

- L’inscription / jointure d’appareils initiale est configurée pour effectuer une tentative à l’ouverture de session ou au verrouillage / déverrouillage. Un délai de cinq minutes peut être déclenché par une tâche du Planificateur de tâches. 

- Une réinstallation du système d’exploitation ou une réinscription manuelle est susceptible de créer une nouvelle inscription dans Azure AD, et ainsi de générer plusieurs entrées sous l’onglet d’informations UTILISATEUR dans le Portail Azure. 

- Si le système d’exploitation est Windows 7 SP1 ou Windows Server 2008 R2 SP1, vérifiez que [KB4284842](https://support.microsoft.com/en-us/help/4284842) est installé. Cette mise à jour empêche les échecs d’authentification qui se produisent lorsque le client perd l’accès aux clés protégées après avoir modifié le mot de passe.

## <a name="step-1-retrieve-the-registration-status"></a>Étape 1 : Récupérer l’état de l’inscription 

**Pour vérifier l’état de l’inscription :**  

1. Connectez-vous avec le compte d’utilisateur qui a effectué une jointure à Azure AD hybride.

2. Ouvrez une invite de commandes en tant qu’administrateur. 

3. Saisissez `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Cette commande affiche une boîte de dialogue qui vous donne plus de détails sur l’état de la jonction.

![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Étape 2 : Évaluer l’état de la jointure Azure AD hybride 

Si la jointure Azure AD hybride n’a pas réussi, la boîte de dialogue vous fournit des informations sur le problème qui s’est produit.

**Les tâches les plus courantes sont :**

- Une mauvaise configuration d’AD FS ou d’Azure AD

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Vous n’êtes pas connecté en tant qu’utilisateur du domaine

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Cette situation peut se produire pour différentes raisons :
    
    - L’utilisateur connecté n’est pas un utilisateur de domaine (par exemple, un utilisateur local). La jointure Azure AD hybride sur les appareils de bas niveau est uniquement prise en charge pour les utilisateurs de domaine.
    
    - Autoworkplace.exe ne peut pas s’authentifier sans assistance auprès d’Azure AD ou d’AD FS. Il existe peut-être des problèmes de connectivité réseau sortante aux URL Azure AD. Le problème peut également être lié au fait que l’authentification multifacteur (MFA) est activée/configurée pour l’utilisateur alors que WIAORMUTLIAUTHN n’est pas configuré sur le serveur de fédération. Ce problème peut également découler du fait que la page de découverte du domaine d’accueil (HRD) attend une intervention de la part de l’utilisateur, ce qui empêche **autoworkplace.exe** d’obtenir un jeton sans assistance.
    
    - Votre organisation utilise l’authentification unique Azure AD sans interruption, `https://autologon.microsoftazuread-sso.com` ou `https://aadg.windows.net.nsatc.net` ne sont pas présents dans les paramètres intranet Internet Explorer de l’appareil et l’option **Autoriser les mises à jour de la barre d’état via le script** n’est pas activée pour la zone Intranet.

- Un quota a été atteint.

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Le service ne répond pas. 

    ![Workplace Join pour Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Vous pouvez également trouver les informations d’état dans le journal des événements sous **Applications and Services Log\Microsoft-Workplace Join**.
  
**Voici les causes les plus courantes d’échec d’une jointure Azure AD hybride :** 

- Votre ordinateur n’est pas connecté au réseau interne de votre organisation, ni à un VPN avec une connexion à votre contrôleur de domaine AD local.

- Vous êtes connecté à votre ordinateur avec un compte d’ordinateur local. 

- Problèmes de configuration du service : 

  - Le serveur de fédération a été configuré pour prendre en charge **WIAORMULTIAUTHN**. 

  - La forêt de votre ordinateur n’a aucun objet de point de connexion de service qui pointe vers votre nom de domaine vérifié dans Azure AD. 

  - Un utilisateur a atteint la limite d’appareils. 

## <a name="next-steps"></a>Étapes suivantes

Pour toute question, consultez [FAQ sur la gestion des appareils](device-management-faq.md)  
