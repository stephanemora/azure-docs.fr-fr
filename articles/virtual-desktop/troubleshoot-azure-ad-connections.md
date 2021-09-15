---
title: Connexions aux machines virtuelles jointes à Azure AD dans Azure Virtual Desktop - Azure
description: Guide pratique pour résoudre les problèmes de connexion aux machines virtuelles jointes à Azure AD dans Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 08/20/2021
ms.author: helohr
ms.openlocfilehash: f168c05e5df3421126c94bea7160896fb1b75363
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662271"
---
# <a name="connections-to-azure-ad-joined-vms"></a>Connexion aux machines virtuelles jointes à Azure AD

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager.

Utilisez cet article pour résoudre les problèmes de connexion aux machines virtuelles jointes à Azure AD dans Azure Virtual Desktop.

## <a name="provide-feedback"></a>Fournir des commentaires

Rendez-vous sur le site [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum) pour discuter du service Azure Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="all-clients"></a>Tous les clients

### <a name="your-account-is-configured-to-prevent-you-from-using-this-device"></a>Votre compte est configuré pour vous empêcher d’utiliser cet appareil

Si vous rencontrez une erreur indiquant **Votre compte est configuré pour vous empêcher d’utiliser cet appareil. Pour plus d’informations, contactez votre administrateur système**, vérifiez que le [rôle Connexion de l’utilisateur de la machine virtuelle](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#azure-role-not-assigned) a été attribué au compte d’utilisateur sur les machines virtuelles. 

## <a name="windows-desktop-client"></a>Client Windows Desktop

### <a name="the-logon-attempt-failed"></a>Échec de la tentative d’ouverture de session

Si vous rencontrez une erreur indiquant **Échec de la tentative d’ouverture de session** à l’invite des informations d’identification de sécurité Windows, vérifiez les points suivants :

- Vous utilisez un appareil ayant une jonction Azure AD ou une jonction hybride Azure AD avec le même locataire Azure AD que l’hôte de session OR
- Vous utilisez un appareil exécutant Windows 10 2004 ou version ultérieure, inscrit auprès d’Azure AD sur le même locataire Azure AD que l’hôte de session
- Le [protocole PKU2U est activé](/windows/security/threat-protection/security-policy-settings/network-security-allow-pku2u-authentication-requests-to-this-computer-to-use-online-identities) sur le PC local et l’hôte de session
- L’[authentification multifacteur par utilisateur est désactivée](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) pour le compte d’utilisateur, car elle n’est pas prise en charge pour les machines virtuelles jointes à Azure AD.

### <a name="the-sign-in-method-youre-trying-to-use-isnt-allowed"></a>La méthode de connexion que vous essayez d’utiliser n’est pas autorisée

Si vous rencontrez une erreur indiquant **La méthode de connexion que vous essayez d’utiliser n’est pas autorisée. Essayez une autre méthode de connexion ou contactez votre administrateur système**, vous disposez de stratégies d’accès conditionnel qui restreignent l’accès. Suivez les instructions fournies dans [Activer l’authentification multifacteur](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) pour activer l’authentification multifacteur sur les machines virtuelles jointes à Azure AD.

## <a name="web-client"></a>Client web

### <a name="sign-in-failed-please-check-your-username-and-password-and-try-again"></a>Échec de la connexion. Vérifiez votre nom d’utilisateur et votre mot de passe, puis réessayez

Si vous rencontrez une erreur indiquant **Désolé, nous n’avons pas pu nous connecter à NOM. Échec de la connexion. Vérifiez votre nom d’utilisateur et votre mot de passe, puis réessayez** quand vous utilisez le client web, vérifiez que vous avez [activé les connexions à partir d’autres clients](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients).

### <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Nous n’avons pas pu nous connecter à l’ordinateur distant en raison d’une erreur de sécurité

Si vous rencontrez une erreur indiquant **Désolé, nous n’avons pas pu nous connecter à NOM. Nous n’avons pas pu nous connecter au PC distant en raison d’une erreur de sécurité. Si le problème persiste, demandez de l’aide à votre administrateur ou au support technique**, vous disposez de stratégies d’accès conditionnel qui restreignent l’accès. Suivez les instructions fournies dans [Activer l’authentification multifacteur](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms) pour activer l’authentification multifacteur sur les machines virtuelles jointes à Azure AD.

## <a name="android-client"></a>Client Android

### <a name="error-code-2607---we-couldnt-connect-to-the-remote-pc-because-your-credentials-did-not-work"></a>Code d’erreur 2607 - Nous n’avons pas pu nous connecter au PC distant, car vos informations d’identification ne sont pas valides

Si vous rencontrez une erreur indiquant **Nous n’avons pas pu nous connecter au PC distant, car vos informations d’identification ne sont pas valides. La machine distante est jointe à Azure AD** avec le code d’erreur 2607 quand vous utilisez le client Android, vérifiez que vous avez [activé les connexions à partir d’autres clients](deploy-azure-ad-joined-vm.md#connect-using-the-other-clients).

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir une vue d’ensemble de la résolution des problèmes Azure Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes liés à la création d’un environnement Azure Virtual Desktop et d’un pool d’hôtes dans un environnement Azure Virtual Desktop, consultez [Création d’un environnement et d’un pool d’hôtes](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Azure Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes relatifs à l’agent Azure Virtual Desktop ou à la connectivité des sessions, consultez [Résoudre les problèmes courants liés à l’agent Azure Virtual Desktop](troubleshoot-agent.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Azure Virtual Desktop, consultez [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
