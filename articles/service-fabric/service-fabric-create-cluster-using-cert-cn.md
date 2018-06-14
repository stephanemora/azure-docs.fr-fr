---
title: Créer un cluster Azure Service Fabric en utilisant un nom commun de certificat | Microsoft Docs
description: Découvrez comment créer un cluster Service Fabric en utilisant un nom commun de certificat à partir d’un modèle.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 8725dd1931b120b0369d0810fa49108a00c71e8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211063"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Déployer un cluster Service Fabric utilisant un nom commun de certificat au lieu d’une empreinte
Deux certificats ne peuvent pas avoir la même empreinte, ce qui complique la gestion ou le renouvellement des certificats de cluster. Toutefois, plusieurs certificats peuvent avoir le même nom commun ou objet.  Un cluster utilisant des noms communs de certificat facilite grandement la gestion des certificats. Cet article décrit le déploiement d’un cluster Service Fabric pour utiliser le nom commun d’un certificat à la place de son empreinte.
 
## <a name="get-a-certificate"></a>Obtenir un certificat
Commencez par obtenir un certificat auprès d’une [autorité de certification (AC)](https://wikipedia.org/wiki/Certificate_authority).  Le nom commun du certificat doit être le nom d’hôte du cluster.  Exemple : « myclustername.southcentralus.cloudapp.azure.com ».  

À des fins de test, vous pouvez obtenir un certificat signé par une autorité de certification auprès d’une autorité de certification ouverte ou gratuite.

> [!NOTE]
> Les certificats auto-signés, notamment ceux qui sont générés lors du déploiement d’un cluster Service Fabric dans le portail Azure, ne sont pas pris en charge.

## <a name="upload-the-certificate-to-a-key-vault"></a>Charger le certificat vers un coffre de clés
Dans Azure, un cluster Service Fabric est déployé sur un groupe de machines virtuelles identiques.  Chargez le certificat vers un coffre de clés.  Lors du déploiement du cluster, le certificat est installé sur le groupe de machines virtuelles identiques sur lequel le cluster s’exécute.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

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
```

## <a name="download-and-update-a-sample-template"></a>Télécharger et mettre à jour un exemple de modèle
Cet article utilise l’exemple de modèle [cluster sécurisé à 5 nœuds](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) et des paramètres du modèle. Téléchargez les fichiers *azuredeploy.json* et *azuredeploy.parameters.json* sur votre ordinateur.

### <a name="update-parameters-file"></a>Mettre à jour le fichier de paramètres
Tout d’abord, ouvrez le fichier *azuredeploy.parameters.json* dans un éditeur de texte, puis ajoutez la valeur de paramètre suivante :
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Ensuite, définissez les valeurs des paramètres *certificateCommonName*, *sourceVaultValue* et *certificateUrlValue* sur celles retournées par le script précédent :
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Mettre à jour le fichier de modèle
Ouvrez le fichier *azuredeploy.json* dans un éditeur de texte et effectuez trois mises à jour pour prendre en charge le nom commun du certificat.

1. Dans la section **parameters**, ajoutez un paramètre *certificateCommonName* :
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    Pensez également à supprimer le paramètre *certificateThumbprint*, car il n’est sans doute plus utile.

2. Définissez la valeur de la variable *sfrpApiVersion* sur « 2018-02-01 » :
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Dans la ressource **Microsoft.Compute/virtualMachineScaleSets**, mettez à jour l’extension de machine virtuelle pour utiliser le nom commun dans les paramètres du certificat au lieu de l’empreinte.  Dans **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate**, ajoutez `"commonNames": ["[parameters('certificateCommonName')]"],` et supprimez `"thumbprint": "[parameters('certificateThumbprint')]",`.
    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4.  Dans la ressource **Microsoft.ServiceFabric/clusters**, mettez à jour la version de l’API vers « 2018-02-01 ».  Ajoutez également un paramètre **certificateCommonNames** avec une propriété **commonNames** et supprimez le paramètre **certificate** (avec la propriété d’empreinte) comme dans l’exemple suivant :
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>Déployer le modèle mis à jour
Redéployez le modèle mis à jour après avoir apporté les modifications.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* Découvrez comment [renouveler un certificat de cluster](service-fabric-cluster-rollover-cert-cn.md).
* [Mettre à jour et gérer les certificats de cluster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png