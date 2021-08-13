---
title: Accorder à une application l’accès à d’autres ressources Azure
description: Cet article explique comment accorder à votre application Service Fabric avec identité managée l’accès à d’autres ressources Azure prenant en charge l’authentification basée sur Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7f49a3f97862c3a141ea9376d0ffc9bf510d3e6f
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110782959"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Accorder à l’identité managée d’une application Service Fabric l’accès à des ressources Azure

Pour que l’application puisse utiliser son identité managée et accéder à d’autres ressources, les autorisations doivent être accordées à cette identité sur la ressource Azure protégée faisant l’objet d’un accès. L’octroi d’autorisations est généralement une action de gestion sur le « plan de contrôle » du service Azure propriétaire de la ressource protégée routée via Azure Resource Manager, ce qui permet d’appliquer toute vérification d’accès en fonction du rôle applicable.

La séquence exacte d’étapes dépend ensuite du type de ressource Azure qui fait l’objet de l’accès, ainsi que de la langue/du client utilisé pour accorder des autorisations. Le reste de cet article suppose qu’une identité affectée par l’utilisateur est attribuée à l’application et comprend plusieurs exemples typiques pour votre commodité, mais il ne s’agit en aucun cas d’une référence exhaustive pour cette rubrique ; consultez la documentation des services Azure respectifs pour obtenir des instructions à jour sur l’octroi d’autorisations.  

## <a name="granting-access-to-azure-storage"></a>Octroi de l’accès au Stockage Azure
Vous pouvez utiliser l’identité managée de l’application Service Fabric (affectée par l’utilisateur dans ce cas) pour récupérer les données à partir d’un objet blob de stockage Azure. Accordez à l’identité les autorisations nécessaires pour le compte de stockage en affectant le rôle de [lecteur de données blob de stockage](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) à l’identité gérée de l’application au niveau de l’étendue du *groupe de ressources* .

Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../role-based-access-control/role-assignments-portal.md).


## <a name="granting-access-to-azure-key-vault"></a>Octroi de l’accès à Azure Key Vault
De même que pour l’accès au stockage, vous pouvez tirer parti de l’identité managée d’une application Service Fabric pour accéder à un coffre de clés Azure. Les étapes permettant d’accorder l’accès dans le Portail Azure sont similaires à celles répertoriées ci-dessus et ne sont pas répétées ici. Reportez-vous à l’image ci-dessous pour connaître les différences.

![Stratégie d’accès au coffre de clés](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

L’exemple suivant illustre l’octroi d’accès à un coffre par le biais d’un déploiement de modèle. Ajoutez le ou les extraits de code ci-dessous comme une autre entrée sous l’élément `resources` du modèle. L’exemple montre l’octroi d’accès pour les types d’identité attribués par l’utilisateur et attribués par le système, respectivement. Choisissez celui qui convient.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
Et pour les identités managées attribuées par le système :
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Pour plus d’informations, consultez [Coffres - Mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Étapes suivantes
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par le système](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par l’utilisateur](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
