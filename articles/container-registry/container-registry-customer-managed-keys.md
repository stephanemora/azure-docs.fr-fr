---
title: Chiffrement au repos avec des clés gérées par le client
description: Apprenez-en davantage sur le chiffrement au repos de votre registre de conteneurs Azure et sur la façon de chiffrer votre registre avec une clé gérée par le client, stockée dans Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498953"
---
# <a name="encryption-using-customer-managed-keys"></a>Chiffrement à l’aide de clés gérées par le client

Lorsque vous stockez des images et d’autres artefacts dans un registre de conteneurs Azure, Azure chiffre automatiquement le contenu du registre au repos avec des [clés gérées par le service](../security/fundamentals/encryption-atrest.md#data-encryption-models). Vous pouvez compléter le chiffrement par défaut avec une couche de chiffrement supplémentaire à l’aide d’une clé que vous créez et gérez dans Azure Key Vault. Cet article vous guide tout au long des étapes utilisant Azure CLI et le portail Azure.

Le chiffrement côté serveur avec des clés gérées par le client est pris en charge via l’intégration à [Azure Key Vault](../key-vault/key-vault-overview.md). Vous pouvez créer vos propres clés de chiffrement et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés de chiffrement. Avec Azure Key Vault, vous pouvez également auditer l’utilisation des clés.

Cette fonctionnalité est disponible uniquement au niveau de service **Premium** de registre de conteneurs. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Références SKU Azure Container Registry](container-registry-skus.md).

> [!IMPORTANT]
> Cette fonctionnalité est actuellement en préversion et certaines [limitations s’appliquent](#preview-limitations). Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.
>
   
## <a name="preview-limitations"></a>Limitations de la version préliminaire 

* Vous pouvez actuellement activer cette fonctionnalité uniquement lorsque vous créez un registre.
* Après avoir activé une clé gérée par le client sur un registre, vous ne pouvez pas la désactiver.
* L’[approbation de contenu](container-registry-content-trust.md) n’est actuellement pas prise en charge dans un registre chiffré avec une clé gérée par le client.
* Dans un registre chiffré avec une clé gérée par le client, les journaux d’exécution pour les [tâches ACR](container-registry-tasks-overview.md) sont actuellement conservés pendant 24 heures uniquement. Si vous devez conserver les journaux plus longtemps, consultez les instructions pour [exporter et stocker les journaux d’exécution des tâches](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Prérequis

Pour utiliser les étapes Azure CLI décrites dans cet article, vous devez disposer d’Azure CLI version 2.2.0 ou ultérieure. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Activer une clé gérée par le client – interface de ligne de commande

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Si nécessaire, exécutez la commande [az group create][az-group-create] pour créer un groupe de ressources pour la création du coffre de clés, du registre de conteneurs et d’autres ressources requises.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Créez une [identité managée attribuée par l’utilisateur pour des ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) avec la commande [az identity create][az-identity-create]. Cette identité sera utilisée par votre registre pour accéder au service Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Dans la sortie de la commande, prenez note des valeurs suivantes : `id` et `principalId`. Vous aurez besoin de ces valeurs dans les étapes ultérieures pour configurer l’accès du registre au coffre de clés.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Par souci pratique, stockez ces valeurs dans des variables d’environnement :

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Création d’un coffre de clés

Créez un coffre de clés avec [az keyvault create][az-keyvault-create] pour stocker une clé gérée par le client pour le chiffrement du registre. 

Pour éviter toute perte de données provoquée par la suppression accidentelle d’une clé ou d’un coffre de clés, vous devez activer les paramètres suivants : **Suppression réversible** et **Protection de purge**. L’exemple suivant inclut des paramètres pour ces paramètres : 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Ajouter une stratégie d’accès au coffre de clés

Configurez une stratégie pour le coffre de clés afin que l’identité puisse y accéder. Dans la commande [az keyvault set-policy][az-keyvault-set-policy] suivante, vous transmettez l’ID du principal de l’identité managée que vous avez créée, précédemment stocké dans une variable d’environnement. Définissez les autorisations de clé sur **get**, **unwrapKey** et **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Créer une clé et obtenir un ID de clé

Exécutez la commande [az keyvault key create][az-keyvault-key-create] pour créer une clé dans le coffre de clés.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Dans la sortie de la commande, prenez note de l’ID de la clé, `kid`. Vous utiliserez cet ID dans l’étape suivante :

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Pour plus de commodité, stockez cette valeur dans une variable d’environnement :

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Créer un registre avec une clé gérée par le client

Exécutez la commande [az acr create][az-acr-create] pour créer un registre et activer la clé gérée par le client. Transmettez l’ID du principal de l’identité managée et l’ID de clé, précédemment stockés dans des variables d’environnement :

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Afficher l’état de chiffrement

Pour indiquer si le chiffrement du registre avec une clé gérée par le client est activé, exécutez la commande [az acr encryption show][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Activer une clé gérée par le client – portail

### <a name="create-a-managed-identity"></a>Créer une identité managée

Créez une [identité managée attribuée par l’utilisateur pour des ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) dans le portail Azure. Pour connaître les étapes, consultez [Créer une identité managée attribuée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Notez le **nom de ressource** de l’identité managée. Vous aurez besoin de ce nom dans des étapes ultérieures.

![Créer une identité managée attribuée par l’utilisateur dans le portail Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Création d’un coffre de clés

Pour connaître la procédure de création d’un coffre de clés, consultez [Démarrage rapide : Définir et récupérer un secret depuis Azure Key Vault à l’aide du portail Azure](../key-vault/quick-create-portal.md).

Lorsque vous créez un coffre de clés pour une clé gérée par le client, sous l’onglet **Informations de base**, vous devez activer les paramètres de protection suivants : **Suppression réversible** et **Protection de purge**. Ces paramètres permettent d’éviter toute perte de données provoquée par la suppression accidentelle d’une clé ou d’un coffre de clés.

![Créer un coffre de clés dans le portail Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Ajouter une stratégie d’accès au coffre de clés

Configurez une stratégie pour le coffre de clés afin que l’identité puisse y accéder.

1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres** > **Stratégies d’accès > +Ajouter une stratégie d’accès**.
1. Sélectionnez **Autorisations de clé**, puis **Obtenir**, **Ne pas inclure la clé** et **Inclure la clé**.
1. Sélectionnez **Sélectionner le principal** et sélectionnez le nom de ressource de votre identité managée attribuée par l’utilisateur.  
1. Sélectionnez **Ajouter**, puis **Enregistrer**.

![Créer une stratégie d’accès au coffre de clés](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Créer une clé

1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres** > **Clés**.
1. Sélectionnez **+Générer/Importer** et entrez un nom unique pour la clé.
1. Acceptez les valeurs par défaut restantes et sélectionnez **Créer**.
1. Après la création, sélectionnez la clé et notez la version actuelle de la clé.

### <a name="create-azure-container-registry"></a>Créer un registre de conteneurs Azure

1. Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.
1. Sous l’onglet **Informations de base**, sélectionnez ou créez un groupe de ressources, puis entrez un nom de registre. Dans **Référence (SKU)** , sélectionnez **Premium**.
1. Sous l’onglet **Chiffrement**, dans **Clé gérée par le client**, sélectionnez **Activée**.
1. Dans **Identité**, sélectionnez l’identité managée que vous avez créée.
1. Dans **Clé de chiffrement**, sélectionnez **Sélectionner dans le coffre de clés**.
1. Dans la fenêtre **Sélectionner une clé à partir d’Azure Key Vault**, sélectionnez le coffre de clés, la clé et la version que vous avez créés dans la section précédente.
1. Sous l’onglet **Chiffrement**, sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer** pour déployer l’instance du registre.

![Créer un registre de conteneurs dans le portail Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Pour visualiser l’état de chiffrement de votre registre dans le portail, accédez à votre registre. Sous **Paramètres**, sélectionnez **Chiffrement (préversion)** .

## <a name="enable-customer-managed-key---template"></a>Activer une clé gérée par le client – modèle

Vous pouvez également utiliser un modèle Resource Manager pour créer un registre et activer le chiffrement avec une clé gérée par le client. 

Le modèle suivant crée un registre de conteneurs et une identité managée attribuée par l’utilisateur. Copiez le contenu suivant dans un nouveau fichier et enregistrez-le à l’aide d’un nom de fichier tel que `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Suivez les étapes décrites dans les sections précédentes pour créer les ressources suivantes :

* Coffre de clés, identifié par un nom
* Clé de coffre de clés, identifiée par un ID de clé

Exécutez la commande [az group deployment create][az-group-deployment-create] suivante pour créer le registre à l’aide du fichier de modèle précédent. Lorsque cela est indiqué, fournissez un nouveau nom de registre et un nom d’identité managée, ainsi que le nom et l’ID de clé du coffre de clés que vous avez créé. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Afficher l’état de chiffrement

Pour afficher l’état du chiffrement du registre, exécutez la commande [az acr encryption show-status][az-acr-encryption-show-status] :

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Utiliser le registre

Une fois que vous avez activé un registre pour chiffrer les données à l’aide d’une clé gérée par le client, vous pouvez effectuer les mêmes opérations de registre que celles que vous effectuez dans un registre qui n’est pas chiffré avec une clé gérée par le client. Par exemple, vous pouvez vous authentifier à l’aide du registre et envoyer (push) des images Docker. Consultez les exemples de commandes dans [Envoyer (push) et extraire (pull) une image](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Rotation de clé

Vous pouvez permuter une clé gérée par le client dans Azure Key Vault en fonction de vos stratégies de conformité. Créez une clé, puis mettez à jour le registre pour chiffrer les données à l’aide de la nouvelle clé. Vous pouvez également effectuer ces étapes à l’aide d’Azure CLI ou dans le portail Azure.

Par exemple, exécutez la commande [az keyvault key create][az-keyvault-key-create] pour créer une clé :

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Ensuite, exécutez la commande [az acr encryption rotate-key][az-acr-encryption-rotate-key], en transmettant le nouvel ID de clé et l’ID du principal de l’identité managée que vous avez configurée précédemment :

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Révoquer la clé

Révoquez la clé de chiffrement gérée par le client en modifiant la stratégie d’accès sur le coffre de clés ou en supprimant la clé. Par exemple, utilisez la commande [az keyvault delete-policy][az-keyvault-delete-policy] pour modifier la stratégie d’accès de l’identité managée utilisée par votre registre. Par exemple :

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

La révocation de la clé bloque efficacement l’accès à toutes les données du registre, car le registre ne peut pas accéder à la clé de chiffrement. Si l’accès à la clé est activé ou si la clé supprimée est restaurée, votre registre choisira la clé afin de vous permettre d’accéder de nouveau aux données de registre chiffrées.

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur le [chiffrement au repos dans Azure](../security/fundamentals/encryption-atrest.md).
* Apprenez-en davantage sur les stratégies d’accès et sur la façon de [sécuriser l’accès à un coffre de clés](../key-vault/key-vault-secure-your-key-vault.md).
* Pour fournir des commentaires sur les clés gérées par le client pour Azure Container Registry, visitez le [site GitHub ACR](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
