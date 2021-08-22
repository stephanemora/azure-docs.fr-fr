---
title: Compte Azure Automanage
description: Découvrez comment fonctionne un compte Automanage et comment en créer un.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: d3a88c81f60eaf08f64326f2c53f4d5dfa886fa1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525090"
---
# <a name="automanage-accounts"></a>Comptes Automanage

Le compte Automanage est l’identité utilisée par le service Automanage pour effectuer ses opérations automatisées.

Dans l’interface du portail Azure, lorsque vous activez le service Automanage sur vos machines virtuelles, le panneau **Activer la meilleure pratique de machine virtuelle Azure** contient une liste déroulante Avancées, qui vous permet d’attribuer ou de créer manuellement le Compte Automanage.

Le compte Automanage se verra attribuer à la fois les rôles **Contributeur** et **Contributeur de la stratégie de ressource** pour les abonnements contenant les ordinateurs que vous avez intégrés à Automanage. Vous pouvez utiliser le même compte Automanage sur les machines de plusieurs abonnements, ce qui accordera à ce compte Automanage des autorisations **Contributeur** et **Contributeur de la stratégie de ressources** sur tous les abonnements.

Si votre machine virtuelle est connectée à un espace de travail Log Analytics dans un autre abonnement, le compte Automanage se verra également attribuer les rôles **Contributeur** et **Contributeur de la stratégie de ressources** dans cet autre abonnement.

Si vous activez Automanage avec un nouveau compte Automanage, vous devez disposer des autorisations suivantes sur votre abonnement : Rôles **Propriétaire** ou **Contributeur**, ainsi que **Administrateur de l’accès utilisateur**.

Si vous activez Automanage avec un compte Automanage existant, vous devez disposer du rôle **Contributeur** sur le groupe de ressources contenant vos machines virtuelles.

> [!NOTE]
> Lorsque vous désactivez les meilleures pratiques d’Automanage, les autorisations du compte Automanage sur les abonnements associés sont conservées. Supprimez manuellement les autorisations en accédant à la page IAM de l’abonnement ou en supprimant le compte Automanage. Le compte Automanage ne peut pas être supprimé s’il gère toujours des ordinateurs.

## <a name="create-an-automanage-account"></a>Créer un compte Automanage
Vous pouvez créer un compte Automanage à l’aide du portail ou à l’aide d’un modèle ARM.

### <a name="portal"></a>Portail
1. Accédez au panneau **Automanage** dans le portail
1. Cliquez sur **Activer sur la machine existante**
1. Sous **Avancé**, cliquez sur « Créer un nouveau compte »
1. Renseignez les champs obligatoires, puis cliquez sur **Créer**

### <a name="arm-template"></a>Modèle ARM
La création d’un compte Automanage à l’aide d’un modèle ARM nécessite 2 étapes :
1. Créer le compte Automanage
1. Accordez des autorisations suffisantes au compte pour lui permettre d’effectuer des opérations pour vous
    1. Vous aurez besoin de l’ID d’objet du compte que vous avez créé pour cette étape.
        1. Les étapes permettant de trouver les détails du principal de service de votre compte (y compris l’ID d’objet) sont disponibles [ici](../active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal.md#view-the-service-principal).
    1. Une fois que vous avez trouvé le principal du service, copiez l’**ID de l'objet**. Enregistrez-le, car vous en aurez besoin pour déléguer les autorisations ci-dessous.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Créer un compte Automanage (ne pas lui accorder d’autorisations)
Pour créer un compte Automanage, enregistrez le modèle ARM suivant sous `azuredeploy.json` et exécutez la commande Azure CLI ci-dessous. Lorsque vous en avez terminé, passez au deuxième modèle ci-dessous pour déléguer les autorisations nécessaires au compte.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Accorder des autorisations au compte Automanage
Pour accorder des autorisations suffisantes au compte Automanage, vous devez effectuer les opérations suivantes :
1. Enregistrez le modèle ARM suivant comme `azuredeploy2.json` et exécutez l’interface de ligne de commande Azure suivante.
1. Lorsque vous y êtes invité, entrez l’ID d’objet du compte Automanage que vous avez créé et enregistré.

```azurecli-interactive
az deployment sub create --location <location> --template-file azuredeploy2.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "dateTime": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('contributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('resourcePolicyContributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur les services Automanage pour [Linux](./automanage-linux.md) et [Windows](./automanage-windows-server.md)