---
title: Renouveler un certificat de cluster Azure Service Fabric | Microsoft Docs
description: Découvrez comment renouveler un certificat de cluster Service Fabric identifié par le nom commun du certificat.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: aljo
ms.openlocfilehash: 549c5fed02402bb17b85fe796fecc4b53b321a66
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497072"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Renouveler manuellement un certificat de cluster Service Fabric
Lorsqu’un certificat de cluster Service Fabric est proche de la date d’expiration, vous devez le mettre à jour.  Le renouvellement de certificat est un processus simple si le cluster a été [configuré pour utiliser des certificats basés sur un nom commun](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (et pas une empreinte).  Obtenez un nouveau certificat auprès d’une autorité de certification avec une nouvelle date d’expiration.  Les certificats auto-signés ne sont pas pris en charge avec les clusters Service Fabric de production, afin d’inclure les certificats générés dans le cadre du workflow de création de cluster dans le portail Azure. Le nouveau certificat doit présenter le même nom commun que l’ancien certificat. 

Lorsque plusieurs certificats valides sont installés sur l’ordinateur hôte, le cluster Service Fabric utilise automatiquement le certificat déclaré dont la date d’expiration est la plus éloignée. L’utilisation d’un modèle Resource Manager pour provisionner les ressources Azure constitue une bonne pratique. Dans un environnement autre que de production, le script suivant peut être utilisé pour charger un nouveau certificat dans un coffre de clés, puis pour installer le certificat sur le groupe de machines virtuelles identiques : 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Vous ne pouvez pas utiliser un même ID de ressource pour deux secrets de groupes de machines virtuelles identiques, puisque chaque secret constitue une ressource unique avec sa propre version. 

Pour plus d’informations, consultez les articles suivants :
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* [Mettre à jour et gérer les certificats de cluster](service-fabric-cluster-security-update-certs-azure.md)

