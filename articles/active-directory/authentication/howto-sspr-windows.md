---
title: Réinitialisation de mot de passe en libre-service pour Windows – Azure Active Directory
description: Procédure d’activation de la réinitialisation de mot de passe en libre-service à l’aide du lien « Vous avez oublié votre mot de passe ? » sur l’écran de connexion de Windows
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e25efcb068fe51f05dbbde50e8a96da492a735
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381232"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Activation Activer la réinitialisation de mot de passe à partir de l’écran de connexion Windows

Pour les machines exécutant Windows 7, 8, 8.1 et 10, vous pouvez autoriser les utilisateurs à réinitialiser leur mot de passe sur l’écran de connexion Windows. Les utilisateurs n’ont plus besoin de trouver d’appareil avec un navigateur Web pour accéder au [portail de réinitialisation de mot de passe en libre-service](https://aka.ms/sspr).

![Exemples d’écrans de connexion Windows 7 et 10 avec le lien vers la réinitialisation de mot de passe en libre-service affiché](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Limitations générales

- Actuellement, la réinitialisation du mot de passe n’est pas prise en charge à partir d’un bureau à distance ou des sessions améliorées Hyper-V.
- Cette fonctionnalité ne fonctionne pas pour les réseaux avec l’authentification de réseau 802.1x déployée et l’option « Immédiatement avant l’ouverture de session de l’utilisateur ». Pour les réseaux avec l’authentification de réseau 802.1x déployée, il est recommandé d’utiliser l’authentification de la machine pour activer cette fonctionnalité.
- Les machines jointes Azure AD Hybride doivent disposer d’une connectivité réseau à un contrôleur de domaine pour utiliser le nouveau mot de passe et mettre à jour les informations d’identification mises en cache.
- Si vous utilisez une image, avant d’exécuter sysprep, vérifiez que le cache web est effacé pour le compte Administrateur intégré avant d’effectuer l’étape CopyProfile. Vous trouverez plus d’informations au sujet de cette étape dans l’article de support [Performances médiocres lors de l’utilisation du profil d’utilisateur par défaut personnalisé](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Les paramètres suivants sont connus pour leur interférence avec la fonctionnalité de réinitialisation des mots de passe sur les appareils Windows 10
    - Si Ctrl+Alt+Suppr est exigé par la stratégie dans les versions de Windows 10 avant v1809, **Réinitialiser le mot de passe** ne fonctionnera pas.
    - Si les notifications sur l’écran de verrouillage sont désactivées, **Réinitialiser le mot de passe** ne fonctionnera pas.
    - HideFastUserSwitching est défini sur activé ou 1
    - DontDisplayLastUserName est défini sur activé ou 1
    - NoLockScreen est défini sur activé ou 1
    - EnableLostMode est défini sur l’appareil
    - Explorer.exe est remplacé par un interpréteur de commandes personnalisé
- La combinaison des trois paramètres spécifiques suivants peut empêcher le fonctionnement de cette fonctionnalité.
    - Ouverture de session interactive : N’exige pas la combinaison de touches CTRL + ALT + SUPPR = Désactivé
    - DisableLockScreenAppNotifications = 1 ou Activé
    - IsContentDeliveryPolicyEnforced = 1 ou True

## <a name="windows-10-password-reset"></a>Réinitialisation de mot de passe de Windows 10

### <a name="windows-10-prerequisites"></a>Prérequis pour Windows 10

- Un administrateur doit activer la réinitialisation de mot de passe en libre-service Azure AD à partir du portail Azure.
- **Les utilisateurs doivent s’inscrire à la réinitialisation de mot de passe en libre-service avant d’utiliser cette fonctionnalité**
- Conditions requises du proxy réseau
   - Appareils Windows 10 
       - Port 443 vers `passwordreset.microsoftonline.com` et `ajax.aspnetcdn.com`
       - Les appareils Windows 10 prennent uniquement en charge la configuration du proxy au niveau de la machine
- Exécutez au moins Windows 10, avec la mise à jour d’avril 2018 (v1803), et les appareils doivent être :
    - Appareil joints Azure AD
    - Appareils joints Azure AD hybrides

### <a name="enable-for-windows-10-using-intune"></a>Activez pour Windows 10 à l’aide de Intune

Déployer la modification de la configuration est la méthode la plus souple pour activer la réinitialisation du mot de passe à partir de l’écran de connexion à l’aide d’Intune. Intune vous permet de déployer les modifications de configuration vers un groupe spécifique de machines que vous définissez. Cette méthode nécessite un appareil inscrit à Intune.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Créer une stratégie de configuration d’appareil dans Intune

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **Intune**.
1. Créez un profil de configuration d’appareil en cliquant sur **Configuration de l’appareil** > **Profils** > **Créer un profil**
   - Attribuez un nom explicite au profil
   - Indiquez éventuellement une description explicite du profil
   - Plateforme **Windows 10 ou plus**
   - Type de profil **personnalisé**
1. Configurez les **paramètres**
   - **Ajoutez** le paramètre OMA-URI suivant pour activer le lien de réinitialisation du mot de passe
      - Entrez un nom explicite pour expliquer l’action du paramètre
      - Indiquez éventuellement une description explicite du paramètre
      - Paramètre **OMA-URI** défini sur `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - Paramètre **Type de données** défini sur la valeur **Entier**
      - Paramètre **Valeur** défini sur **1**
      - Cliquez sur **OK**
   - Cliquez sur **OK**
1. Cliquez sur **Créer**
1. Cette stratégie peut être attribuée à des utilisateurs, des appareils ou des groupes spécifiques. Vous trouverez plus d’informations dans l’article [Attribuer des profils d’utilisateur et d’appareil dans Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Activez pour Windows 10 à l’aide du Registre

1. Se connecter à un PC Windows à l’aide d’informations d’identification d’administration
1. Exécutez **regedit** en tant qu’administrateur
1. Définissez la clé de Registre suivante
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Résolution des problèmes de réinitialisation de mot de passe de Windows 10

Le journal d’audit Azure AD inclut des informations sur l’adresse IP et le ClientType associés à la réinitialisation de mot de passe.

![Exemple de réinitialisation de mot de passe sur Windows 7 dans le journal d’audit Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Quand des utilisateurs réinitialisent leur mot de passe à partir de l’écran de connexion d’un appareil Windows 10, un compte temporaire à faibles privilèges appelé `defaultuser1` est créé. Ce compte sert à sécuriser le processus de réinitialisation du mot de passe. Le compte lui-même a un mot de passe généré de manière aléatoire, ne s’affiche pas pour la connexion à l’appareil, et est supprimé automatiquement une fois que l’utilisateur a réinitialisé son mot de passe. Il peut exister plusieurs profils `defaultuser`, mais vous pouvez les ignorer sans risque.

## <a name="windows-7-8-and-81-password-reset"></a>Réinitialisation de mot de passe de Windows 7, 8 et 8.1

### <a name="windows-7-8-and-81-prerequisites"></a>Prérequis pour Windows 7, 8 et 8.1

- Un administrateur doit activer la réinitialisation de mot de passe en libre-service Azure AD à partir du portail Azure.
- **Les utilisateurs doivent s’inscrire à la réinitialisation de mot de passe en libre-service avant d’utiliser cette fonctionnalité**
- Conditions requises du proxy réseau
   - Appareils Windows 7, 8 et 8.1
       - Port 443 vers `passwordreset.microsoftonline.com`
- Système d’exploitation Windows 7 ou Windows 8.1 corrigé.
- TLS 1.2 activé à l’aide des instructions fournies dans l’article [Paramètres de Registre TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Si plusieurs fournisseurs tiers d’informations d’identification sont activés sur votre machine, les utilisateurs verront plusieurs profils utilisateur sur l’écran de connexion.

> [!WARNING]
> TLS 1.2 doit être activé, et non simplement défini sur négociation automatique

### <a name="install"></a>Installer

1. Téléchargez le programme d’installation correspondant à la version de Windows que vous souhaitez activer.
   - Le logiciel est disponible dans le Centre de téléchargement Microsoft à l’adresse [https://aka.ms/sspraddin](https://aka.ms/sspraddin).
1. Connectez-vous à la machine sur laquelle vous souhaitez installer et exécuter le programme d’installation.
1. Après l’installation, un redémarrage est vivement recommandé.
1. Une fois le redémarrage effectué, sur l’écran de connexion, choisissez un utilisateur, puis cliquez sur « Vous avez oublié votre mot de passe ? » pour lancer le flux de travail de réinitialisation de mot de passe.
1. Exécutez le flux de travail en suivant les étapes à l’écran pour réinitialiser votre mot de passe.

![Exemple Windows 7 de flux de réinitialisation de mot de passe en libre-service Flux de réinitialisation de mot de passe en libre-service](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installation sans assistance

- Pour une installation sans assistance, utilisez la commande « msiexec /i SsprWindowsLogon.PROD.msi /qn »
- Pour une désinstallation sans assistance, utilisez la commande « msiexec /x SsprWindowsLogon.PROD.msi /qn »

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Résolution des problèmes de réinitialisation de mot de passe de Windows 7, 8 et 8.1

Les événements sont enregistrés à la fois sur la machine et dans Azure AD. Azure AD Events inclut des informations sur l’adresse IP et le ClientType associés à la réinitialisation de mot de passe.

![Exemple de réinitialisation de mot de passe sur Windows 7 dans le journal d’audit Azure AD](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Si une journalisation supplémentaire est requise, vous pouvez modifier une clé de Registre sur la machine pour activer la journalisation détaillée. N’activez la journalisation détaillée qu’à des fins de résolution des problèmes.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Pour activer la journalisation détaillée, créez un `REG_DWORD: “EnableLogging”`, et affectez-lui la valeur 1.
- Pour désactiver la journalisation détaillée, faites basculer la valeur de `REG_DWORD: “EnableLogging”` à 0.

## <a name="what-do-users-see"></a>Ce que voient les utilisateurs

Maintenant que vous avez configuré la réinitialisation de mot de passe pour vos appareils Windows, qu’est-ce qui change pour l’utilisateur ? Comment peut-il savoir qu’il est possible de réinitialiser un mot de passe sur l’écran de connexion ?

![Exemples d’écrans de connexion Windows 7 et 10 avec le lien vers la réinitialisation de mot de passe en libre-service affiché](./media/howto-sspr-windows/windows-reset-password.png)

Quand des utilisateurs tentent de se connecter, ils voient maintenant un lien de **Réinitialisation du mot de passe** ou de **Mot de passe oublié** qui ouvre l’expérience de réinitialisation de mot de passe libre-service au niveau de l’écran de connexion. Cette fonctionnalité permet aux utilisateurs de réinitialiser leur mot de passe sans avoir à utiliser un autre appareil pour accéder à un navigateur web.

Pour en savoir plus sur l’utilisation de cette fonction, les utilisateurs peuvent consulter la section [Réinitialiser ou déverrouiller mon mot de passe d’un compte professionnel ou scolaire](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Étapes suivantes

[Planifier les méthodes d’authentification pour permettre](concept-authentication-methods.md)

[Configurer Windows 10](https://docs.microsoft.com/windows/configuration/)
