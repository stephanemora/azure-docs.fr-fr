---
title: Secret Key Vault avec Bicep
description: Montre comment passer un secret à partir d’un coffre de clés en tant que paramètre lors du déploiement de Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: f96b9228b6ebe6ab3ca6d48dc3403bbafa6e8d55
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112029672"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-bicep-deployment"></a>Utiliser Azure Key Vault pour passer une valeur de paramètre sécurisée pendant le déploiement de Bicep

Au lieu de placer une valeur sécurisée (telle qu’un mot de passe) directement dans votre fichier Bicep ou votre fichier de paramètres, vous pouvez récupérer la valeur à partir d’un coffre [Azure Key Vault](../../key-vault/general/overview.md) pendant un déploiement. Vous récupérez la valeur en référençant le coffre de clés et la clé secrète dans votre fichier de paramètres. Lorsqu’un [module](./modules.md) attend un paramètre `string` avec un modificateur `secure:true`, vous pouvez utiliser la fonction `getSecret` pour obtenir un secret de coffre de clés. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Le coffre de clés peut exister dans un autre abonnement que le groupe de ressources sur lequel vous effectuez le déploiement.

Cet article est consacré au passage d’une valeur sensible en tant que paramètre Bicep. L’article ne traite pas de la définition d’une propriété de machine virtuelle sur l’URL d’un certificat dans un coffre de clés.
Vous trouverez un modèle de démarrage rapide de ce scénario dans [Installer un certificat à partir d’Azure Key Vault sur une machine virtuelle](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Déployer des coffres de clés et des secrets

Pour accéder à un coffre de clés lors d’un déploiement Bicep, définissez `enabledForTemplateDeployment` sur `true` dans le coffre de clés.

Si vous disposez déjà d’un coffre Key Vault, vérifiez qu’il autorise les déploiements de modèles.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Pour créer un coffre Key Vault et ajouter un secret, utilisez :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

En tant que propriétaire du coffre de clés, vous avez automatiquement accès à la création de secrets. Si l’utilisateur qui utilise les secrets n’est pas le propriétaire du coffre de clés, octroyez l’accès avec :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Pour plus d’informations sur la création de coffres de clés et l’ajout des secrets, consultez :

- [Définir et récupérer un secret à l'aide de l'interface de ligne de commande (CLI)](../../key-vault/secrets/quick-create-cli.md)
- [Définir et récupérer un secret à l’aide de PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Définir et récupérer un secret à l'aide du portail](../../key-vault/secrets/quick-create-portal.md)
- [Définir et récupérer un secret à l'aide de .NET](../../key-vault/secrets/quick-create-net.md)
- [Définir et récupérer un secret à l'aide de Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Accorder l'accès aux secrets

L’utilisateur qui déploie le fichier Bicep doit disposer de l’autorisation `Microsoft.KeyVault/vaults/deploy/action` pour l’étendue du groupe de ressources et du coffre de clés. Les rôles [propriétaire](../../role-based-access-control/built-in-roles.md#owner) et [contributeur](../../role-based-access-control/built-in-roles.md#contributor) accordent cet accès. Si vous avez créé le coffre de clés, vous êtes le propriétaire et vous avez donc l’autorisation.

La procédure suivante montre comment créer un rôle avec les permissions minimales et comment affecter l’utilisateur.

1. Créez un fichier JSON de définition de rôle personnalisé :

    ```json
    {
      "Name": "Key Vault Bicep deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a Bicep file with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```

    Remplacez « 00000000-0000-0000-0000-000000000000 » par l’ID d’abonnement.

2. Créez le nouveau rôle à l’aide du fichier JSON :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    L’exemple attribue le rôle personnalisé à l’utilisateur au niveau du groupe de ressources.

Quand vous utilisez un coffre de clés avec le fichier Bicep pour une [Application managée](../managed-applications/overview.md), vous devez accorder l’accès au principal de service du **fournisseur de ressources d’appliance**. Pour plus d’informations, consultez [Accéder au secret de coffre de clés pendant le déploiement d’applications managées Azure](../managed-applications/key-vault-access.md).

## <a name="use-getsecret-function"></a>Utiliser la fonction getSecret

Vous pouvez utiliser la [fonction `getSecret`](./bicep-functions-resource.md#getsecret) pour obtenir un secret de coffre de clés et passer la valeur au paramètre `string` d’un module. La fonction `getSecret` ne peut être appelée que dans une ressource `Microsoft.KeyVault/vaults`, et ne peut être utilisée qu’avec un paramètre associé à un décorateur `@secure()`.

Le fichier Bicep suivant crée un serveur Azure SQL. Le paramètre `adminPassword` a un décorateur `@secure()`.

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

Nous allons utiliser le fichier Bicep précédent en tant que module, étant donné que le nom de fichier est *sql.bicep* dans le répertoire où se trouve le fichier Bicep principal.

Le fichier Bicep suivant consomme sql.bicep en tant que module.  Le fichier Bicep référence un coffre de clés existant, appelle la fonction `getSecret` pour récupérer le secret du coffre de clés, puis passe la valeur en tant que paramètre au module.

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="reference-secrets-in-parameter-file"></a>Référencer des secrets dans un fichier de paramètres

Avec cette approche, vous référencez le coffre de clés dans le fichier de paramètres, et non dans Bicep. L’illustration suivante montre comment le fichier de paramètres référence le secret et passe cette valeur au fichier Bicep.

![Diagramme d’intégration du coffre de clés Resource Manager](./media/key-vault-parameter/statickeyvault.png)

Le modèle fichier Bicep déploie un serveur SQL qui comprend un mot de passe administrateur. Le paramètre du mot de passe est défini sur une chaîne sécurisée. Toutefois, le fichier Bicep ne spécifie pas d’où vient cette valeur.

```bicep
param adminLogin string

@secure()
param adminPassword string

param sqlServerName string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  name: sqlServerName
  location: resourceGroup().location
  properties: {
    administratorLogin: adminLogin
    administratorLoginPassword: adminPassword
    version: '12.0'
  }
}
```

---

À présent, créez un fichier de paramètres pour le fichier Bicep. Dans le fichier de paramètres, spécifiez un paramètre qui correspond au nom du paramètre dans le fichier Bicep. Pour la valeur du paramètre, référencez le secret du coffre de clés. Vous référencez le secret en transmettant l'identificateur de ressource du coffre de clés et le nom du secret :

Dans le fichier de paramètres suivant, le secret du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "value": "exampleadmin"
    },
    "adminPassword": {
      "reference": {
        "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
        },
        "secretName": "ExamplePassword"
      }
    },
    "sqlServerName": {
      "value": "<your-server-name>"
    }
  }
}
```

Si vous devez utiliser une version du secret autre que la version actuelle, utilisez la propriété `secretVersion`.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Déployez le modèle et transmettez le fichier de paramètres :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-file <Bicep-file> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile <Bicep-file> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Présentation d’Azure Key Vault](../../key-vault/general/overview.md)
- Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples) sur GitHub.
- Pour lire un module Microsoft Learn dans lequel est utilisée une valeur sécurisée issue d’un coffre de clés, consultez [Gérer des déploiements cloud complexes à l’aide des fonctionnalités avancées de modèle ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
