---
title: Référencer un réseau virtuel existant dans un modèle de groupe identique Azure | Microsoft Docs
description: Découvrez comment ajouter un réseau virtuel à un modèle de groupe de machines virtuelles identiques Azure existant
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868949"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Ajouter une référence à un réseau virtuel existant dans un modèle de groupe identique Azure

Cet article explique comment modifier le [modèle de base identiques](virtual-machine-scale-sets-mvss-start.md) à déployer dans un réseau virtuel existant au lieu de créer un nouveau.

## <a name="change-the-template-definition"></a>Modifier la définition du modèle

Dans un [article précédent](virtual-machine-scale-sets-mvss-start.md) nous avions créé un modèle de groupe identique de base. Maintenant nous utiliser ce modèle antérieures et modifiez-le pour créer un modèle qui déploie un groupe identique dans un réseau virtuel existant. 

Tout d’abord, ajoutez un paramètre `subnetId`. Cette chaîne est transférée dans la configuration du groupe identique, ce qui permet au groupe identique d’identifier le sous-réseau précréé pour y déployer des machines virtuelles. Cette chaîne doit être au format : `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

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

Enfin, transmettez le `subnetId` paramètre défini par l’utilisateur (au lieu d’utiliser `resourceId` pour obtenir l’ID d’un réseau virtuel dans le même déploiement, ce que la base identique viable modèle fait).

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
