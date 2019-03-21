---
title: Configurer la jointure hybride Azure Active Directory pour des domaines fédérés | Microsoft Docs
description: Découvrez comment configurer une jonction Azure Active Directory hybride pour les domaines fédérés.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f9045246366d8595dd47b1e1be50e966859759b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58163906"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutoriel : Configurer la jointure hybride Azure Active Directory pour des domaines fédérés

À l’instar d’un utilisateur, un appareil devient une autre identité que vous souhaitez protéger et aussi utiliser pour protéger vos ressources à tout moment et en tout lieu. Vous pouvez atteindre cet objectif en intégrant les identités de vos appareils à Azure AD suivant l’une des méthodes ci-dessous :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En mettant vos appareils sur Azure AD, vous optimisez la productivité de vos utilisateurs par le biais de l’authentification unique (SSO) sur vos ressources cloud et locales. En même temps, vous pouvez sécuriser l’accès à vos ressources cloud et locales avec l’[accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Dans ce tutoriel, vous apprenez à configurer une jonction Azure AD hybride pour les appareils qui ont fédéré avec ADFS.

> [!div class="checklist"]
> * Configurer une jonction Azure AD hybride
> * Activer des appareils Windows de bas niveau
> * Vérifier l’inscription
> * Résolution des problèmes


## <a name="prerequisites"></a>Prérequis

Ce tutoriel part du principe que vous connaissez :

-  [Présentation de la gestion des appareils dans Azure Active Directory](../device-management-introduction.md)

-  [Comment planifier l’implémentation de la jointure hybride Azure Active Directory](hybrid-azuread-join-plan.md)

-  [Comment contrôler la jointure hybride Azure Active Directory pour vos appareils](hybrid-azuread-join-control.md)



Pour configurer le scénario décrit dans ce tutoriel, vous avez besoin de ce qui suit :

- Windows Server 2012 R2 avec AD FS

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 ou une version ultérieure 
 

Depuis la version 1.1.819.0, Azure AD Connect est doté d’un Assistant pour configurer une jonction Azure AD hybride. L’Assistant vous permet de simplifier considérablement le processus de configuration. Cet Assistant :

- Configure les points de connexion de service (SCP) pour l’inscription des appareils

- Sauvegarde votre approbation de partie de confiance Azure AD existante

- Met à jour les règles de revendication dans votre approbation Azure AD

Les étapes de configuration décrites dans cet article sont basées sur cet Assistant. Si la version installée d’Azure AD Connect est ancienne, vous devez la mettre à niveau avec la version 1.1.819 ou ultérieure. Si vous n’envisagez pas d’installer la dernière version d’Azure AD Connect, consultez [Guide pratique pour configurer manuellement l’inscription des appareils](../device-management-hybrid-azuread-joined-devices-setup.md).

L’accès des appareils aux ressources Microsoft suivantes depuis le réseau de votre organisation est indispensable à la jonction Azure AD hybride :  

- https\://enterpriseregistration.windows.net
- https\://login.microsoftonline.com
- https\://device.login.microsoftonline.com
- STS de votre organisation (domaines fédérés)
- https\://autologon.microsoftazuread-sso.com (si vous utilisez ou prévoyez d’utiliser l’authentification unique fluide)

À compter de Windows 10 1803, si la jonction Azure AD hybride instantanée d’un domaine fédéré tel qu’AD FS tombe en panne, nous nous appuyons sur Azure AD Connect pour synchroniser l’objet ordinateur dans Azure AD, qui sera ensuite utilisé pour terminer l’inscription de l’appareil à une jointure Azure AD hybride.

Si votre organisation nécessite l’accès à Internet via un proxy sortant, à compter de Windows 10 1709, vous pouvez [configurer les paramètres du proxy sur votre ordinateur à l’aide d’un objet de stratégie de groupe (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Si la version de Windows qui s’exécute sur votre ordinateur est antérieure à Windows 10 1709, vous devez implémenter la détection automatique de proxy Web (WPAD) pour permettre aux ordinateurs Windows 10 de procéder à l’inscription des appareils auprès d’Azure AD. 

Si votre organisation a besoin d’accéder à Internet via un proxy sortant authentifié, assurez-vous que vos ordinateurs Windows 10 parviennent à s’authentifier sur le proxy sortant. Les ordinateurs Windows 10 procèdent à l’inscription des appareils au moyen du contexte ordinateur, il est donc indispensable de configurer l’authentification du proxy sortant avec le contexte ordinateur. Poursuivez avec la configuration requise pour votre fournisseur de proxy sortant. 


## <a name="configure-hybrid-azure-ad-join"></a>Configurer une jonction Azure AD hybride

Pour configurer une jonction Azure AD hybride avec Azure AD Connect, vous avez besoin des informations suivantes :

- Informations d’identification d’un administrateur général pour votre locataire Azure AD.  

- Informations d’identification de l’administrateur d’entreprise pour chacune des forêts.

- Informations d’identification de l’administrateur AD FS. 


**Pour configurer une jonction Azure AD hybride à l’aide d’Azure AD Connect :**

1. Lancez Azure AD Connect et cliquez sur **Configurer**.

    ![Bienvenue](./media/hybrid-azuread-join-federated-domains/11.png)

2. Dans la page **Tâches supplémentaires**, sélectionnez **Configurer les options de l’appareil** et cliquez sur **Suivant**. 

    ![Tâches supplémentaires](./media/hybrid-azuread-join-federated-domains/12.png)

3. Dans la page **Vue d’ensemble**, cliquez sur **Suivant**. 

    ![Vue d’ensemble](./media/hybrid-azuread-join-federated-domains/13.png)

4. Dans la page **Connexion à Azure AD**, entrez les informations d’identification d’un administrateur général pour votre locataire Azure AD, puis cliquez sur **Suivant**.   

    ![Se connecter à Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

5. Dans la page **Options de l’appareil**, sélectionnez **Configurer joindre Hybrid Azure AD** et cliquez sur **Suivant**. 

    ![Options de l’appareil](./media/hybrid-azuread-join-federated-domains/15.png)

6. Dans la page **SCP**, effectuez les étapes suivantes, puis cliquez sur **Suivant** : 

    ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

    a. Sélectionnez la forêt.

    b. Sélectionnez le service d’authentification. Vous devez sélectionner le serveur AD FS, sauf si votre organisation a exclusivement des clients Windows 10 et si vous avez configuré la synchronisation ordinateur/appareil ou si votre organisation utilise l’authentification unique fluide.

    c. Cliquez sur **Ajouter** pour indiquer les informations d’identification de l’administrateur d’entreprise.


7. Dans la page **Systèmes d’exploitation des appareils**, sélectionnez les systèmes d’exploitation utilisés par les appareils dans votre environnement Active Directory, puis cliquez sur **suivant**. 

    ![Systèmes d’exploitation des appareils](./media/hybrid-azuread-join-federated-domains/17.png)

8. Dans la page **Configuration de la fédération**, entrez les informations d’identification de l’administrateur AD FS, puis cliquez sur **suivant**. 

    ![Configuration de la fédération](./media/hybrid-azuread-join-federated-domains/18.png)

9. Dans la page **Prêt à configurer**, cliquez sur **Configurer**. 

    ![Prêt à configurer](./media/hybrid-azuread-join-federated-domains/19.png)

10. Dans la page **Configuration terminée**, cliquez sur **Quitter**. 

    ![Configuration terminée](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Activer des appareils Windows de bas niveau

Si certains de vos appareils joints à un domaine sont des appareils Windows de bas niveau, vous devez :

- Mettre à jour les paramètres des appareils
 
- Configurer les paramètres d’intranet local pour l’inscription des appareils

- Contrôler des appareils Windows de bas niveau 


### <a name="update-device-settings"></a>Mettre à jour les paramètres des appareils 

Pour inscrire des appareils Windows de bas niveau, vous devez vous assurer que le paramètre des appareils permettant aux utilisateurs d’inscrire des appareils dans Azure AD est défini. Dans le portail Azure, ce paramètre se trouve à l’emplacement suivant :

`Home > [Name of your tenant] > Devices - Device settings`  


    
La stratégie suivante doit être définie sur **All** (Tout) : **Les utilisateurs peuvent inscrire leurs appareils sur Azure AD**

![Inscrire des appareils](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurer les paramètres d’intranet local pour l’inscription des appareils

Pour terminer la jonction Azure AD Hybride de vos appareils Windows de bas niveau et éviter les invites de certificat lorsque les appareils s’authentifient auprès d’Azure AD, vous pouvez transmettre une stratégie à vos appareils joints à un domaine afin d’ajouter les URL ci-après à la zone Intranet local dans Internet Explorer :

- `https://device.login.microsoftonline.com`

- Le service d’émission de jeton de sécurité de votre organisation (STS - domaines fédérés)

- `https://autologon.microsoftazuread-sso.com` (pour l’authentification unique fluide).

Vous devez également activer **Autoriser les mises à jour de la barre d’état via le script** dans la zone Intranet local de l’utilisateur.



### <a name="control-windows-down-level-devices"></a>Contrôler des appareils Windows de bas niveau 

Pour inscrire des appareils Windows de bas niveau, vous devez télécharger et installer un package Windows Installer (.msi) à partir du Centre de téléchargement. Pour plus d’informations, cliquez [ici](hybrid-azuread-join-control.md#control-windows-down-level-devices). 

## <a name="verify-the-registration"></a>Vérifier l’inscription

Pour vérifier l’état de l’inscription d’un appareil dans votre locataire Azure, vous pouvez utiliser l’applet de commande **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** dans le **[module Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Lorsque vous utilisez l’applet de commande **Get-MSolDevice** pour vérifier les détails du service :

- Un objet dont l’**id d’appareil** correspond à l’ID présent sur le client Windows doit exister.
- La valeur de **DeviceTrustType** doit être **Domain Joined**. C’est l’équivalent de l’état **Joint à une version hybride d’Azure AD** dans la page Appareils du portail Azure AD.
- La valeur de **Enabled** doit être **True** et celle de **DeviceTrustLevel** doit être **Managed** pour les appareils qui sont utilisés dans l’accès conditionnel. 


**Pour vérifier les détails du service :**

1. Ouvrez **Windows PowerShell** en tant qu’administrateur.

2. Tapez `Connect-MsolService` pour vous connecter à votre locataire Azure.  

3. Saisissez `get-msoldevice -deviceId <deviceId>`.

6. Vérifiez que le paramètre **Enabled** est défini sur **True**.





## <a name="troubleshoot-your-implementation"></a>Résoudre les problèmes liés à votre implémentation

Si vous rencontrez des problèmes pour effectuer une jonction Azure AD hybride avec des appareils Windows joints à un domaine, consultez :

- [Résolution des problèmes de jonction Azure AD hybride pour les appareils Windows actuels](troubleshoot-hybrid-join-windows-current.md)
- [Résolution des problèmes de jonction Azure AD hybride pour les appareils Windows de bas niveau](troubleshoot-hybrid-join-windows-legacy.md)



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer une jonction Azure Active Directory hybride pour les domaines managés](hybrid-azuread-join-managed-domains.md)
> [Configurer manuellement une jonction Azure Active Directory hybride](hybrid-azuread-join-manual-steps.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
