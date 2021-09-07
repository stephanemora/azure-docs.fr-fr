---
title: Installer l’agent de provisionnement cloud Azure AD Connect à l’aide d’une interface de ligne de commande (CLI) et de PowerShell
description: Découvrez comment installer l’agent de provisionnement cloud Azure AD Connect à l’aide des applets de commande PowerShell.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c150f4bf18f9c4f5b9215122a45df1de32f48c32
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506238"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-by-using-a-cli-and-powershell"></a>Installer l’agent de provisionnement Azure AD Connect à l’aide de l’interface CLI et de PowerShell
Cet article vous montre comment installer l’agent de provisionnement Azure AD (Azure Active Directory) Connect à l’aide des applets de commande PowerShell.
 
>[!NOTE]
>Cet article traite de l’installation de l’agent de provisionnement à l’aide de l’interface de ligne de commande (CLI). Pour plus d’informations sur l’installation de l’agent de provisionnement Azure AD Connect à l’aide de l’Assistant, consultez [Installer l’agent de provisionnement Azure AD Connect](how-to-install.md).

## <a name="prerequisite"></a>Prérequis

Le protocole TLS 1.2 doit être activé sur le serveur Windows pour que vous puissiez installer l’agent de provisionnement Azure AD Connect à l’aide des applets de commande PowerShell. Pour activer le protocole TLS 1.2, suivez les étapes décrites dans [Prérequis pour la synchronisation cloud Azure AD Connect](how-to-prerequisites.md#tls-requirements).

>[!IMPORTANT]
>Les instructions d’installation suivantes supposent que tous les [prérequis](how-to-prerequisites.md) ont été respectés.

## <a name="install-the-azure-ad-connect-provisioning-agent-by-using-powershell-cmdlets"></a>Installer l’agent de provisionnement Azure AD Connect à l’aide des applets de commande PowerShell 

 1. Connectez-vous au portail Azure, puis accédez à **Azure Active Directory**.
 1. Dans le menu de gauche, sélectionnez **Azure AD Connect**.
 1. Sélectionnez **Gérer le provisionnement (préversion)**  > **Passer en revue tous les agents**.
 1. Téléchargez l’agent de provisionnement Azure AD Connect à partir du portail Azure.

    ![Capture d’écran montrant le téléchargement de l’agent local.](media/how-to-install/install-9.png)</br>

 1. Dans le cadre des instructions, l’agent a été téléchargé dans le dossier C:\ProvisioningSetup. 
 1. Installez ProvisioningAgent en mode silencieux.

       ```
       $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
       $installerProcess.WaitForExit()  
       ```
 1. Importez le module PS de l’agent de provisionnement.

       ```
       Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
       ```
 1. Connectez-vous à Azure AD à l’aide des informations d’identification d’un administrateur général. Vous pouvez personnaliser cette section pour récupérer un mot de passe à partir d’un magasin sécurisé. 

       ```
       $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 
    
       $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
       
       Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 
       ```
 1. Ajoutez le compte gMSA, puis indiquez les informations d’identification de l’administrateur de domaine pour créer le compte gMSA par défaut.
 
       ```
       $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 
    
       Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
       ```
 1. Vous pouvez également utiliser l’applet de commande précédente pour spécifier un compte gMSA précréé.
 
       ```
       Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
       ```
 1. Ajoutez le domaine.

       ```
       $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
       ```
 1. Vous pouvez également utiliser l’applet de commande précédente pour configurer les contrôleurs de domaine préférés.

       ```
       $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
       ```
 1. Répétez l’étape précédente pour ajouter d’autres domaines. Indiquez les noms de compte et les noms de domaine des domaines respectifs.
 
 1. Redémarrez le service.
 
       ```
       Restart-Service -Name AADConnectProvisioningAgent  
       ```
 1. Accédez au portail Azure pour créer la configuration de la synchronisation cloud.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Applets de commande PowerShell gMSA de l’agent de provisionnement
Une fois que vous avez installé l’agent, vous pouvez appliquer des autorisations plus précises au gMSA. Pour plus d’informations et pour obtenir des instructions pas à pas sur la configuration des autorisations, consultez les [applets de commande PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect](how-to-gmsa-cmdlets.md).

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect](how-to-gmsa-cmdlets.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)