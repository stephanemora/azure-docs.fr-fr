---
title: Stocker des informations d’identification d’accès en toute sécurité sur les machines virtuelles de science des données - Azure | Microsoft Docs
description: Stockez des informations d’identification d’accès en toute sécurité sur les machines virtuelles de science des données (DSVM).
keywords: formation approfondie, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale, processus de science des données en équipe
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 9ec734cf456050250396b00aa09b61bace7e9aa0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830263"
---
# <a name="store-access-credentials-on-the-data-science-virtual-machine-securely"></a>Stocker des informations d’identification d’accès en toute sécurité sur les machines virtuelles de science des données (DSVM)

La gestion des informations d’identification qui doivent se trouver dans votre code pour s’authentifier auprès des services cloud constitue un défi courant lors de la génération d’applications cloud. La sécurisation de ces informations d’identification est une tâche importante. Dans l’idéal, elles ne s’affichent jamais sur les stations de travail de développement ou ne sont jamais archivées dans le contrôle de code source. 

[Managed Service Identity (MSI)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement managée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. Un modèle commun de sécurisation des informations d’identification consiste à utiliser MSI en combinaison avec [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), un service Azure managé permettant de stocker les clés de chiffrement et les secrets en toute sécurité. Vous pouvez accéder à la solution Key Vault à l’aide de l’identité de service managée et récupérer à partir de celle-ci les secrets et les clés de chiffrement autorisés. 

La documentation de MSI et Key Vault est une ressource complète comptant des informations détaillées sur ces services. Le reste de cet article est un manuel de base illustrant l’utilisation élémentaire de MSI et de Key Vault sur la machine virtuelle DSVM. 

## <a name="walkthrough-of-using-msi-to-securely-access-azure-resources"></a>Procédure pas à pas d’utilisation de MSI pour accéder en toute sécurité aux ressources Azure

## <a name="1-create-msi-on-the-dsvm"></a>1. Créer MSI sur la machine virtuelle DSVM 


```
# Pre-requisites: You have already created a Data Science VM in the usual way

# Create identity principal for the VM
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal id of the DSVM
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```


## <a name="2-assign-keyvault-access-permission-to-vm-principal"></a>2. Assigner une autorisation d’accès à Key Vault à un principal de machine virtuelle
```
# Pre-requisites: You have already create an empty Key Vault resource on Azure using Portal or Azure CLI 

# Assign only get and set permission but not the capability to list the keys
az keyvault set-policy --object-id <PrincipalID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="3-access-a-secret-in-the-keyvault-from-the-dsvm"></a>3. Accéder à un secret dans Key Vault à partir de la machine virtuelle DSVM

```
# Get the access token for VM
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access Keyvault using access token. 
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="4-access-storage-keys-from-the-dsvm"></a>4. Accéder aux clés de stockage à partir de la machine virtuelle DSVM

```
# Pre-requisites: You have granted your VM's MSI access to use storage account access keys based on instruction from this article:https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage that describes this process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

#Now you can access the data in the storage account from the retrieved Storage account keys
```
## <a name="5-access-keyvault-from-python"></a>5. Accéder à Key Vault à partir de Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a KeyVault client
key_vault_client = KeyVaultClient(
credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
key_vault_uri,  # Your KeyVault URL
"SQLPasswd",       # Name of your secret that already exists in the Key Vault
""              # The version of the secret. Empty string for latest
)
print("My secret value is {}".format(secret.value))
```

## <a name="6-azure-cli-access-from-vm"></a>6. Accéder à Azure CLI à partir d’une machine virtuelle

```
# With a Managed Service Identity setup on the DSVM, users on the DSVM can execute Azure CLI to perform the authorized functions. Here are commands to access Key Vault from a Azure CLI without having to login to an Azure account. 
# Pre-requisites: MSI is already setup on the DSVM as indicated earlier and specific permission like accessing storage account keys, reading specific secrets and writing new secrets is provided to the MSI . 

# Authenticate to Azure CLI without requiring an Azure Account. 
az login --msi

# Retrieve a secret from Key Vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new Secret in Key Vault
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List Storage Account Access Keys
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
