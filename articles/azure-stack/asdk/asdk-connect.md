---
title: Se connecter à Azure Stack | Microsoft Docs
description: Découvrez comment vous connecter à ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026817"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Se connecter au Kit de développement Azure Stack

Pour gérer des ressources, vous devez d’abord vous connecter au Kit de développement Azure Stack (ADSK). Dans cet article, nous décrivons les étapes à suivre pour vous connecter à ce Kit de développement. Vous pouvez choisir l’une de ces deux options de connexion :

* [Connexion Bureau à distance](#connect-with-remote-desktop). Lorsque vous vous connectez à l’aide de la connexion Bureau à distance, un seul utilisateur peut se connecter rapidement au kit de développement.
* [Réseau privé virtuel (VPN)](#connect-with-vpn). Lorsque vous vous connectez à l’aide d’un VPN, plusieurs utilisateurs peuvent se connecter simultanément à partir de clients en dehors de l’infrastructure Azure Stack. Une connexion VPN nécessite une configuration.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Se connecter à Azure Stack à l’aide de la connexion Bureau à distance

Un seul utilisateur simultané peut gérer les ressources depuis le portail de l’opérateur ou le portail de l’utilisateur via une connexion Bureau à distance.

1. Ouvrez une connexion Bureau à distance (mstc.exe) et connectez-vous à l’ordinateur hôte du kit de développement en tant que **AzureStack\AzureStackAdmin** à l’aide du mot de passe spécifié pendant l’installation d’ASDK.  

2. Sur l’ordinateur hôte du kit de développement, ouvrez le Gestionnaire de serveur (ServerManager.exe). Sélectionnez **Serveur local**, désactiver **Configuration de sécurité renforcée d’Internet Explorer**, puis fermez le Gestionnaire de serveur.

3. Connectez-vous au portail d’administration en tant que **AzureStack\CloudAdmin** ou utilisez d’autres informations d’identification de l’opérateur d’Azure Stack. L’adresse du portail d’administration d’ASDK est [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Connectez-vous au portail utilisateur en tant que **AzureStack\CloudAdmin** ou utilisez d’autres informations d’identification de l’utilisateur d’Azure Stack. L’adresse du portail utilisateur d’ASDK est [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Pour savoir quand utiliser quel compte, consultez [Informations de base sur l’administration de l’ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Se connecter à Azure Stack en utilisant l’option VPN

Vous pouvez établir une connexion VPN par tunnel fractionné à un ASDK pour accéder aux portails Azure Stack et aux outils installés localement tels que Visual Studio et PowerShell. Grâce aux connexions VPN, plusieurs utilisateurs peuvent se connecter en même temps aux ressources Azure Stack hébergées par l’ASDK.

La connectivité VPN est prise en charge dans les déploiements Azure AD et Active Directory Federation Services (AD FS).

> [!NOTE]
> Une connexion VPN ne fournit pas de connectivité aux machines virtuelles de l’infrastructure Azure Stack.

### <a name="prerequisites"></a>Prérequis
Avant de configurer une connexion VPN à l’ASDK, vérifiez que les conditions préalables suivantes sont réunies.

- Installez [Azure PowerShell pour Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) sur votre ordinateur local.  
- Téléchargez les [outils nécessaires pour utiliser Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Configurer une connectivité VPN

Pour créer une connexion VPN à l’ASDK, ouvrez Windows PowerShell en tant qu’administrateur sur votre ordinateur local Windows. Ensuite, exécutez le script suivant (mise à jour des valeurs de l’adresse IP et du mot de passe pour votre environnement) :

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la configuration réussit, **azurestack** apparaît dans votre liste des connexions VPN.

![Connexions réseau](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

Connectez-vous à l’instance Azure Stack à l’aide d’une des méthodes suivantes :  

* Utiliser la commande `Connect-AzsVpn ` :
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Quand vous y êtes invité, approuvez l’hôte Azure Stack et installez le certificat fourni par **AzureStackCertificateAuthority** dans le magasin de certificats de votre ordinateur local. 
  
  > [!IMPORTANT]
  > L’invite de commandes peut être masquée par la fenêtre PowerShell.

* Sur votre ordinateur local, ouvrez **Paramètres réseau** > **VPN** > **azurestack** > **Se connecter**. À l’invite de connexion, entrez le nom d’utilisateur (**AzureStack\AzureStackAdmin**) et votre mot de passe.

### <a name="test-vpn-connectivity"></a>Connectivité VPN de test

Pour tester la connexion au portail, ouvrez un navigateur web, puis accédez au portail utilisateur (https://portal.local.azurestack.external/)) ou au portail d’administration (https://adminportal.local.azurestack.external/)). Connectez-vous et créez un groupe de ressources.  

## <a name="next-steps"></a>Étapes suivantes

[Dépannage](asdk-troubleshooting.md)
