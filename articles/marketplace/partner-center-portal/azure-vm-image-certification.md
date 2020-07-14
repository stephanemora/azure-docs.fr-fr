---
title: Certification de machine virtuelle Azure - Place de marché Azure
description: Découvrez comment tester et envoyer une offre de machine virtuelle sur la Place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: emuench
ms.author: mingshen
ms.date: 04/09/2020
ms.openlocfilehash: d3b89945c077b9c26bab1709bd6d1def20959e33
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110043"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certification d’image de machine virtuelle Azure

Cet article explique comment tester et envoyer une image de machine virtuelle vers la Place de marché commerciale afin de vérifier qu’elle répond aux exigences de publication les plus récentes de la Place de marché Azure.

Effectuez les étapes suivantes avant d’envoyer votre offre de machine virtuelle :

1. Créez et déployez des certificats.
2. Déployez une machine virtuelle Azure à l’aide de votre image généralisée.
3. Effectuez les validations.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Créer et déployer des certificats pour Azure Key Vault

Cette section explique comment créer et déployer les certificats auto-signés qui sont nécessaires pour configurer la connexion entre Windows Remote Management (WinRM) et une machine virtuelle hébergée dans Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Créer des certificats pour Azure Key Vault

Le processus se déroule en trois étapes :

1. Créez le certificat de sécurité.
2. Créez le coffre de clés Azure Key Vault qui doit stocker ce certificat.
3. Stockez les certificats dans ce coffre de clés.

Vous pouvez utiliser un groupe de ressources Azure nouveau ou existant pour ce faire.

#### <a name="create-the-security-certificate"></a>Créer le certificat de sécurité

Modifiez et exécutez le script Azure PowerShell suivant pour créer le fichier de certificat (.pfx) dans un dossier local. Remplacez les valeurs des paramètres indiqués dans le tableau suivant.

| **Paramètre** | **Description** |
| --- | --- |
| $certroopath | Dossier local dans lequel enregistrer le fichier .pfx |
| $location | Un des emplacements géographiques standard Azure |
| $vmName | Nom de la machine virtuelle Azure planifiée |
| $certname | Nom du certificat. Doit correspondre au nom de domaine complet de la machine virtuelle planifiée |
| $certpassword | Mot de passe des certificats. Doit correspondre au mot de passe utilisé pour la machine virtuelle planifiée |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
    $location = "westus"

    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"

    # Certification name - should match with FQDN of Windows Azure creating VM
    $certname = "$vmName.$location.cloudapp.azure.com"

    # Certification password - should be match with password of Windows Azure creating VM
    $certpassword = "SecretPassword@123"

    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb

```

> [!TIP]
> Laissez s’exécuter la même session de console Azure PowerShell pendant ces étapes pour conserver les valeurs des différents paramètres.

> [!WARNING]
> Si vous enregistrez ce script, vous devez l’enregistrer dans un emplacement sécurisé, car il contient des informations de sécurité (un mot de passe).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Créer le coffre de clés Azure pour stocker ce certificat

Sur votre ordinateur local, copiez le contenu du modèle ci-dessous dans un fichier. Dans l’exemple de script ci-dessous, cette ressource est `C:\certLocation\keyvault.json`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Modifiez et exécutez le script Azure PowerShell suivant pour créer une instance Azure Key Vault et le groupe de ressources associé. Remplacer les valeurs des paramètres indiqués dans le tableau suivant

| **Paramètre** | **Description** |
| --- | --- |
| $postfix | Chaîne numérique aléatoire ajoutée aux identificateurs de déploiement |
| $rgName | Nom du groupe de ressources Azure à créer |
| $location | Un des emplacements géographiques standard Azure |
| $kvTemplateJson | Chemin du fichier (keyvault.json) contenant le modèle Resource Manager pour le coffre de clés |
| $kvname | Nom du nouveau coffre de clés|
|   |   |

```PowerShell
    # Creating Key vault in resource group

    # "Random" number for deployment identifiers
    $postfix = "0101048"

    # Resource group name
    $rgName = "TestRG$postfix"

    # Location of Resource Group
    $location = "westus"

    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"

    # Key vault name
    $kvname = "isvkv$postfix"

    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host

        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }

        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id

        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message
      Break
      }

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host

        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message
        Break
        }

    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------"

    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId

    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all

```

#### <a name="store-the-certificates-to-the-key-vault"></a>Stocker les certificats dans le coffre de clés

Stockez les certificats contenus dans le fichier .pfx dans le nouveau coffre de clés à l’aide de ce script :

```PowerShell
     $fileName =$certroopath+"\$certname"+".pfx"

     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id

```

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Déployer une machine virtuelle Azure à l’aide de votre image généralisée

Cette section explique comment déployer une image de disque dur virtuel généralisée pour créer une ressource de machine virtuelle Azure. Pour ce processus, nous allons utiliser le modèle Azure Resource Manager et le script Azure PowerShell fournis.

### <a name="prepare-an-azure-resource-manager-template"></a>Préparer un modèle Azure Resource Manager

Copiez le modèle Azure Resource Manager suivant pour le déploiement de disque dur virtuel dans un fichier local nommé VHDtoImage.json. Le script suivant nécessite que l’emplacement de l’ordinateur local utilise ce code JSON.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Modifiez ce fichier pour fournir les valeurs de ces paramètres :

| **Paramètre** | **Description** |
| --- | --- |
| ResourceGroupName | Le nom du groupe de ressources Azure existant. En général, vous devez utiliser le même groupe de ressources que votre coffre de clés. |
| TemplateFile | Chemin complet du fichier VHDtoImage.json. |
| userStorageAccountName | Nom du compte de stockage. |
| sNameForPublicIP | Nom DNS de l’adresse IP publique. Doit être en minuscules. |
| subscriptionId | Identificateur de l’abonnement Azure. |
| Emplacement | Sélectionnez un emplacement géographique Azure standard pour le groupe de ressources. |
| vmName | Nom de la machine virtuelle. |
| VaultName | Nom du coffre de clés. |
| vaultResourceGroup | Groupe de ressources du coffre de clés. |
| certificateUrl | Adresse web (URL) du certificat, y compris la version stockée dans le coffre de clés, par exemple : `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`. |
| vhdUrl | Adresse web du disque dur virtuel. |
| vmSize | Taille de l’instance de machine virtuelle. |
| publicIPAddressName | Nom de l’adresse IP publique. |
| virtualNetworkName | Nom du réseau virtuel. |
| nicName | Nom de la carte d’interface réseau du réseau virtuel. |
| adminUsername | Nom d’utilisateur du compte administrateur. |
| adminPassword | Mot de passe de l’administrateur. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Déployer une machine virtuelle Azure

Copiez et modifiez le script suivant pour indiquer les valeurs des variables `$storageaccount` et `$vhdUrl`. Exécutez-le pour créer une ressource de machine virtuelle Azure à partir de votre disque dur virtuel généralisé existant.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Effectuer les validations

Il existe deux façons d’effectuer des validations sur l’image déployée :

- Utiliser l’outil Certification Test Tool for Azure Certified
- Utiliser l’API d’auto-test

### <a name="download-and-run-the-certification-test-tool"></a>Télécharger et exécuter l’outil Certification Test

L’outil Certification Test Tool for Azure Certified s’exécute sur une machine Windows locale, mais teste une machine virtuelle Windows ou Linux basée sur Azure. Il vérifie que votre image de machine virtuelle utilisateur peut être utilisée avec Microsoft Azure, autrement dit que les recommandations et conditions requises de la préparation de votre VHD ont été satisfaites. La sortie de l’outil est un rapport de compatibilité, que vous devez charger dans le portail Espace partenaires pour demander la certification de la machine virtuelle.

1. Téléchargez et installez la version la plus récente de l’outil [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Ouvrez l’outil de certification, puis sélectionnez **Démarrer un nouveau test**.
3. Dans l’écran **Test Information** (Informations sur le test), entrez un nom de test dans le champ **Test Name** (Nom du test) pour la série de tests.
4. Dans le champ **Plateforme**, sélectionnez Windows Server ou Linux pour votre machine virtuelle. La plateforme que vous choisissez a une incidence sur le reste des options.
5. Si votre machine virtuelle utilise ce service de base de données, cochez la case **Test pour Azure SQL Database**.

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Connecter l’outil de certification à une image de machine virtuelle

L’outil se connecte aux machines virtuelles Windows avec [Azure PowerShell](https://docs.microsoft.com/powershell/), et se connecte aux machines virtuelles Linux par le biais de [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Connecter l’outil de certification à une image de machine virtuelle Linux

1. Sélectionnez le mode **SSH Authentication** (Authentification SSH) : Password Authentication (authentification par mot de passe) ou Key File Authentication (authentification par fichier de clé).
2. Si vous utilisez l’authentification basée sur un mot de passe, renseignez les champs **VM DNS Name** (Nom du DNS de machine virtuelle), **User Name** (Nom d’utilisateur) et **Password** (Mot de passe). Vous pouvez également modifier la valeur par défaut du champ **SSH Port** (Port SSH).

    ![Azure Certified Test Tool, authentification par mot de passe de l’image de machine virtuelle Linux](media/avm-cert2.png)

3. Si vous utilisez l’authentification par fichier de clé, renseignez les champs **VM DNS Name** (Nom du DNS de machine virtuelle), **User Name** (Nom d’utilisateur) et **Private key** (Clé privée). Vous pouvez également renseigner le champ **Passphrase** (Phrase secrète) ou modifier la valeur par défaut du champ **SSH Port** (Port SSH).

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Connecter l’outil de certification à une image de machine virtuelle sous Windows**

1. Saisissez le **nom DNS complet de la machine virtuelle** (par exemple, MyVMName.Cloudapp.net).
2. Renseignez les champs **User Name** (Nom d’utilisateur) et **Password** (Mot de passe).

    ![Azure Certified Test Tool, authentification par mot de passe de l’image de machine virtuelle Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Exécuter un test de certification

Après avoir fourni les valeurs des paramètres pour votre image de machine virtuelle dans l’outil de certification, sélectionnez **Test Connection** (Tester la connexion) pour créer une connexion valide vers votre machine virtuelle. Une fois la connexion vérifiée, sélectionnez **Next** (Suivant) pour démarrer le test. Une fois le test terminé, les résultats du test sont affichés dans un tableau. La colonne Status (État) affiche Pass/Fail/Warning (Réussite/Échec/Avertissement) pour chaque test. Si l’un des tests échoue, votre image n’est _pas_ certifiée. Dans ce cas, examinez les exigences et les messages d’échec, apportez les modifications suggérées, puis réexécutez le test.

Une fois le test automatisé terminé, fournissez des informations supplémentaires sur votre image de machine virtuelle sous les deux onglets de l’écran **Questionnaire** que sont **General Assessment** (Évaluation générale) et **Kernel Customization** (Personnalisation du noyau), puis sélectionnez **Next** (Suivant).

Le dernier écran vous permet de fournir des informations supplémentaires, telles que les informations d’accès SSH pour une image de machine virtuelle Linux, ainsi qu’une explication des éventuels échecs d’évaluation si vous recherchez les exceptions.

Enfin, sélectionnez **Generate Report** (Générer le rapport) afin de télécharger les résultats de test et les fichiers journaux pour les cas de test exécutés, ainsi que vos réponses au questionnaire. Enregistrez les résultats dans le même conteneur que vos disques durs virtuels.

## <a name="next-step"></a>Étape suivante

- [Problèmes courants des URI SAS et correctifs](common-sas-uri-issues.md)
