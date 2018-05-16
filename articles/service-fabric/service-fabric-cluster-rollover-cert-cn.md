---
title: Renouveler un certificat de cluster Azure Service Fabric | Microsoft Docs
description: Découvrez comment renouveler un certificat de cluster Service Fabric identifié par le nom commun du certificat.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: a471e1aa8b68e8b94094dc79c8ae71f24cf290cb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Renouveler manuellement un certificat de cluster Service Fabric
Lorsqu’un certificat de cluster Service Fabric est proche de la date d’expiration, vous devez le mettre à jour.  Le renouvellement de certificat est un processus simple si le cluster a été [configuré pour utiliser des certificats basés sur un nom commun](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (et pas une empreinte).  Obtenez un nouveau certificat auprès d’une autorité de certification avec une nouvelle date d’expiration.  Les certificats auto-signés, notamment ceux qui sont générés lors du déploiement d’un cluster Service Fabric dans le portail Azure, ne sont pas pris en charge.  Le nouveau certificat doit présenter le même nom commun que l’ancien certificat. 

Le script suivant charge un nouveau certificat dans un coffre de clés, puis installe le certificat sur le groupe de machines virtuelles identiques.  Le cluster Service Fabric utilise automatiquement le certificat pourvu de la dernière date d’expiration.

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

Pour plus d’informations, consultez les articles suivants :
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* [Mettre à jour et gérer les certificats de cluster](service-fabric-cluster-security-update-certs-azure.md)

