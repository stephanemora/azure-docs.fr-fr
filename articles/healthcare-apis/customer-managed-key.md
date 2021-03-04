---
title: Configurer des clés gérées par le client pour l’API Azure pour FHIR
description: Intégrez votre propre fonctionnalité de clé prise en charge dans l’API Azure pour FHIR par le biais de Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: f810769529569309656193d41f28cca201a85c07
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719217"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configurer des clés gérées par le client au repos

Quand vous créez un compte d’API Azure pour FHIR, vos données sont chiffrées à l’aide de clés managées par Microsoft par défaut. À présent, vous pouvez ajouter une deuxième couche de chiffrement pour les données à l’aide de votre propre clé que vous choisissez et gérez vous-même.

Dans Azure, cette opération s’effectue généralement à l’aide d’une clé de chiffrement dans le coffre de clés Azure du client. Azure SQL, Stockage Azure et Cosmos DB, pour ne citer qu’eux, offrent désormais cette fonctionnalité. L’API Azure pour FHIR tire parti de cette prise en charge à partir de Cosmos DB. Quand vous créez un compte, vous avez la possibilité de spécifier un URI de clé Azure Key Vault. Cette clé est transmise à Cosmos DB quand le compte de base de données est provisionné. Quand une demande FHIR est effectuée, Cosmos DB extrait votre clé et l’utilise pour chiffrer/déchiffrer les données. Pour commencer, vous pouvez consulter les liens suivants :

- [Inscrire le fournisseur de ressources Azure Cosmos DB dans l’abonnement Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurer votre instance Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Ajouter une stratégie d’accès à votre instance Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Générer une clé dans Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>En passant par le portail Azure

Lors de la création de votre compte d’API Azure pour FHIR dans le portail Azure, vous pouvez voir une option de configuration « Chiffrement des données » sous « Paramètres de base de données » sous l’onglet « Paramètres supplémentaires ». Par défaut, l’option Clé gérée par le service est sélectionnée. 

Vous pouvez choisir votre clé dans le sélecteur de clés :

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Sélecteur de clés":::

Ou vous pouvez spécifier votre clé Azure Key Vault ici en sélectionnant l’option « Clé gérée par le client ». Vous pouvez entrer l’URI de la clé ici.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Créer l’API Azure pour FHIR":::

Pour les comptes FHIR existants, vous pouvez afficher le choix du chiffrement à clé (clé gérée par le service ou le client) dans le panneau « Base de données » illustré ci-dessous. Vous ne pouvez pas modifier l’option de configuration une fois que vous l’avez choisie. Par contre, vous pouvez modifier et mettre à jour votre clé.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Sauvegarde de la base de données":::

Vous pouvez également créer une version de la clé spécifiée ; vos données seront alors chiffrées avec la nouvelle version sans aucune interruption de service. Vous pouvez également supprimer l’accès à la clé pour supprimer l’accès aux données. Lorsque la clé est désactivée, les requêtes génèrent une erreur. Si la clé est réactivée, les requêtes aboutiront.




## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

Avec l’URI de votre clé Azure Key Vault, vous pouvez configurer une clé gérée par le client à l’aide de PowerShell en exécutant la commande PowerShell ci-dessous :

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

Comme avec la méthode PowerShell, vous pouvez configurer la clé gérée par le client en passant l’URI de clé Azure Key Vault qui se trouve sous le paramètre `key-vault-key-uri`, et en exécutant la commande CLI ci-dessous : 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Utilisation d’un modèle Azure Resource Manager

Avec l’URI de clé Azure Key Vault, vous pouvez configurer la clé gérée par le client en la passant sous la propriété **keyVaultKeyUri** dans l’objet **properties**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Vous pouvez déployer le modèle avec le script PowerShell suivant :

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez vu comment configurer des clés gérées par le client au repos à l’aide du portail Azure, de PowerShell, de l’interface CLI et du modèle Resource Manager. Si vous avez d’autres questions, vous pouvez consulter la section Questions fréquentes (FAQ) d’Azure Cosmos DB : 
 
>[!div class="nextstepaction"]
>[Cosmos DB : comment configurer une CMK](../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)