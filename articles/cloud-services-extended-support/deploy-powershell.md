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
ms.openlocfilehash: bcf6b2f6b964a056b9d90f08c0586fcbdec5b260
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167274"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Déployer un service cloud (support étendu) à l’aide d’Azure PowerShell

Cet article montre comment utiliser le module PowerShell `Az.CloudService` pour déployer, dans Azure, une instance Cloud Services (support étendu) qui dispose de plusieurs rôles (WebRole et WorkerRole) et de l’extension Bureau à distance. 

## <a name="before-you-begin"></a>Avant de commencer

Consultez les [prérequis du déploiement](deploy-prerequisite.md) de Cloud Services (support étendu) et créez les ressources associées. 

## <a name="deploy-a-cloud-services-extended-support"></a>Déployer une instance Cloud Services (support étendu)
1. Installez le module PowerShell Az.CloudService.  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Créer un groupe de ressources Cette étape est facultative si vous utilisez un groupe de ressources existant.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Créez un compte de stockage et un conteneur qui sera utilisé pour stocker les fichiers de package de service cloud (.cspkg) et de configuration de service (.cscfg). Vous devez utiliser un nom unique comme nom de compte de stockage. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Chargez votre package de service cloud (cspkg) dans le compte de stockage.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Chargez votre configuration de service cloud (cscfg) dans le compte de stockage. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Créez un réseau virtuel et un sous-réseau. Cette étape est facultative si vous utilisez un réseau et un sous-réseau existant. Cet exemple utilise un seul réseau virtuel et un seul sous-réseau pour les deux rôles de service cloud (WebRole et WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Créez une adresse IP publique et définissez sa propriété d’étiquette DNS. Azure Cloud Services (support étendu) prend uniquement en charge les adresses IP publiques de référence SKU (https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) [De base]. Les adresses IP publiques de référence SKU standard ne fonctionnent pas avec Azure Cloud Services.
Si vous utilisez une adresse IP statique, vous devez la référencer comme adresse IP réservée dans le fichier de configuration de service (.cscfg). 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Créez un objet de profil réseau et associez l’adresse IP publique au serveur front-end de l’équilibreur de charge. La plateforme Azure crée automatiquement une ressource d’équilibreur de charge de référence SKU « Classique » dans le même abonnement que la ressource de service cloud. La ressource d’équilibreur de charge est une ressource en lecture seule dans ARM. Les mises à jour de la ressource sont prises en charge uniquement par le biais des fichiers de déploiement de service cloud (.cscfg & .csdef).

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Création d’un coffre de clés. Ce coffre de clés sera utilisé pour stocker les certificats associés aux rôles de service cloud (support étendu). Le coffre de clés doit se trouver dans la même région et le même abonnement que le service cloud et avoir un nom unique. Pour plus d’informations, consultez [Utiliser des certificats avec Azure Cloud Services (support étendu)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Chargez la stratégie d’accès au coffre de clés et accordez des autorisations de certificat à votre compte d’utilisateur. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Vous pouvez également définir la stratégie d’accès par le biais d’ObjectId (qui peut être obtenu en exécutant `Get-AzADUser`) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Pour les besoins de cet exemple, nous allons ajouter un certificat auto-signé à un coffre de clés. L’empreinte numérique du certificat doit être ajoutée dans le fichier de configuration du service cloud (.cscfg) pour le déploiement sur les rôles de service cloud. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Créez un objet en mémoire de profil de système d’exploitation. Le profil de système d’exploitation spécifie les certificats qui sont associés aux rôles de service cloud. Il s’agit du même certificat que celui créé à l’étape précédente. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Créez un objet en mémoire de profil de rôle. Le profil de rôle définit des propriétés spécifiques aux références SKU d’un rôle, comme le nom, la capacité et le niveau. Pour cet exemple, nous avons défini deux rôles : frontendRole et backendRole. Les informations de profil de rôle doivent correspondre à la configuration de rôle définie dans le fichier de configuration (cscfg) et dans le fichier de définition de service (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Facultatif) Créez un objet en mémoire de profil d’extension que vous voulez ajouter à votre service cloud. Pour cet exemple, nous allons ajouter l’extension RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Notez que configFile doit avoir uniquement des balises PublicConfig et contenir un espace de noms comme suit :
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Facultatif) Définissez les balises comme table de hachage PowerShell que vous voulez ajouter à votre service cloud. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Créez un déploiement de service cloud à l’aide d’objets de profil et d’URL SAS.

    ```powershell
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
- Consultez les [questions fréquentes (FAQ)](faq.md) concernant Cloud Services (support étendu).
- Déployez un service cloud (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
- Visitez le [dépôt d’exemples de Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).
