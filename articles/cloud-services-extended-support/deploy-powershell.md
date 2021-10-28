---
title: Déployer un service cloud (support étendu) - PowerShell
description: Déployer un service cloud (support étendu) à l’aide de PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: e157fcb3dd3c25d2724d9f0a190596549913edb3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229866"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Déployer un service cloud (support étendu) à l’aide d’Azure PowerShell

Cet article montre comment utiliser le module PowerShell `Az.CloudService` pour déployer, dans Azure, une instance Cloud Services (support étendu) qui dispose de plusieurs rôles (WebRole et WorkerRole).

## <a name="pre-requisites"></a>Conditions préalables

1. Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu) et créez les ressources associées. 
2. Installez le module PowerShell Az.CloudService.

    ```azurepowershell-interactive
    Install-Module -Name Az.CloudService 
    ```

3. Créer un groupe de ressources Cette étape est facultative si vous utilisez un groupe de ressources existant.   

    ```azurepowershell-interactive
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

4. Créez un compte de stockage et un conteneur qui sera utilisé pour stocker les fichiers de package de service cloud (.cspkg) et de configuration de service (.cscfg). Un nom unique comme nom de compte de stockage est requis. Cette étape est facultative si vous utilisez un compte de stockage existant.

    ```azurepowershell-interactive
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```
    
## <a name="deploy-a-cloud-services-extended-support"></a>Déployer une instance Cloud Services (support étendu)

Utilisez l’une des cmdlets PowerShell suivantes pour déployer les services cloud (prise en charge étendue) :

1. [**Création rapide d’un service cloud à l’aide d’un compte Stockage**](#quick-create-cloud-service-using-a-storage-account)

    - Ce paramètre définit les fichiers .cscfg, .cspkg et .csdef en tant qu’entrées, ainsi que le compte de stockage. 
    - Le profil de rôle de service cloud, le profil réseau et le profil de système d’exploitation sont créés par la cmdlet avec une entrée minimale de l’utilisateur. 
    - Dans le cas d’une entrée de certificat, le nom du coffre doit être spécifié. Les empreintes numériques du certificat dans le coffre de stockage sont validées par rapport à celles spécifiées dans le fichier .cscfg.
    
 2. [**Création rapide d’un service cloud à l’aide d’un URI SAS**](#quick-create-cloud-service-using-a-sas-uri)
 
    - Ce paramètre définit l’URI SAS du fichier .cspkg avec les chemins d’accès locaux des fichiers .csdef et .cscfg. Aucune entrée de compte de stockage n’est requise. 
    - Le profil de rôle de service cloud, le profil réseau et le profil de système d’exploitation sont créés par la cmdlet avec une entrée minimale de l’utilisateur. 
    - Dans le cas d’une entrée de certificat, le nom du coffre doit être spécifié. Les empreintes numériques du certificat dans le coffre de stockage sont validées par rapport à celles spécifiées dans le fichier .cscfg.
    
3. [**Créer un service cloud avec un rôle, un système d’exploitation, un profil de réseau et d’extension et des URI SAS**](#create-cloud-service-using-profile-objects--sas-uris)

    - Ce paramètre définit les URI SAS des fichiers .cscfg et .cspkg.
    - Le rôle, le réseau, le système d’exploitation et le profil d’extension doivent être spécifiés par l’utilisateur et doivent correspondre aux valeurs des fichiers .cscfg et .csdef. 

### <a name="quick-create-cloud-service-using-a-storage-account"></a>Création rapide d’un service cloud à l’aide d’un compte Stockage

Créez un déploiement de service cloud à l’aide des fichiers .cscfg, .csdef et .cspkg.

```azurepowershell-interactive
$cspkgFilePath = "<Path to cspkg file>"
$cscfgFilePath = "<Path to cscfg file>"
$csdefFilePath = "<Path to csdef file>"
      
# Create Cloud Service       
New-AzCloudService
-Name "ContosoCS" `
-ResourceGroupName "ContosOrg" `
-Location "EastUS" `
-ConfigurationFile $cscfgFilePath `
-DefinitionFile $csdefFilePath `
-PackageFile $cspkgFilePath `
-StorageAccount $storageAccount `
[-KeyVaultName <string>]
```

### <a name="quick-create-cloud-service-using-a-sas-uri"></a>Création rapide d’un service cloud à l’aide d’un URI SAS

1. Chargez votre package de service cloud (cspkg) dans le compte de stockage.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    $cscfgFilePath = "<Path to cscfg file>"
    $csdefFilePath = "<Path to csdef file>"
    ```

 2. Créez un déploiement de service cloud à l’aide de l’URI SAS de .cscfg, .csdef et .cspkg.

    ```azurepowershell-interactive
    New-AzCloudService
        -Name "ContosoCS" `
        -ResourceGroupName "ContosOrg" `
        -Location "EastUS" `
        -ConfigurationFile $cspkgFilePath `
        -DefinitionFile $csdefFilePath `
        -PackageURL $cspkgUrl `
        [-KeyVaultName <string>]
    ```
    
### <a name="create-cloud-service-using-profile-objects--sas-uris"></a>Créez un service cloud à l’aide d’objets de profil et d’URI SAS

1.  Chargez votre configuration de service cloud (cscfg) dans le compte de stockage. 

    ```azurepowershell-interactive
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```
2. Chargez votre package de service cloud (cspkg) dans le compte de stockage.

    ```azurepowershell-interactive
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
    
3. Créez un réseau virtuel et un sous-réseau. Cette étape est facultative si vous utilisez un réseau et un sous-réseau existant. Cet exemple utilise un seul réseau virtuel et un seul sous-réseau pour les deux rôles de service cloud (WebRole et WorkerRole). 

    ```azurepowershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
4. Créez une adresse IP publique et définissez sa propriété d'étiquette DNS. Cloud Services (support étendu) prend uniquement en charge les adresses IP publiques de la référence SKU [De base](../virtual-network/ip-services/public-ip-addresses.md#basic). Les adresses IP publiques de référence SKU standard ne fonctionnent pas avec Azure Cloud Services.
Si vous utilisez une adresse IP statique, vous devez la référencer comme adresse IP réservée dans le fichier de configuration de service (.cscfg).

    ```azurepowershell-interactive
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

5. Créez un objet de profil réseau et associez l’adresse IP publique au serveur front-end de l’équilibreur de charge. La plateforme Azure crée automatiquement une ressource d’équilibreur de charge de référence SKU « Classique » dans le même abonnement que la ressource de service cloud. La ressource d'équilibreur de charge est une ressource en lecture seule dans Azure Resource Manager. Les mises à jour de la ressource sont uniquement prises en charge par le biais des fichiers de déploiement de service cloud (.cscfg et .csdef).

    ```azurepowershell-interactive
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
6. Création d’un coffre de clés. Ce coffre de clés sera utilisé pour stocker les certificats associés aux rôles de service cloud (support étendu). Le coffre de clés doit se trouver dans la même région et le même abonnement que le service cloud et avoir un nom unique. Pour plus d’informations, consultez [Utiliser des certificats avec Azure Cloud Services (support étendu)](certificates-and-key-vault.md).

    ```azurepowershell-interactive
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

7. Chargez la stratégie d’accès au coffre de clés et accordez des autorisations de certificat à votre compte d’utilisateur. 

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Vous pouvez également définir la stratégie d’accès par le biais d’ObjectId (qui peut être obtenu en exécutant `Get-AzADUser`). 
    
    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

8. Dans cet exemple, nous allons ajouter un certificat auto-signé à un coffre de clés. L’empreinte numérique du certificat doit être ajoutée dans le fichier de configuration du service cloud (.cscfg) pour le déploiement sur les rôles de service cloud. 

    ```azurepowershell-interactive
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
9. Créez un objet en mémoire de profil de système d’exploitation. Le profil de système d’exploitation spécifie les certificats qui sont associés aux rôles de service cloud. Il s’agit du même certificat que celui créé à l’étape précédente. 

    ```azurepowershell-interactive
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

10. Créez un objet en mémoire de profil de rôle. Le profil de rôle définit des propriétés spécifiques à la référence SKU du rôle, comme le nom, la capacité et le niveau. Dans cet exemple, nous avons défini deux rôles : frontendRole et backendRole. Les informations de profil de rôle doivent correspondre à la configuration de rôle définie dans le fichier de configuration (cscfg) et dans le fichier de définition de service (csdef). 

    ```azurepowershell-interactive
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

11. (Facultatif) Créez un objet en mémoire de profil d’extension que vous voulez ajouter à votre service cloud. Pour cet exemple, nous allons ajouter l’extension RDP. 

    ```azurepowershell-interactive
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    
    ConfigFile doit avoir uniquement des balises PublicConfig et contenir un espace de noms comme suit :
   
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
    
12. (Facultatif) Définissez les balises comme table de hachage PowerShell que vous voulez ajouter à votre service cloud. 

    ```azurepowershell-interactive
    $tag=@{"Owner" = "Contoso"} 
    ```

13. Créez un déploiement de service cloud à l’aide d’objets de profil et d’URL SAS.

    ```azurepowershell-interactive
    $cloudService = New-AzCloudService ` 
        -Name “ContosoCS” ` 
        -ResourceGroupName “ContosOrg” ` 
        -Location “East US” ` 
        -PackageUrl $cspkgUrl ` 
        -ConfigurationUrl $cscfgUrl ` 
        -UpgradeMode 'Auto' ` 
        -RoleProfile $roleProfile ` 
        -NetworkProfile $networkProfile  ` 
        -ExtensionProfile $extensionProfile ` 
        -OSProfile $osProfile `
        -Tag $tag 
    ```

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [questions fréquentes (FAQ)](faq.yml) concernant Cloud Services (support étendu).
- Déployez une instance Cloud Services (support étendu) avec le [Portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
- Rendez-vous sur le [référentiel d’exemples d’Azure Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).