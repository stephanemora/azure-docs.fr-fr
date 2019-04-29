---
title: Utiliser des identités gérées dans Gestion des API Azure | Microsoft Docs
description: Découvrez comment utiliser des identités gérées dans Gestion des API
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: ebded5d1d58baf501ee5106d622162edc62d46ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656674"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Utiliser des identités gérées dans Gestion des API Azure

Cet article vous montre comment créer une identité gérée pour une instance de service de gestion des API et comment accéder aux autres ressources. Une identité gérée générée par Azure Active Directory (Azure AD) permet à votre instance gestion des API pour facilement et en toute sécurité accéder aux autres ressources protégées par AD Azure, telles qu’Azure Key Vault. Cette identité est gérée par Azure et vous dispense à approvisionner ou permuter des clés secrètes. Pour plus d’informations sur les identités, consultez [What ' s des identités gérées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Créer une identité gérée pour une instance de la gestion des API

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour configurer une identité gérée dans le portail, vous serez tout d’abord créer une instance de la gestion des API comme d’habitude, puis activez la fonctionnalité.

1. Créez une instance Gestion des API dans le portail comme vous le faites en temps normal. Accédez-y dans le portail.
2. Sélectionnez **identités de service managées**.
3. Définissez Inscrire auprès d’Azure Active Directory sur Activé. Cliquez sur Enregistrer.

![Activer MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Utilisation du modèle Azure Resource Manager

Vous pouvez créer une instance Gestion des API avec une identité en incluant la propriété suivante dans la définition de la ressource :

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ce code indique à Azure de créer et de manager l’identité de votre instance Gestion des API.

Par exemple, un modèle complet Azure Resource Manager peut se présenter comme suit :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": {
                "type": "systemAssigned"
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Utiliser l’identité de service managée pour accéder à d’autres ressources

> [!NOTE]
> Actuellement, les identités gérées peuvent être utilisées pour obtenir des certificats à partir d’Azure Key Vault pour les noms de domaine personnalisé de gestion des API. D’autres scénarios seront bientôt pris en charge.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Obtenir un certificat à partir d’Azure Key Vault

#### <a name="prerequisites"></a>Conditions préalables
1. Le coffre de clés contenant le certificat pfx doit être dans le même abonnement Azure et le même groupe de ressources que le service Gestion des API. C’est une exigence du modèle Azure Resource Manager.
2. Le type de contenu du secret doit être *application/x-pkcs12*. Vous pouvez utiliser le script suivant pour charger le certificat :

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Si la version de l’objet du certificat n’est pas fournie, le service Gestion des API obtiendra automatiquement la version la plus récente du certificat après son chargement dans Key Vault.

L’exemple suivant illustre un modèle Azure Resource Manager qui contient les étapes suivantes :

1. Créez une instance de la gestion des API avec une identité gérée.
2. Mettre à jour les stratégies d’accès d’une instance Azure Key Vault et permettre à l’instance Gestion des API d’obtenir des secrets à partir de cette dernière.
3. Mettre à jour l’instance Gestion des API en définissant un nom de domaine personnalisé à l’aide d’un certificat obtenu à partir de l’instance Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les identités pour les ressources Azure :

* [Nouveautés d’identités gérées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Modèles Microsoft Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
