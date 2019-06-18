---
title: Configurer la jointure hybride Azure Active Directory pour des domaines fédérés | Microsoft Docs
description: Découvrez comment configurer une jonction Azure Active Directory hybride pour les domaines fédérés.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 600d6b9f1eb8d8073e1658dd5b8196a3d8137e42
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733713"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Didacticiel : Configurer la jointure hybride Azure Active Directory pour des domaines fédérés

À l’instar d’un utilisateur, un appareil est une autre identité essentielle que vous souhaitez protéger et utiliser pour protéger vos ressources à tout moment et en tout lieu. Vous pouvez atteindre cet objectif en intégrant et en gérant les identités des appareils dans Azure AD à l’aide d’une des méthodes ci-dessous :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. Dans le même temps, vous pouvez sécuriser l’accès à ces ressources avec [l’accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Dans ce tutoriel, vous apprenez à configurer une jonction Azure AD hybride pour des ordinateurs joints à un domaine AD dans un environnement fédéré utilisant AD FS.

> [!NOTE]
> Si votre environnement fédéré utilise un autre fournisseur d’identité qu’AD FS, assurez-vous que votre fournisseur d’identité prend en charge le protocole WS-Trust. WS-Trust est nécessaire pour l’authentification de vos appareils Windows actuels joints à Azure AD hybride auprès d’Azure AD. De plus, si vous avez besoin de joindre des appareils Windows de bas niveau avec une jonction Azure AD hybride, votre fournisseur d’identité doit prendre en charge la revendication WIAORMULTIAUTHN. 


> [!div class="checklist"]
> * Configurer une jonction Azure AD hybride
> * Activer des appareils Windows de bas niveau
> * Vérifier l’inscription
> * Résolution des problèmes

## <a name="prerequisites"></a>Prérequis

Ce tutoriel part du principe que vous connaissez :

- [Présentation de la gestion des identités des appareils dans Azure Active Directory](../device-management-introduction.md)
- [Comment planifier l’implémentation de la jointure hybride Azure Active Directory](hybrid-azuread-join-plan.md)
- [Comment effectuer une validation contrôlée de la jonction Azure AD hybride](hybrid-azuread-join-control.md)

Pour configurer le scénario décrit dans ce tutoriel, vous avez besoin de ce qui suit :

- Windows Server 2012 R2 avec AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 ou une version ultérieure

Depuis la version 1.1.819.0, Azure AD Connect est doté d’un Assistant pour configurer une jonction Azure AD hybride. L’Assistant vous permet de simplifier considérablement le processus de configuration. Cet Assistant :

- Configure les points de connexion de service (SCP) pour l’inscription des appareils
- Sauvegarde votre approbation de partie de confiance Azure AD existante
- Met à jour les règles de revendication dans votre approbation Azure AD

Les étapes de configuration décrites dans cet article sont basées sur cet Assistant. Si la version installée d’Azure AD Connect est ancienne, vous devez la mettre à niveau avec la version 1.1.819 ou ultérieure. Si vous n’envisagez pas d’installer la dernière version d’Azure AD Connect, consultez [Comment configurer manuellement la jonction Azure AD hybride](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual).

L’accès des appareils aux ressources Microsoft suivantes depuis le réseau de votre organisation est indispensable à la jonction Azure AD hybride :  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- STS de votre organisation (domaines fédérés)
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`) (Si vous utilisez ou prévoyez d’utiliser l’authentification unique fluide)

À compter de Windows 10 1803, en cas d’échec de la jonction Azure AD hybride instantanée pour un environnement fédéré utilisant AD FS, nous utilisons Azure AD Connect pour synchroniser l’objet ordinateur dans Azure AD, qui sera ensuite utilisé pour terminer l’inscription de l’appareil à une jonction Azure AD hybride. Vérifiez qu’Azure AD Connect a synchronisé les objets ordinateurs des appareils qui deviendront hybrides et joints à Azure AD. Si les objets ordinateurs appartiennent à des unités d’organisation (UO), celles-ci doivent être également configurées du point de vue de la synchronisation dans Azure AD Connect. Pour en savoir plus sur la façon de synchroniser des objets ordinateur à l’aide d’Azure AD Connect, consultez l’article [Configurer le filtrage à l’aide d’Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Si votre organisation a besoin d’un accès à Internet via un proxy sortant, Microsoft recommande d’[implémenter la détection automatique de proxy web (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) pour permettre aux ordinateurs Windows 10 de s’inscrire dans Azure AD. Si vous rencontrez des problèmes pour configurer et gérer WPAD, accédez à la [résolution des problèmes de détection automatique](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Si vous n’utilisez pas WPAD et que vous devez configurer les paramètres du proxy sur votre ordinateur, vous pouvez le faire, à compter de Windows 10 1709, en [configurant les paramètres WinHTTP à l’aide d’un objet de stratégie de groupe (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Si vous configurez les paramètres du proxy sur votre ordinateur à l’aide des paramètres WinHTTP, les ordinateurs qui ne parviennent pas à se connecter au proxy configuré ne pourront pas non plus se connecter à Internet.

Si votre organisation a besoin d’accéder à Internet via un proxy sortant authentifié, assurez-vous que vos ordinateurs Windows 10 parviennent à s’authentifier sur le proxy sortant. Les ordinateurs Windows 10 procèdent à l’inscription des appareils au moyen du contexte ordinateur, il est donc indispensable de configurer l’authentification du proxy sortant avec le contexte ordinateur. Poursuivez avec la configuration requise pour votre fournisseur de proxy sortant.

## <a name="configure-hybrid-azure-ad-join"></a>Configurer une jonction Azure AD hybride

Pour configurer une jonction Azure AD hybride avec Azure AD Connect, vous avez besoin des informations suivantes :

- Informations d’identification d’un administrateur général pour votre locataire Azure AD.  
- Informations d’identification de l’administrateur d’entreprise pour chacune des forêts.
- Informations d’identification de l’administrateur AD FS.

**Pour configurer une jonction Azure AD hybride à l’aide d’Azure AD Connect :**

1. Lancez Azure AD Connect et cliquez sur **Configurer**.

   ![Bienvenue](./media/hybrid-azuread-join-federated-domains/11.png)

1. Dans la page **Tâches supplémentaires**, sélectionnez **Configurer les options de l’appareil** et cliquez sur **Suivant**.

   ![Tâches supplémentaires](./media/hybrid-azuread-join-federated-domains/12.png)

1. Dans la page **Vue d’ensemble**, cliquez sur **Suivant**.

   ![Vue d'ensemble](./media/hybrid-azuread-join-federated-domains/13.png)

1. Dans la page **Connexion à Azure AD**, entrez les informations d’identification d’un administrateur général pour votre locataire Azure AD, puis cliquez sur **Suivant**.

   ![Se connecter à Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Dans la page **Options de l’appareil**, sélectionnez **Configurer joindre Hybrid Azure AD** et cliquez sur **Suivant**.

   ![Options de l’appareil](./media/hybrid-azuread-join-federated-domains/15.png)

1. Dans la page **SCP**, effectuez les étapes suivantes, puis cliquez sur **Suivant** :

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Sélectionnez la forêt.
   1. Sélectionnez le service d’authentification. Vous devez sélectionner le serveur AD FS, sauf si votre organisation a exclusivement des clients Windows 10 et si vous avez configuré la synchronisation ordinateur/appareil ou si votre organisation utilise l’authentification unique fluide.
   1. Cliquez sur **Ajouter** pour indiquer les informations d’identification de l’administrateur d’entreprise.

1. Dans la page **Systèmes d’exploitation des appareils**, sélectionnez les systèmes d’exploitation utilisés par les appareils dans votre environnement Active Directory, puis cliquez sur **suivant**.

   ![Systèmes d’exploitation des appareils](./media/hybrid-azuread-join-federated-domains/17.png)

1. Dans la page **Configuration de la fédération**, entrez les informations d’identification de l’administrateur AD FS, puis cliquez sur **suivant**.

   ![Configuration de la fédération](./media/hybrid-azuread-join-federated-domains/18.png)

1. Dans la page **Prêt à configurer**, cliquez sur **Configurer**.

   ![Prêt à configurer](./media/hybrid-azuread-join-federated-domains/19.png)

1. Dans la page **Configuration terminée**, cliquez sur **Quitter**.

   ![Configuration terminée](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Activer des appareils Windows de bas niveau

Si certains de vos appareils joints à un domaine sont des appareils Windows de bas niveau, vous devez :

- Configurer les paramètres d’intranet local pour l’inscription des appareils
- Installer Microsoft Workplace Join pour les ordinateurs Windows de bas niveau

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurer les paramètres d’intranet local pour l’inscription des appareils

Pour terminer la jonction Azure AD Hybride de vos appareils Windows de bas niveau et éviter les invites de certificat lorsque les appareils s’authentifient auprès d’Azure AD, vous pouvez transmettre une stratégie à vos appareils joints à un domaine afin d’ajouter les URL ci-après à la zone Intranet local dans Internet Explorer :

- `https://device.login.microsoftonline.com`
- Le service d’émission de jeton de sécurité de votre organisation (STS - domaines fédérés)
- `https://autologon.microsoftazuread-sso.com` (pour l’authentification unique fluide).

Vous devez également activer **Autoriser les mises à jour de la barre d’état via le script** dans la zone Intranet local de l’utilisateur.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installer des ordinateurs de bas niveau Workplace Join for Windows

Pour inscrire des ordinateurs Windows de bas niveau, les organisations doivent installer [Microsoft Workplace Join pour les ordinateurs non-Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) à partir du Centre de téléchargement Microsoft.

Vous pouvez déployer le package à l’aide d’un système de distribution de logiciels, comme  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Le package prend en charge les options d’installation sans assistance standard avec le paramètre quiet. La branche actuelle de Configuration Manager offre des avantages supplémentaires par rapport aux versions précédentes, comme la possibilité d’effectuer le suivi des inscriptions effectuées.

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche joint en mode silencieux l’appareil à Azure AD, au moyen des informations d’identification de l’utilisateur, après son authentification auprès d’Azure AD.

## <a name="verify-the-registration"></a>Vérifier l’inscription

Pour vérifier l’état de l’inscription d’un appareil dans votre locataire Azure, vous pouvez utiliser l’applet de commande **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** dans le **[module Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Lorsque vous utilisez l’applet de commande **Get-MSolDevice** pour vérifier les détails du service :

- Un objet dont l’**ID d’appareil** correspond à l’ID défini sur le client Windows doit exister.
- La valeur de **DeviceTrustType** doit être **Domain Joined**. C’est l’équivalent de l’état **Joint à une version hybride d’Azure AD** dans la page Appareils du portail Azure AD.
- La valeur de **Enabled** doit être **True** et celle de **DeviceTrustLevel** doit être **Managed** pour les appareils qui sont utilisés dans l’accès conditionnel.

**Pour vérifier les détails du service :**

1. Ouvrez **Windows PowerShell** en tant qu’administrateur.
1. Tapez `Connect-MsolService` pour vous connecter à votre locataire Azure.  
1. Saisissez `get-msoldevice -deviceId <deviceId>`.
1. Vérifiez que le paramètre **Enabled** est défini sur **True**.

## <a name="troubleshoot-your-implementation"></a>Résoudre les problèmes liés à votre implémentation

Si vous rencontrez des problèmes pour effectuer une jonction Azure AD hybride avec des appareils Windows joints à un domaine, consultez :

- [Résolution des problèmes de jonction Azure AD hybride pour les appareils Windows actuels](troubleshoot-hybrid-join-windows-current.md)
- [Résolution des problèmes de jonction Azure AD hybride pour les appareils Windows de bas niveau](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la gestion des identités des appareils dans le portail Azure AD, consultez [Gestion des identités des appareils dans le portail Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
