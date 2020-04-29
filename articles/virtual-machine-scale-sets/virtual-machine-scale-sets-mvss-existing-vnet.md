---
title: Référencer un réseau virtuel existant dans un modèle de groupe identique Azure
description: Découvrez comment ajouter un réseau virtuel à un modèle de groupe de machines virtuelles identiques Azure existant
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273662"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Ajouter une référence à un réseau virtuel existant dans un modèle de groupe identique Azure

Cet article explique comment modifier le [modèle de groupe identique de base ](virtual-machine-scale-sets-mvss-start.md) pour un déploiement dans un réseau virtuel existant au lieu d’en créer un.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Dans un [article précédent](virtual-machine-scale-sets-mvss-start.md), nous avions créé un modèle de groupe identique de base. Nous allons maintenant utiliser ce modèle antérieur et le modifier pour créer un modèle qui déploie un groupe identique dans un réseau virtuel existant. 

Tout d’abord, ajoutez un paramètre `subnetId`. Cette chaîne est transférée dans la configuration du groupe identique, ce qui permet au groupe identique d’identifier le sous-réseau précréé pour y déployer des machines virtuelles. Cette chaîne doit être au format : `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Par exemple, pour déployer le groupe identique sur un réseau virtuel existant avec le nom `myvnet`, le sous-réseau `mysubnet`, le groupe de ressources `myrg` et l’abonnement `00000000-0000-0000-0000-000000000000`, l’ID du sous-réseau serait : `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Ensuite, supprimez la ressource de réseau virtuel du tableau `resources`, car vous utilisez un réseau virtuel existant et n’avez pas besoin d’en déployer un nouveau.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Le réseau virtuel existe déjà avant que le modèle ne soit déployé, il est donc inutile de spécifier une clause dependsOn du groupe identique vers le réseau virtuel. Supprimez les lignes suivantes :

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Enfin, transmettez le paramètre `subnetId` défini par l’utilisateur (au lieu d’utiliser `resourceId` pour obtenir l’ID d’un réseau virtuel dans le même déploiement, ce que fait le modèle de groupe identique viable de base).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
