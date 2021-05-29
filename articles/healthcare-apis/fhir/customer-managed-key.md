---
title: Configurer des clés gérées par le client pour l’API Azure pour FHIR
description: Intégrez votre propre fonctionnalité de clé prise en charge dans l’API Azure pour FHIR par le biais de Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/04/2021
ms.author: ginle
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 371b1286976a5f9dabfb82a5a706ff4a2672ceb2
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110700476"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configurer des clés gérées par le client au repos

Quand vous créez un compte d’API Azure pour FHIR, vos données sont chiffrées à l’aide de clés managées par Microsoft par défaut. À présent, vous pouvez ajouter une deuxième couche de chiffrement pour les données à l’aide de votre propre clé que vous choisissez et gérez vous-même.

Dans Azure, cette opération s’effectue généralement à l’aide d’une clé de chiffrement dans le coffre de clés Azure du client. Azure SQL, Stockage Azure et Cosmos DB, pour ne citer qu’eux, offrent désormais cette fonctionnalité. L’API Azure pour FHIR tire parti de cette prise en charge à partir de Cosmos DB. Quand vous créez un compte, vous avez la possibilité de spécifier un URI de clé Azure Key Vault. Cette clé est transmise à Cosmos DB quand le compte de base de données est provisionné. Quand une demande FHIR est effectuée, Cosmos DB extrait votre clé et l’utilise pour chiffrer/déchiffrer les données. 

Pour commencer, reportez-vous aux liens suivants :

- [Inscrire le fournisseur de ressources Azure Cosmos DB dans l’abonnement Azure](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurer votre instance Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Ajouter une stratégie d’accès à votre instance Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [Générer une clé dans Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>En passant par le portail Azure

Lorsque vous créez votre compte Azure API pour FHIR sur Portail Azure, vous remarquerez l’option de configuration **chiffrement des données** sous les **paramètres de base de données** sous l’onglet **paramètres supplémentaires** . Par défaut, l’option de clé gérée par le service est sélectionnée.

> [!Important]
> L’option de chiffrement des données n’est disponible que lorsque l’API Azure pour FHIR est créée et ne peut pas être modifiée par la suite. Toutefois, vous pouvez afficher et mettre à jour la clé de chiffrement si l’option **clé gérée par le client** est sélectionnée. 


Vous pouvez choisir votre clé dans le sélecteur de clés :

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Sélecteur de clés":::

Vous pouvez également spécifier votre clé de Azure Key Vault ici en sélectionnant l’option **de clé gérée par le client** .
 
Vous pouvez également entrer l’URI de la clé ici :

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Créer l’API Azure pour FHIR":::

> [!Important]
> Vérifiez que toutes les autorisations pour Azure Key Vault sont correctement définies. Pour plus d’informations, consultez [Ajouter une stratégie d’accès à votre instance de Azure Key Vault](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#add-access-policy). En outre, assurez-vous que la suppression réversible est activée dans les propriétés de l’Key Vault. Si vous n’effectuez pas ces étapes, une erreur de déploiement se produit. Pour plus d’informations, consultez [vérifier si la suppression réversible est activée sur un coffre de clés et activer la suppression réversible](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete).

Pour les comptes FHIR existants, vous pouvez afficher le choix de chiffrement à clé (clé **gérée** par le service ou **clé gérée** par le client) dans le panneau **de la base de données** , comme indiqué ci-dessous. L’option de configuration ne peut pas être modifiée une fois qu’elle est sélectionnée. Par contre, vous pouvez modifier et mettre à jour votre clé.

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

Dans cet article, vous avez appris à configurer des clés gérées par le client au repos à l’aide du modèle Portail Azure, PowerShell, CLI et Gestionnaire des ressources. Pour plus d’informations, reportez-vous à la section FAQ Azure Cosmos DB. 
 
>[!div class="nextstepaction"]
>[Cosmos DB : comment configurer une CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)
