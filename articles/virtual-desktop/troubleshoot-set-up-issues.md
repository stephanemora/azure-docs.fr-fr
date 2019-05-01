---
title: Bureau virtuel Windows client et l’hôte de la création du pool - Azure
description: Comment dépanner et résoudre le client et l’hôte le pool de problèmes lors de l’installation d’un environnement de client de bureau virtuel Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 9f52d1b949310792ffedc6e4723fbfb423fb5dfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928496"
---
# <a name="tenant-and-host-pool-creation"></a>Création du pool de client et de l’hôte

Cet article traite des problèmes lors de l’installation initiale du client Bureau virtuel Windows et l’infrastructure de pool de hôte de session associée.

## <a name="provide-feedback"></a>Fournir des commentaires

Nous n’acceptons actuellement aucun cas de support pendant que Windows Virtual Desktop est en préversion. Rendez-vous sur le site [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) pour discuter du service Windows Virtual Desktop avec l’équipe de produit et les membres actifs de la communauté.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>L’acquisition de l’image de session multiples de Windows 10 entreprise

Pour utiliser l’image de session multiples de Windows 10 entreprise, accédez à la place de marché Azure, sélectionnez **prise en main** > **Microsoft Windows 10** > et [Windows 10 entreprise pour Aperçu de bureaux virtuels, Version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Une capture d’écran de sélection de Windows 10 entreprise pour l’aperçu de bureaux virtuels, Version 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Création d’un bureau virtuel Windows client

Cette section couvre les problèmes potentiels lors de la création du client de bureau virtuel Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error: L’utilisateur n’est pas autorisé à interroger le service de gestion

![Fenêtre de capture d’écran de PowerShell dans lequel un utilisateur n’est pas autorisé à interroger le service de gestion.](media/UserNotAuthorizedNewTenant.png)

Exemple d’erreur brut :

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Cause :** L’utilisateur connecté n’a pas été attribué le rôle de TenantCreator dans leur Azure Active Directory.

**Correctif :** Suivez les instructions de [attribuer le rôle d’application TenantCreator à un utilisateur dans votre client Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Après avoir suivi les instructions, vous disposerez d’un utilisateur affecté au rôle TenantCreator.

![Rôle de la capture d’écran de TenantCreator attribué.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Création de machines virtuelles de hôte de session de bureau virtuel Windows

Hôte de session des machines virtuelles peut être créée de plusieurs façons, mais les équipes de bureau virtuel de la Services/Windows Bureau à distance prennent uniquement en charge l’approvisionnement des problèmes liés au modèle Azure Resource Manager de machine virtuelle. Le modèle Azure Resource Manager est disponible dans [place de marché Azure](https://azuremarketplace.microsoft.com/) et [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problèmes à l’aide de bureau virtuel Windows – approvisionner une offre de la place de marché Azure de pool hôte

Le bureau virtuel Windows – configurer un modèle de pool d’hôte est disponible à partir de la place de marché Azure.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Error: Lorsque vous utilisez le lien à partir de GitHub, le message « créer un compte gratuit » s’affiche

![Capture d’écran pour créer un compte gratuit.](media/be615904ace9832754f0669de28abd94.png)

**Cause 1 :** Il ne sont pas des abonnements actifs dans le compte utilisé pour se connecter à Azure ou le compte utilisé ne dispose des autorisations pour afficher les abonnements.

**Correctif 1 :** Connectez-vous avec un compte disposant d’un accès collaborateur (au minimum) à l’abonnement dans lequel les machines virtuelles hôtes de session sont destinés à être déployés.

**Cause 2 :** L’abonnement utilisé fait partie d’un locataire du fournisseur de services Cloud (CSP) Microsoft.

**Correctif 2 :** Accédez à l’emplacement GitHub pour **créer et approvisionner nouveau bureau virtuel Windows hôte pool** et suivez ces instructions :

1. Avec le bouton droit sur **déployer sur Azure** et sélectionnez **copier l’adresse lien**.
2. Ouvrez **le bloc-notes** et collez le lien.
3. Avant le caractère #, insérez le nom du locataire client CSP fin.
4. Ouvrez le nouveau lien dans un navigateur et le portail Azure charge le modèle.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modèle Azure Resource Manager et les erreurs de PowerShell Desired State Configuration (DSC)

Suivez ces instructions pour résoudre les problèmes des déploiements ayant échoués de modèles Azure Resource Manager et PowerShell DSC.

1. Passez en revue les erreurs dans le déploiement à l’aide [afficher les opérations de déploiement avec Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. S’il n’y a aucune erreur dans le déploiement, passez en revue les erreurs dans le journal d’activité à l’aide [afficher les journaux d’activité pour auditer les actions sur les ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Une fois que l’erreur est identifiée, utilisez le message d’erreur et les ressources dans [résoudre les erreurs courantes de déploiement Azure avec Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) pour résoudre le problème.
4. Supprimer toutes les ressources créées pendant le déploiement précédent et redéployer le modèle de nouvelle tentative.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Error: Échec de votre déploiement...<hostname>/joindomain

![Votre capture d’écran de l’échec du déploiement.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemple d’erreur brut :

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Cause 1 :** Informations d’identification fournies pour joindre des machines virtuelles au domaine sont incorrectes.

**Correctif 1 :** Consultez l’erreur « Informations d’identification incorrectes » pour les machines virtuelles ne sont pas joints au domaine dans [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).

**Cause 2 :** Nom de domaine ne se résout pas.

**Correctif 2 :** Consultez l’erreur « nom de domaine ne se résout pas » pour les machines virtuelles ne sont pas joints au domaine dans [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Error: VMExtensionProvisioningError

![Échec de la capture d’écran de votre déploiement a échoué avec l’état d’approvisionnement terminal.](media/7aaf15615309c18a984673be73ac969a.png)

**Cause 1 :** Erreur temporaire avec l’environnement de bureau virtuel Windows.

**Cause 2 :** Erreur temporaire avec la connexion.

**Correctif :** Confirmer l’environnement de bureau virtuel Windows fonctionne correctement en vous connectant à l’aide de PowerShell. Terminer l’inscription de la machine virtuelle manuellement dans [créer un pool de l’hôte avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Error: Le nom d’utilisateur administrateur spécifié n’est pas autorisé.

![Échec de la capture d’écran de votre déploiement dans lequel un administrateur spécifié n’est pas autorisé.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemple d’erreur brut :

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Cause :** Mot de passe fourni contient les sous-chaînes interdits (admin, administrateur, racine).

**Correctif :** Mettre à jour le nom d’utilisateur ou utiliser les différents utilisateurs.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Error: Machine virtuelle a signalé un échec lors de l’extension pour le traitement

![Capture d’écran de l’opération de ressource s’est terminée avec l’état d’approvisionnement terminal dans votre déploiement a échoué.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exemple d’erreur brut :

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Cause :** Extension de PowerShell DSC n’a pas pu obtenir un accès administrateur sur la machine virtuelle.

**Correctif :** Vérifiez le nom d’utilisateur et mot de passe ont un accès administrateur sur la machine virtuelle, puis réexécutez le modèle Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Error: DeploymentFailed – Configuration de DSC PowerShell 'FirstSessionHost' s’est terminée avec l’erreur (s)

![Échec de la capture d’écran de déploiement avec la Configuration DSC PowerShell 'FirstSessionHost' s’est terminée avec l’erreur (s).](media/64870370bcbe1286906f34cf0a8646ab.png)

Exemple d’erreur brut :

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Cause :** Extension de PowerShell DSC n’a pas pu obtenir un accès administrateur sur la machine virtuelle.

**Correctif :** Vérifiez le nom d’utilisateur et mot de passe fourni ont un accès administrateur sur la machine virtuelle, puis réexécutez le modèle Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error: DeploymentFailed – InvalidResourceReference

Exemple d’erreur brut :

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Cause :** Partie du nom du groupe de ressources est utilisé pour certaines ressources créées par le modèle. En raison du nom correspondant à des ressources existantes, le modèle peut sélectionner une ressource existante à partir d’un autre groupe.

**Correctif :** Lorsque vous exécutez le modèle Azure Resource Manager pour déployer des machines virtuelles hôtes de session, vérifiez les deux premiers caractères unique pour votre nom de groupe de ressources abonnement.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Error: DeploymentFailed – InvalidResourceReference

Exemple d’erreur brut :

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Cause :** Cette erreur est, car la carte réseau créée avec le modèle Azure Resource Manager a déjà le même nom qu’une autre carte réseau dans le réseau virtuel.

**Correctif :** Utiliser un préfixe d’hôte différent.

### <a name="error-deploymentfailed--error-downloading"></a>Error: DeploymentFailed : erreur lors du téléchargement

Exemple d’erreur brut :

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Cause :** Cette erreur est en raison d’un itinéraire statique, une règle de pare-feu ou un groupe de sécurité réseau bloque le téléchargement du fichier zip lié au modèle Azure Resource Manager.

**Correctif :** Supprimer le blocage d’itinéraire statique, de règle de pare-feu ou de groupe de sécurité réseau. Si vous le souhaitez, ouvrez le fichier json de modèle Azure Resource Manager dans un éditeur de texte, prendre le lien vers le fichier zip et télécharger la ressource à un emplacement autorisé.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Error: L’utilisateur n’est pas autorisé à interroger le service de gestion

Exemple d’erreur brut :

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Cause :** L’administrateur de client de bureau virtuel Windows spécifié n’a pas une attribution de rôle valide.

**Correctif :** L’utilisateur qui a créé le client de bureau virtuel Windows doit se connecter à Windows Virtual Desktop PowerShell et affecter l’utilisateur tentée une attribution de rôle. Si vous utilisez les paramètres du modèle GitHub Azure Resource Manager, suivez ces instructions à l’aide des commandes PowerShell :

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
Set-RdsContext -Name <Windows Virtual Desktop tenant group name>
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Error: Utilisateur nécessite Azure multi-Factor Authentication (MFA)

![Capture d’écran de votre déploiement a échoué en raison d’un manque de multi-Factor Authentication (MFA)](media/MFARequiredError.png)

Exemple d’erreur brut :

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Cause :** L’administrateur de client de bureau virtuel Windows spécifié requiert Azure multi-Factor Authentication (MFA) pour vous connecter.

**Correctif :** Créer un principal de service et lui attribuer un rôle pour votre client de bureau virtuel Windows en suivant les étapes décrites dans [didacticiel : Créer des principaux de service et les attributions de rôles avec PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Après avoir vérifié que vous pouvez vous connecter au bureau virtuel Windows avec le principal du service, exécutez à nouveau l’offre de place de marché Azure ou le modèle Azure Resource Manager de GitHub, selon la méthode que vous utilisez. Suivez les instructions ci-dessous pour entrer les paramètres corrects pour votre méthode.

Si vous utilisez la place de marché Azure offre, fournir des valeurs pour les paramètres suivants pour s’authentifier correctement au bureau virtuel Windows :

- Client de bureau virtuel Windows propriétaire des services Bureau à distance : Principal du service
- ID d’application : Identification de la nouvelle entité de sécurité de service que vous avez créé
- Mot de passe/Confirmer le mot de passe : Le secret de mot de passe que vous avez généré pour le principal du service
- ID de locataire Azure AD : L’ID de locataire Azure AD du principal du service que vous avez créé

Si vous utilisez le modèle Azure Resource Manager de GitHub, fournir des valeurs pour les paramètres suivants pour s’authentifier correctement au bureau virtuel Windows :

- Locataire administrateur nom d’utilisateur principal (UPN) ou ID d’Application : Identification de la nouvelle entité de sécurité de service que vous avez créé
- Mot de passe administrateur locataire : Le secret de mot de passe que vous avez généré pour le principal du service
- IsServicePrincipal : **true**
- AadTenantId: L’ID de locataire Azure AD du principal du service que vous avez créé

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble sur la résolution des problèmes de bureau virtuel Windows et les pistes d’escalade de verrous, consultez [résolution des problèmes de vue d’ensemble, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes lors de la configuration d’une machine virtuelle (VM) dans un bureau virtuel Windows, consultez [configuration de machine virtuelle hôte de Session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion de client de bureau virtuel de Windows, consultez [les connexions Bureau à distance client](troubleshoot-client-connection.md).
- Pour résoudre les problèmes lors de l’utilisation de PowerShell avec le bureau virtuel de Windows, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour en savoir plus sur le service en version préliminaire, consultez [environnement en version préliminaire de Windows Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Suivez le [Didacticiel : Résoudre les problèmes de déploiements de modèle Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).