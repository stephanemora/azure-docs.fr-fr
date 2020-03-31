---
title: Créer un certificat Azure Key Vault | Place de marché Azure
description: Explique comment inscrire une machine virtuelle à partir d’un disque dur virtuel déployé dans Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: dc106ecf40367172f28c0f339bbcce8bddb50a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278073"
---
# <a name="create-certificates-for-azure-key-vault"></a>Créer des certificats pour Azure Key Vault

Cet article explique comment provisionner les certificats autosignés nécessaires pour établir une connectivité Windows Remote Management (WinRM) sur une machine virtuelle hébergée sur Azure. Le processus se déroule en trois étapes :

1.  Créez le certificat de sécurité. 
2.  Créez le coffre de clés Azure Key Vault pour stocker ce certificat. 
3.  Stockez les certificats dans ce coffre de clés. 

Vous pouvez utiliser un groupe de ressources Azure nouveau ou existant pour ce faire.  La première approche est utilisée dans l’explication suivante.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Créer le certificat

Modifiez et exécutez le script Azure Powershell suivant pour créer le fichier de certificat (.pfx) dans un dossier local.  Vous devrez remplacer les valeurs des paramètres suivants :

|  **Paramètre**        |   **Description**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Dossier local dans lequel enregistrer le fichier .pfx  |
| `$location`    | L’un des emplacements géographiques standard Azure  |
| `$vmName`      | Nom de la machine virtuelle Azure planifiée   |
| `$certname`    | Nom du certificat ; doit correspondre au nom de domaine complet de la machine virtuelle planifiée  |
| `$certpassword` | Mot de passe des certificats, doit correspondre au mot de passe utilisé pour la machine virtuelle planifiée  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
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
> Laissez la même session de console PowerShell activée pendant ces étapes afin que les valeurs des différents paramètres soient conservées.

> [!WARNING]
> Si vous enregistrez ce script, stockez-le dans un emplacement sécurisé, car il contient des informations de sécurité (un mot de passe).


## <a name="create-the-key-vault"></a>Créer le coffre de clés

Copiez le contenu du [modèle de déploiement du coffre de clés](./cpp-key-vault-deploy-template.md) dans un fichier sur votre ordinateur local. (Dans l’exemple de script ci-dessous, cette ressource est `C:\certLocation\keyvault.json`.)  Modifiez et exécutez le script Azure Powershell suivant pour créer une instance Azure Key Vault et le groupe de ressources associé.  Vous devrez remplacer les valeurs des paramètres suivants :

|  **Paramètre**        |   **Description**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Chaîne numérique arbitraire ajoutée aux identificateurs de déploiement                     |
| `$rgName`             | Nom du groupe de ressources Azure à créer                                        |
|  `$location`          | L’un des emplacements géographiques standard Azure                                  |
| `$kvTemplateJson`     | Chemin d’accès du fichier (keyvault.json) contenant le modèle Resource Manager pour le coffre de clés |
| `$kvname`             | Nom du nouveau coffre de clés                                                       |
|  |  |

```powershell
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

## <a name="store-the-certificate"></a>Stockez le certificat

Vous pouvez maintenant stocker les certificats contenus dans le fichier .pfx dans le nouveau coffre de clés en exécutant le script suivant. 

```powershell
    #push certificate to key vault secret

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


## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous allez [déployer une machine virtuelle à partir de votre image de machine virtuelle utilisateur](./cpp-deploy-vm-user-image.md).
