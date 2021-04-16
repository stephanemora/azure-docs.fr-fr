---
title: Référencer un réseau virtuel existant dans un modèle de groupe identique Azure
description: Découvrez comment ajouter un réseau virtuel à un modèle de groupe de machines virtuelles identiques Azure existant
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076664"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Référencer un réseau virtuel existant dans un modèle de groupe identique Azure

Cet article explique comment modifier le [modèle de groupe identique de base ](virtual-machine-scale-sets-mvss-start.md) pour un déploiement dans un réseau virtuel existant au lieu d’en créer un.

## <a name="prerequisites"></a>Prérequis

Dans un article précédent, nous avions créé un [modèle de groupe identique de base](virtual-machine-scale-sets-mvss-start.md). Vous aurez besoin de ce modèle antérieur afin de pouvoir le modifier et créer un modèle qui déploie un groupe identique dans un réseau virtuel existant.

## <a name="identify-subnet"></a>Identifier le sous-réseau

Tout d’abord, ajoutez un paramètre `subnetId`. Cette chaîne est transférée dans la configuration du groupe identique, ce qui permet au groupe identique d’identifier le sous-réseau précréé pour y déployer des machines virtuelles. Cette chaîne doit être au format : 

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Par exemple, pour déployer le groupe identique sur un réseau virtuel existant avec le nom `myvnet`, le sous-réseau `mysubnet`, le groupe de ressources `myrg` et l’abonnement `00000000-0000-0000-0000-000000000000`, l’ID du sous-réseau serait : 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

## <a name="delete-extra-virtual-network-resource"></a>Supprimer une ressource de réseau virtuel inutile

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
## <a name="remove-dependency-clause"></a>Supprimer une clause de dépendance

Le réseau virtuel existe déjà avant que le modèle ne soit déployé, il est donc inutile de spécifier une clause `dependsOn` du groupe identique vers le réseau virtuel. Supprimez les lignes suivantes :

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

## <a name="pass-subnet-parameter"></a>Passer un paramètre de sous-réseau

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
