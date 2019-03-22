---
title: Azure AD mot de passe libre-service de réinitialisation Windows 7 et 8.1 - Azure Active Directory
description: Procédure d’activation de la réinitialisation de mot de passe en libre-service à l’aide du lien « Vous avez oublié votre mot de passe ? » sur l’écran d’ouverture de session de Windows 7 ou 8.1
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a7752fac54f9dfb2f8fb0aecd3b6249c52c3bcf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316352"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Activation Activer la réinitialisation de mot de passe à partir de Windows 7, 8 et 8.1

En tant qu’administrateur, vous avez activé la réinitialisation de mot de passe en libre-service (SSPR), mais les utilisateurs continuent d’appeler le support technique pour réinitialiser leur mot de passe, car ils ne parviennent pas à obtenir une fenêtre de navigateur pour accéder au [portail SSPR](https://aka.ms/sspr). Pour les machines Windows 10, vous pouvez activer le lien « Réinitialiser le mot de passe » sur l’écran d’ouverture de session à l’aide du didacticiel [Réinitialisation du mot de passe Azure AD depuis l’écran de connexion](tutorial-sspr-windows.md). Les instructions ci-après vous indiquent comment permettre aux utilisateurs Windows 7, 8 et 8.1 de réinitialiser leur mot de passe à l’aide de la fonctionnalité SSPR au niveau de l’écran d’ouverture de session Windows.

Contrairement aux machines Windows 10, les machines Windows 7, 8 et 8.1 ne présentent aucune exigence de jonction à un domaine Azure AD ou Active Directory pour la réinitialisation de mot de passe.

![Exemple d’écran d’ouverture de session Windows 7 présentant le lien « Vous avez oublié votre mot de passe ? »](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Conditions préalables

* activer la réinitialisation du mot de passe libre-service Azure AD.
* Système d’exploitation Windows 7 ou Windows 8.1 corrigé.
* TLS 1.2 activé à l’aide des instructions fournies dans l’article [Paramètres de Registre TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).

> [!WARNING]
> TLS 1.2 doit être activé, et non simplement défini sur négociation automatique.

## <a name="install"></a>Installer

1. Téléchargez le programme d’installation correspondant à la version de Windows que vous souhaitez activer.

   1. Le logiciel est disponible dans le Centre de téléchargement Microsoft à l’adresse [https://aka.ms/sspraddin](https://aka.ms/sspraddin).

1. Connectez-vous à la machine sur laquelle vous souhaitez installer et exécuter le programme d’installation.
1. Après l’installation, un redémarrage est vivement recommandé.
1. Une fois le redémarrage effectué, sur l’écran d’ouverture de session, choisissez un utilisateur, puis cliquez sur « Vous avez oublié votre mot de passe ? » pour lancer le flux de travail de réinitialisation de mot de passe.
1. Exécutez le flux de travail en suivant les étapes à l’écran pour réinitialiser votre mot de passe.

![Exemple Windows 7 de flux de réinitialisation de mot de passe en libre-service après un clic sur le lien « Vous avez oublié votre mot de passe ? »](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Installation sans assistance

* Pour une installation sans assistance, utilisez la commande « msiexec /i SsprWindowsLogon.PROD.msi /qn »
* Pour une désinstallation sans assistance, utilisez la commande « msiexec /x SsprWindowsLogon.PROD.msi /qn »

## <a name="caveats"></a>Mises en garde

Vous devez vous inscrire pour SSPR avant de pouvoir utiliser le lien « Vous avez oublié votre mot de passe ? ».

![Informations de sécurité supplémentaires requises pour la réinitialisation de votre mot de passe](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

L’utilisation de l’application Microsoft Authenticator pour les notifications et les codes afin de réinitialiser votre mot de passe ne fonctionne pas dans cette version initiale. Les utilisateurs doivent disposer d’autres méthodes inscrites qui répondent aux exigences de votre stratégie.

## <a name="troubleshooting"></a>Résolution de problèmes

Les événements sont enregistrés à la fois sur la machine et dans Azure AD.

Azure AD Events inclut des informations sur l’adresse IP et le ClientType associés à la réinitialisation de mot de passe.

![Exemple Windows 7 de réinitialisation de mot de passe sur l’écran d’ouverture de session dans le journal d’audit Azure AD](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Si une journalisation supplémentaire est requise, vous pouvez modifier une clé de Registre sur la machine pour activer la journalisation détaillée. N’activez la journalisation détaillée qu’à des fins de résolution des problèmes.

```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

* Pour activer la journalisation détaillée, créez une entrée REG_DWORD : « EnableLogging », puis définissez-la sur 1.
* Pour désactiver la journalisation détaillée, redéfinissez l’entrée REG_DWORD « EnableLogging » sur 0.

Si vos ordinateurs Windows 7, 8 et 8.1 se trouvent derrière un serveur proxy ou pare-feu, le trafic HTTPS (443) vers passwordreset.microsoftonline.com doit être autorisé.

## <a name="next-steps"></a>Étapes suivantes

[Autoriser les utilisateurs Windows 10 à réinitialiser leur mot de passe sur l’écran d’ouverture de session](tutorial-sspr-windows.md)
