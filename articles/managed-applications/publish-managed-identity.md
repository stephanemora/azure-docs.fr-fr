---
title: Application gérée Azure avec identité gérée
description: Découvrez comment configurer une Application gérée avec une identité gérée. Identité de géré peut être utilisée pour déployer des Applications gérées lié à des ressources existantes, accorder les Applications gérées pour gérer les ressources Azure en dehors du groupe de ressources managé et fournir une identité opérationnelle des Applications gérées pour le journal d’activité et autres services dans Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003445"
---
# <a name="azure-managed-application-with-managed-identity"></a>Application gérée Azure avec identité gérée

> [!NOTE]
> Prise en charge des identités gérés pour les Applications gérées est actuellement en version préliminaire. Utilisez la version d’api 2018-09-01-preview pour utiliser msi.

Découvrez comment configurer une Application gérée à une identité gérée. Identité de géré peut être utilisée pour permettre au client d’accorder l’accès de l’Application gérée vers d’autres ressources existantes. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Pour plus d’informations sur les identités gérées dans Azure Active Directory (AAD), consultez [gérés d’identités pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

Deux types d’identité peuvent être accordés à votre application :

- Une **identité attribuée par le système** est liée à votre application et est supprimée si votre application est supprimée. Une application ne peut avoir qu’une seule identité attribuée par le système.
- Un **identité affectée à l’utilisateur** est une ressource Azure qui peut être affecté à votre application autonome. Une application peut avoir plusieurs identités attribuées par l’utilisateur.

## <a name="how-to-use-managed-identity"></a>L’utilisation de MSI

Identité administré permet de nombreux scénarios pour les Applications gérées. Quelques scénarios courants qui peuvent être résolus sont :

- Déploiement d’une Application gérée lié à des ressources Azure existantes. Un exemple consiste à déployer une machine virtuelle (VM) dans l’Application gérée qui est attaché à un [interface réseau existante](../virtual-network/virtual-network-network-interface-vm.md).
- Octroyant l’accès des applications gérées et le serveur de publication aux ressources Azure en dehors de la **groupe de ressources managé**.
- En fournissant une identité opérationnelle des Applications gérées pour le journal d’activité et d’autres services dans Azure.

## <a name="adding-managed-identity"></a>Ajout d’identités gérées

Création d’une Application gérée avec une identité gérée requiert une propriété supplémentaire à définir sur la ressource Azure. L’exemple suivant montre un exemple **identité** propriété :

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Il existe deux méthodes courantes pour créer une Application gérée avec **identité**: [CreateUIDefinition.json](./create-uidefinition-overview.md) et [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Créer des scénarios pour unique, CreateUIDefinition doit être utilisée pour activer l’identité gérés, car il fournit une expérience plus riche. Toutefois, lorsque vous traitez des avancées ou complexes automatisée des systèmes qui requièrent ou plusieurs déploiements de modèles, Application managée peuvent être utilisées.

### <a name="using-createuidefinition"></a>À l’aide de CreateUIDefinition

Une Application gérée peut être configurée avec MSI via la [CreateUIDefinition.json](./create-uidefinition-overview.md). Dans le [section sorties](./create-uidefinition-overview.md#outputs), la clé `managedIdentity` peut être utilisé pour remplacer la propriété d’identité du modèle d’Application gérés. Active la cloche exemple **attribué par le système** identité sur l’Application gérée. Objets d’identité plus complexes peuvent être formées à l’aide des éléments de CreateUIDefinition pour demander le consommateur entrées. Ces entrées peuvent être utilisées pour construire des Applications gérées avec **identité affectée à l’utilisateur**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Quand utiliser CreateUIDefinition pour l’identité gérée

Voici quelques recommandations à quel moment utiliser CreateUIDefinition pour l’activation de MSI installé sur les Applications gérées.

- La création de l’Application gérée traverse le portail Azure ou de la place de marché.
- L’identité gérée nécessite une entrée de consommateur complexes.
- L’identité gérée est nécessaire lors de la création de l’Application gérée.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

CreateUIDefinition de base qui permet l’identité SystemAssigned pour l’Application gérée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Une fonction CreateUIDefinition base qui prend un **identité affectée à l’utilisateur** ressource comme entrée et Active l’identité UserAssigned pour l’Application gérée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Le CreateUIDefinition.json ci-dessus génère une créer une expérience utilisateur qui a une zone de texte pour un consommateur à entrer le **identité affectée à l’utilisateur** ID de ressource Azure. L’expérience généré ressemblerait à :

![Exemple d’identité affectée à l’utilisateur CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Utilisation de modèles Azure Resource Manager

> [!NOTE]
> Modèles d’Application géré de la place de marché sont automatiquement générés pour les clients accédant via le portail Azure créer l’expérience.
> Pour ces scénarios, le `managedIdentity` clé de sortie sur la CreateUIDefinition doit être utilisée pour activé l’identité.

L’identité gérée peut également être activée par le biais de modèles Azure Resource Manager. Active la cloche exemple **attribué par le système** identité sur l’Application gérée. Objets d’identité plus complexes peuvent être formées à l’aide des paramètres de modèle Azure Resource Manager pour fournir les entrées. Ces entrées peuvent être utilisées pour construire des Applications gérées avec **identité affectée à l’utilisateur**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Quand utiliser des modèles Azure Resource Manager pour l’identité gérée

Voici quelques recommandations sur quand utiliser des modèles Azure Resource Manager pour l’activation de MSI installé sur les Applications gérées.

- Les Applications gérées peuvent être déployées par programme basé sur un modèle.
- Attributions de rôles personnalisés pour l’identité gérée sont nécessaires pour configurer l’Application gérée.
- L’Application managée n’a pas besoin le flux de création de portail et à la place de marché Azure.

#### <a name="systemassigned-template"></a>Modèle de SystemAssigned

Un modèle Azure Resource Manager de base qui déploie une Application gérée avec **attribué par le système** identité.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Modèle de UserAssigned

Un modèle Azure Resource Manager de base qui déploie une Application gérée avec un **identité affectée à l’utilisateur**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Accorder l’accès aux ressources Azure

Une fois qu’une identité accordées à une Application gérée, elle peut accéder aux Ressources azure existantes. Ce processus peut être effectué via l’interface de contrôle (IAM) d’accès dans le portail Azure. Le nom de l’Application gérée ou **identité affectée à l’utilisateur** peut être recherché pour ajouter une attribution de rôle.

![Ajouter une attribution de rôle pour une Application gérée](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Liaison des ressources Azure existants

> [!NOTE]
> Un **identité affectée à l’utilisateur** doit être [configuré](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) avant de déployer l’Application gérée. En outre, ressource liée déploiement d’Applications gérées est uniquement pris en charge pour le **place de marché** type.

Identité administré peut également servir à déployer une Application gérée qui requiert l’accès à des ressources existantes lors de son déploiement. Lors de l’approvisionnement de l’Application gérée par le client, **identités affectées par l’utilisateur** peuvent être ajoutés pour fournir des autorisations supplémentaires pour le **mainTemplate** déploiement.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Création de la fonction CreateUIDefinition avec une ressource liée

Lors de la liaison du déploiement de l’Application gérée à des ressources existantes, à la fois la ressource Azure existante et un **identité affectée à l’utilisateur** avec le rôle applicable attribution sur cette ressource doit être fournie.

 Un exemple de CreateUIDefinition qui requiert deux entrées : un ID de ressource d’interface réseau et un id de ressource d’identité affectée par l’utilisateur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Cette CreateUIDefinition.json génère une expérience utilisateur de création qui comporte deux champs. Le premier champ permet à l’utilisateur à entrer dans l’ID de ressource Azure pour la ressource liée au déploiement d’Application gérée. La deuxième concerne un consommateur à entrer le **identité affectée à l’utilisateur** ID de ressource Azure, qui a accès à la ressource Azure liée. L’expérience généré ressemblerait à :

![Exemple de CreateUIDefinition avec deux entrées : une interface réseau, ID de ressource et un ID de ressource d’identité affectée par l’utilisateur](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Création le modèle principal avec une ressource liée

En plus de la mise à jour la CreateUIDefinition, le modèle principal doit également être mis à jour pour accepter l’ID de ressource liée transmis Le modèle principal peut être mis à jour pour accepter la nouvelle sortie en ajoutant un nouveau paramètre. Dans la mesure où le `managedIdentity` sortie remplace la valeur sur le modèle d’Application managé généré, il n’est pas transmis au modèle principal et ne doit pas être inclus dans la section Paramètres.

Un exemple principal de modèle qui définit le profil de réseau à une interface réseau existante fournie par la fonction CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Utilisation de l’Application gérée avec une ressource liée

Une fois le package d’Application managée est créé, l’Application gérée peut être consommée via le portail Azure. Avant d’être utilisée, il existe plusieurs étapes préliminaires.

- Une instance de la ressource Azure liée obligatoire doit être créée.
- Le **identité affectée à l’utilisateur** doit être [créé et étant donné les attributions de rôles](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) à la ressource liée.
- Existant lié ID de ressource et le **identité affectée à l’utilisateur** ID sont fournies à la fonction CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Le jeton d’identité gérée de l’accès à

Le jeton de l’Application gérée est désormais accessible via le `listTokens` api du client du serveur de publication. Un exemple de demande pourrait ressembler à :

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

Un exemple de réponse peut ressembler :

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comment configurer une Application gérée avec un fournisseur personnalisé](./custom-providers-overview.md)