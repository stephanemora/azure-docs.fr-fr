---
title: Ajouter une identité managée à un type de nœud de cluster managé Service Fabric
description: Cet article explique comment ajouter une identité managée à un type de nœud de cluster géré par Service Fabric.
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 75f421ef750907a172ac3cf5c846b6b35f448521
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544922"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type"></a>Ajouter une identité managée à un type de nœud de cluster managé Service Fabric

Chaque type de nœud d’un cluster managé Service Fabric est adossé à un groupe de machines virtuelles identiques. Pour permettre l’utilisation des identités managées avec un type de nœud de cluster géré, une propriété `vmManagedIdentity` a été ajoutée aux définitions de type de nœud contenant une liste d’identités qui peuvent être utilisées, `userAssignedIdentities`. La fonctionnalité reflète la manière dont les identités managées peuvent être utilisées dans les clusters non gérés, par exemple l’utilisation d’une identité managée avec l’[extension de groupe de machines virtuelles identiques Azure Key Vault](../virtual-machines/extensions/key-vault-windows.md).

Pour obtenir un exemple de déploiement de cluster managé par Service Fabric qui utilise l’identité managée sur un type de nœud, consultez [ces modèles](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). L’exemple comprend deux modèles :

1. **Identité managée et attribution de rôle** : modèle permettant de créer l’identité managée et l’attribution de rôle afin de permettre à Service Fabric RP d’attribuer l’identité au groupe de machines virtuelles identiques du cluster managé. Celui-ci ne doit être déployé qu’une seule fois avant l’utilisation de l’identité managée sur la ressource de type de nœud.

2. **Cluster managé et type de nœud** : modèle pour le cluster managé Service Fabric et les ressources de type de nœud qui utilise l’identité managée créée précédemment.

> [!NOTE]
> Seules les identités affectées par l’utilisateur sont actuellement prises en charge pour cette fonctionnalité.

## <a name="prerequisites"></a>Prérequis

Avant de commencer :

* Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Si vous prévoyez d’utiliser PowerShell, [installez](/cli/azure/install-azure-cli) l’interface Azure CLI pour exécuter les commandes de référence de l’interface de ligne de commande.

## <a name="1-create-identity-and-role-assignment"></a>1. Créer une identité et une attribution de rôle

### <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Une identité managée affectée par l’utilisateur peut être définie dans la section des ressources d’un modèle Azure Resource Manager (ARM) à des fins de création lors du déploiement :

```JSON
{
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
  "name": "[parameters('userAssignedIdentityName')]",
  "apiVersion": "2018-11-30",
  "location": "[resourceGroup().location]"
}
```

ou créée via PowerShell :

```powershell
New-AzResourceGroup -Name <managedIdentityRGName> -Location <location>
New-AzUserAssignedIdentity -ResourceGroupName <managedIdentityRGName> -Name <userAssignedIdentityName>
```

### <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Ajouter une attribution de rôle au fournisseur de ressources Service Fabric

Ajoutez une attribution de rôle à l’identité managée avec l’application de fournisseur de ressources Service Fabric. Cette attribution permet au fournisseur de ressources Service Fabric d’attribuer l’identité créée à l’étape précédente au groupe de machines virtuelles identiques du cluster managé. Il s’agit d’une action unique.

Obtenir le principal du service pour une application de fournisseur de ressources Service Fabric :

```powershell
Login-AzAccount
Select-AzSubscription -SubscriptionId <SubId>
Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
```

> [!NOTE]
> Vérifiez que vous êtes bien dans le bon abonnement. L’ID du principal ne sera pas le même si l’abonnement se trouve dans un autre locataire.

```powershell
ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
ObjectType            : ServicePrincipal
DisplayName           : Azure Service Fabric Resource Provider
Id                    : 00000000-0000-0000-0000-000000000000
```

Utilisez l’**Id** de la sortie précédente comme **principalId** et l’ID de définition de rôle ci-dessous comme **roleDefinitionId**, le cas échéant, dans le modèle ou la commande PowerShell :

|Nom de la définition de rôle|ID de définition de rôle|
|----|-------------------------------------|
|Opérateur d’identités gérées|f1a07417-d97a-45cb-824c-7a7467783830|


Cette attribution de rôle peut être définie dans le modèle de la section Ressources à l’aide de l’ID de principal et de l’ID de définition de rôle :

```json
{
  "type": "Microsoft.Authorization/roleAssignments",
  "apiVersion": "2020-04-01-preview",
  "name": "[parameters('vmIdentityRoleNameGuid')]",
  "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
  "dependsOn": [
    "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "properties": {
    "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
    "principalId": "00000000-0000-0000-0000-000000000000"
  }
}
```
> [!NOTE]
> vmIdentityRoleNameGuid doit être un GUID valide. Si vous redéployez le même modèle, dont cette attribution de rôle, vérifiez que le GUID est identique à celui utilisé à l’origine, ou supprimez cette ressource car elle doit être créée une seule fois.

ou créée par le biais de PowerShell à l’aide de l’ID de principal et du nom de définition de rôle :

```powershell
New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

### <a name="deploy-managed-identity-and-role-assignment"></a>Déployer l’identité managée et l’attribution de rôle
Exécutez l’applet de commande New-AzResourceGroupDeployment pour créer l’identité managée et ajouter l’attribution de rôle :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <managedIdentityRGName> -TemplateFile ".\MangedIdentityAndSfrpRoleAssignment.json" -TemplateParameterFile ".\MangedIdentityAndSfrpRoleAssignment.Parameters.json" -Verbose
```

## <a name="2-assign-identity-to-the-node-type-resource"></a>2. Attribuer une identité à la ressource de type de nœud

### <a name="add-managed-identity-properties-to-node-type-definition"></a>Ajouter les propriétés d’identité managée à la définition de type de nœud

Enfin, ajoutez les propriétés `vmManagedIdentity` et `userAssignedIdentities` à la définition du type de nœud du cluster managé avec l’ID de ressource complet de l’identité créée à la première étape. Veillez à utiliser **2021-05-01** ou une version ultérieure pour `apiVersion`.

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "apiVersion": "2021-05-01",
  "properties": {
    "isPrimary": true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2_v2",
    "vmImagePublisher": "MicrosoftWindowsServer",
    "vmImageOffer": "WindowsServer",
    "vmImageSku": "2019-Datacenter",
    "vmImageVersion": "latest",
    "vmManagedIdentity": {
      "userAssignedIdentities": [
        "[parameters('userAssignedIdentityResourceId')]"
      ]
    }
  }
}
```

### <a name="deploy-the-node-type-resource-assigning-the-identity"></a>Déployer la ressource de type de nœud en lui attribuant une identité

Exécutez l’applet de commande New-AzResourceGroupDeployment pour déployer le modèle de cluster managé Service Fabric qui attribue l’identité managée à la ressource de type de nœud.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <sfmcRGName> -TemplateFile ".\SfmcVmMangedIdentity.json" -TemplateParameterFile ".\SfmcVmMangedIdentity.Parameters.json" -Verbose
```

Après le déploiement, l’identité managée créée a été ajoutée au groupe de machines virtuelles identiques du type de nœud désigné et peut être utilisée comme prévu, comme dans n’importe quel cluster non géré.

## <a name="troubleshooting"></a>Dépannage

Si vous n’ajoutez pas correctement une attribution de rôle, l’erreur suivante se produira lors du déploiement :

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Erreur de déploiement du portail Azure indiquant que le client possédant l’ID d’objet/application du SFRP n’est pas autorisé à effectuer une activité de gestion des identités":::

Dans ce cas, vérifiez que l’attribution de rôle a bien été effectuée pour le rôle « Opérateur d’identité managée ». L’attribution de rôle se trouve sur le portail Azure sous le contrôle d’accès de la ressource d’identité managée, comme indiqué ci-dessous.

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-portal.png" alt-text="Propriétés de l’attribution de rôle pour le fournisseur de ressources Service Fabric dans l’identité managée attribuée à l’utilisateur, telles qu’elles s’affichent dans le portail Azure":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer une application sur un cluster Service Fabric managé](./tutorial-managed-cluster-deploy-app.md)
