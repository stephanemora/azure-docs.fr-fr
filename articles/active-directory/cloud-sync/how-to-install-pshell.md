---
title: Installer l’agent de provisionnement cloud Azure AD Connect à l’aide d’une interface de ligne de commande (CLI) et de PowerShell
description: Découvrez comment installer l’agent de provisionnement cloud Azure AD Connect à l’aide des applets de commande PowerShell.
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
ms.openlocfilehash: d5f560321b13e4c7a47aed262a1d312093e9be56
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758822"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-a-command-line-interface-cli-and-powershell"></a>Installer l’agent de provisionnement Azure AD Connect à l’aide d’une interface de ligne de commande (CLI) et de PowerShell
Le document suivant vous montrera comment installer l’agent de provisionnement Azure AD Connect à l’aide des applets de commande PowerShell.
 
>[!NOTE]
>Ce document explique l’installation de l’agent de provisionnement à l’aide de l’interface de ligne de commande (CLI).  Pour obtenir des informations sur l’installation de l’agent de provisionnement Azure AD Connect à l’aide de l’Assistant, consultez [Installer l’agent de provisionnement Azure AD Connect](how-to-install.md).

## <a name="prerequisite"></a>Condition préalable : 


>[!IMPORTANT]
>Les instructions d’installation suivantes supposent que tous les [prérequis](how-to-prerequisites.md) ont été réunis.
>
> Le protocole TLS 1.2 doit être activé sur le serveur Windows avant d’installer l’agent de provisionnement Azure AD Connect à l’aide des applets de commande PowerShell. Pour activer TLS 1.2, vous pouvez utiliser les étapes présentées [ici](how-to-prerequisites.md#tls-requirements).

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Installer l’agent de provisionnement Azure AD Connect à l’aide des applets de commande PowerShell 


 1. Connectez-vous au portail Azure, puis accédez à **Azure Active Directory**.
 2. Dans le menu de gauche, sélectionnez **Azure AD Connect**.
 3. Sélectionnez **Gérer le provisionnement (préversion)**  > **Passer en revue tous les agents**.
 4. Téléchargez l’agent de provisionnement Azure AD Connect à partir du portail Azure vers un emplacement local.  

   ![Télécharger un agent local](media/how-to-install/install-9.png)</br>
 5. Dans le cadre de ces instructions, l’agent a été téléchargé dans le dossier suivant :   Dossier « C:\ProvisioningSetup ». 
 6. Installez ProvisioningAgent en mode silencieux.

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Importez le module PS de l’agent de provisionnement. 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
   ```
 8. Connectez-vous à AzureAD à l’aide des informations d’identification d’administrateur général. Vous pouvez personnaliser cette section pour extraire le mot de passe d’un stockage sécurisé. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 

 9. Ajoutez le compte gMSA, fournissez les informations d’identification de l’administrateur de domaine pour créer le compte gMSA par défaut. 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Ou utilisez l’applet de commande ci-dessus comme indiqué ci-après pour spécifier un compte gMSA déjà créé. 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Ajoutez un domaine. 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Ou utilisez l’applet de commande ci-dessus comme indiqué ci-après pour configurer des contrôleurs de domaine préférés. 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Répétez l’étape précédente pour ajouter d’autres domaines, indiquez les noms de compte et les noms des domaines respectifs. 
 14. Redémarrez le service. 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Accédez au portail Azure pour créer la configuration de la synchronisation cloud.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Applets de commande PowerShell gMSA de l’agent de provisionnement
Maintenant que vous avez installé l’agent, vous pouvez appliquer des autorisations plus granulaires à gMSA.  Consultez [Cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect](how-to-gmsa-cmdlets.md) pour obtenir des informations et des instructions détaillées sur la configuration des autorisations.

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Cmdlets PowerShell gMSA de l’agent de provisionnement cloud Azure AD Connect](how-to-gmsa-cmdlets.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)