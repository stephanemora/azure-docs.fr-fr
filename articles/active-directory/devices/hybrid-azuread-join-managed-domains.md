---
title: Configurer la jointure hybride Azure Active Directory pour des domaines managés | Microsoft Docs
description: Découvrez comment configurer une jonction Azure Active Directory hybride pour les domaines managés.
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
ms.openlocfilehash: 17bfbc29f38230dc2533c9ccc63cdee4fc776717
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512106"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutoriel : Configurer la jointure hybride Azure Active Directory pour des domaines managés

Comme n’importe quel utilisateur de votre organisation, un appareil est une identité fondamentale que vous souhaitez protéger. Vous pouvez utiliser l’identité d’un appareil pour protéger vos ressources à tout moment et à partir de n’importe quel emplacement. Pour atteindre cet objectif, vous devez intégrer et gérer les identités des appareils dans Azure AD (Azure Active Directory) à l’aide de l’une des méthodes suivantes :

- jointure Azure AD ;
- jointure Azure AD hybride ;
- inscription Azure AD.

En intégrant vos appareils à Azure AD, vous optimisez la productivité des utilisateurs via SSO (authentification unique) parmi vos ressources cloud et locales. En parallèle, vous pouvez sécuriser l’accès à vos ressources cloud et locales à l’aide de l’[accès conditionnel](../active-directory-conditional-access-azure-portal.md).

Dans ce tutoriel, vous allez apprendre à configurer une jonction Azure AD Hybride pour des ordinateurs/appareils appartenant à un domaine Active Directory dans un environnement managé. 

Un environnement managé peut être déployé via la [synchronisation de hachage de mot de passe (PHS)](../hybrid/whatis-phs.md) ou l’[authentification directe (PTA)](../hybrid/how-to-connect-pta.md) avec l’[authentification unique fluide](../hybrid/how-to-connect-sso.md). Ces scénarios ne nécessitent pas la configuration d’un serveur de fédération pour l’authentification.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Configurer une jonction Azure AD hybride
> * Activer des appareils Windows de bas niveau
> * Vérifier des appareils joints
> * Dépanner

## <a name="prerequisites"></a>Conditions préalables requises

Ce tutoriel part du principe que les articles suivants vous sont familiers :

- [Qu’est-ce qu’une identité d’appareil ?](overview.md)
- [Guide pratique pour planifier votre implémentation de la jonction Azure AD Hybride](hybrid-azuread-join-plan.md)
- [Comment effectuer une validation contrôlée de la jonction Azure AD hybride](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD ne prend pas en charge les cartes à puce ou les certificats dans les domaines managés.

Pour permettre la configuration du scénario décrit dans cet article, la [dernière version d’Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou ultérieur) doit être installée.

Vérifiez qu’Azure AD Connect a synchronisé les objets ordinateur des appareils qui doivent devenir membres d’Azure AD Hybride. Si les objets ordinateur appartiennent à des unités d’organisation spécifiques, vous devez également configurer ces unités d’organisation pour qu’elles se synchronisent dans Azure AD Connect. Pour en savoir plus sur la synchronisation des objets ordinateur à l’aide d’Azure AD Connect, consultez [Configurer le filtrage à l’aide d’Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Depuis la version 1.1.819.0, Azure AD Connect comprend un Assistant qui vous permet de configurer la jonction Azure AD Hybride. L’Assistant simplifie considérablement le processus de configuration. L’Assistant configure les SCP (points de connexion de service) pour l’inscription des appareils.

Les étapes de configuration décrites dans cet article sont basées sur l’utilisation de l’Assistant dans Azure AD Connect.

Pour permettre le bon fonctionnement de la jonction Azure AD Hybride, les appareils doivent avoir accès aux ressources Microsoft suivantes sur le réseau de votre organisation :  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (si vous utilisez ou prévoyez d’utiliser l’authentification unique fluide)

Si votre organisation nécessite un accès à Internet via un proxy sortant, Microsoft recommande d’[implémenter le protocole WPAD (Web Proxy AutoDiscovery)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) pour permettre aux ordinateurs Windows 10 de s’inscrire en tant qu’appareils auprès d’Azure AD. Si vous rencontrez des problèmes pour configurer et gérer le protocole WPAD, consultez la [résolution des problèmes de détection automatique](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Si vous n’utilisez pas WPAD et si vous devez configurer des paramètres de proxy sur votre ordinateur, vous pouvez le faire à partir de Windows 10 1709. Pour plus d’informations, consultez [Configurer les paramètres WinHTTP à l’aide d’un GPO (objet de stratégie de groupe)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Si vous configurez les paramètres du proxy sur votre ordinateur à l’aide des paramètres WinHTTP, les ordinateurs qui ne peuvent pas se connecter au proxy configuré ne pourront pas non plus se connecter à Internet.

Si votre organisation nécessite un accès à Internet via un proxy sortant authentifié, vérifiez que vos ordinateurs Windows 10 parviennent à s’authentifier correctement auprès du proxy sortant. Dans la mesure où les ordinateurs Windows 10 effectuent l’inscription de l’appareil en fonction du contexte de la machine, vous devez configurer l’authentification du proxy sortant selon le contexte de la machine. Poursuivez avec la configuration requise pour votre fournisseur de proxy sortant.

Pour vérifier si l’appareil est en mesure d’accéder aux ressources Microsoft ci-dessus sous le compte système, vous pouvez utiliser le script [Tester la connectivité de l’inscription des appareils](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0).

## <a name="configure-hybrid-azure-ad-join"></a>Configurer une jonction Azure AD hybride

Pour configurer une jonction Azure AD hybride avec Azure AD Connect, vous avez besoin des informations suivantes :

- Informations d’identification d’un administrateur général pour votre locataire Azure AD
- Informations d’identification de l’administrateur d’entreprise pour chacune des forêts

**Pour configurer une jonction Azure AD Hybride à l’aide d’Azure AD Connect :**

1. Démarrez Azure AD Connect, puis sélectionnez **Configurer**.

   ![Bienvenue](./media/hybrid-azuread-join-managed-domains/11.png)

1. Dans la page **Tâches supplémentaires**, sélectionnez **Configurer les options de l’appareil**, puis **Suivant**.

   ![Tâches supplémentaires](./media/hybrid-azuread-join-managed-domains/12.png)

1. Dans la page **Vue d’ensemble**, sélectionnez **Suivant**.

   ![Vue d’ensemble](./media/hybrid-azuread-join-managed-domains/13.png)

1. Dans la page **Connexion à Azure AD**, entrez les informations d’identification d’un administrateur général pour votre locataire Azure AD.  

   ![Se connecter à Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Dans la page **Options de l’appareil**, sélectionnez **Configurer la jonction Azure AD Hybride**, puis **Suivant**.

   ![Options de l’appareil](./media/hybrid-azuread-join-managed-domains/15.png)

1. Dans la page **SCP**, pour chaque forêt où vous souhaitez qu’Azure AD Connect configure le SCP (point de connexion de service), effectuez les étapes suivantes, puis sélectionnez **Suivant** :

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Sélectionnez la forêt.
   1. Sélectionnez le service d’authentification.
   1. Sélectionnez **Ajouter** pour entrer les informations d’identification de l’administrateur d’entreprise.

1. Dans la page **Systèmes d’exploitation des appareils**, sélectionnez les systèmes d’exploitation utilisés par les appareils dans votre environnement Active Directory, puis sélectionnez **Suivant**.

   ![Systèmes d’exploitation des appareils](./media/hybrid-azuread-join-managed-domains/17.png)

1. Dans la page **Prêt à configurer**, sélectionnez **Configurer**.

   ![Prêt à configurer](./media/hybrid-azuread-join-managed-domains/19.png)

1. Dans la page **Configuration terminée**, sélectionnez **Quitter**.

   ![Configuration terminée](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Activer des appareils Windows de niveau inférieur

Si certains de vos appareils appartenant à un domaine sont des appareils Windows de niveau inférieur, vous devez :

- Configurer les paramètres d’intranet local pour l’inscription des appareils
- Configurer l’authentification unique fluide
- Installer Microsoft Workplace Join pour des ordinateurs Windows de niveau inférieur

> [!NOTE]
> Le support de Windows 7 a pris fin le 14 janvier 2020. Pour plus d’informations, consultez [Le support de Windows 7 a pris fin le 14 janvier 2020](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Configurer les paramètres d’intranet local pour l’inscription des appareils

Pour effectuer correctement la jonction Azure AD Hybride de vos appareils Windows de niveau inférieur et éviter les invites de certificat quand les appareils s’authentifient auprès d’Azure AD, vous pouvez envoyer (push) une stratégie aux appareils appartenant à un domaine afin d’ajouter les URL suivantes à la zone intranet locale dans Internet Explorer :

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Vous devez également activer **Autoriser les mises à jour de la barre d’état via le script** dans la zone Intranet local de l’utilisateur.

### <a name="configure-seamless-sso"></a>Configurer l’authentification unique fluide

Pour effectuer correctement la jonction Azure AD Hybride de vos appareils Windows de niveau inférieur dans un domaine managé qui utilise [PHS](../hybrid/whatis-phs.md) ou [PTA](../hybrid/how-to-connect-pta.md) en tant que méthode d’authentification des services cloud Azure AD, vous devez également [configurer l’authentification unique fluide](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installer Microsoft Workplace Join pour des ordinateurs Windows de niveau inférieur

Pour inscrire les appareils Windows de niveau inférieur, les organisations doivent installer [Microsoft Workplace Join pour les ordinateurs non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pour les ordinateurs non Windows 10 est disponible dans le Centre de téléchargement Microsoft.

Vous pouvez déployer le package à l’aide d’un système de distribution de logiciels, comme  [Microsoft Endpoint Configuration Manager](https://docs.microsoft.com/configmgr/). Le package prend en charge les options d’installation sans assistance standard avec le paramètre `quiet`. La branche actuelle de Configuration Manager offre des avantages supplémentaires par rapport aux versions précédentes, comme la possibilité d’effectuer le suivi des inscriptions effectuées.

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche joint l’appareil en mode sans assistance à Azure AD avec les informations d’identification de l’utilisateur, après son authentification auprès d’Azure AD.

## <a name="verify-the-registration"></a>Vérifier l’inscription

Pour vérifier l’état de l’inscription d’un appareil dans votre locataire Azure, vous pouvez utiliser l’applet de commande **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** dans le [module Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Quand vous utilisez l’applet de commande **Get-MSolDevice** pour vérifier les détails du service :

- Un objet dont l’**ID d’appareil** correspond à l’ID défini sur le client Windows doit exister.
- La valeur de **DeviceTrustType** doit être **Domain Joined**. Ce paramètre équivaut à l’état **Joint à une version hybride d’Azure AD** dans la page **Appareils** du portail Azure AD.
- Pour les appareils utilisés dans l’accès conditionnel, la valeur de **Enabled** doit être **True** et celle de **DeviceTrustLevel** doit être **Managed**.

**Pour vérifier les détails du service** :

1. Ouvrez Windows PowerShell en tant qu’administrateur.
1. Entrez `Connect-MsolService` pour vous connecter à votre locataire Azure.  
1. Entrez `get-msoldevice -deviceId <deviceId>`.
1. Vérifiez que le paramètre **Enabled** est défini sur **True**.

## <a name="troubleshoot-your-implementation"></a>Résoudre les problèmes liés à votre implémentation

Si vous rencontrez des problèmes durant l’exécution d’une jonction Azure AD Hybride pour des appareils Windows appartenant à un domaine, consultez :

- [Résolution des problèmes de jonction Azure AD Hybride pour les appareils Windows actuels](troubleshoot-hybrid-join-windows-current.md)
- [Résolution des problèmes de jonction Azure AD Hybride pour les appareils Windows de niveau inférieur](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [gérer les identités des appareils à l’aide du portail Azure](device-management-azure-portal.md).
