---
title: Déplacer un réseau virtuel Azure vers une autre région Azure à l’aide d’Azure PowerShell
description: Utilisez un modèle Azure Resource Manager pour déplacer un réseau virtuel Azure d’une région Azure vers une autre à l’aide d’Azure PowerShell.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077953"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Déplacer un réseau virtuel Azure vers une autre région à l’aide d’Azure PowerShell

Il existe différents scénarios dans lesquels vous pourriez souhaiter déplacer vos machines virtuelles Azure existantes d’une région à une autre. Par exemple, vous souhaiterez peut-être créer un réseau virtuel avec la même configuration pour tester et vérifier la disponibilité de votre réseau virtuel existant. Vous pouvez également déplacer un réseau virtuel de production vers une autre région dans le cadre de la planification de la récupération d’urgence.

Vous pouvez utiliser un modèle Azure Resource Manager pour terminer la migration du réseau virtuel vers une autre région. Pour ce faire, exportez le réseau virtuel vers un modèle, modifiez les paramètres pour qu’ils correspondent à la région de destination, puis déployez le modèle dans la nouvelle région.  Pour plus d’informations sur Resource Manager et les modèles, consultez [Exporter des groupes de ressources vers des modèles](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Prérequis

- Vérifiez que le réseau virtuel Azure se trouve dans la région Azure à partir de laquelle vous souhaitez effectuer le déplacement.

- Pour exporter un réseau virtuel et déployer un modèle pour créer un réseau virtuel dans une autre région, vous avez besoin du rôle Contributeur de réseaux ou supérieur.

- Les homologations de réseaux virtuels ne seront pas recréées et échoueront si elles sont toujours présentes dans le modèle.  Vous devez supprimer tous les homologues de réseaux virtuels avant d’exporter le modèle, puis rétablir les homologues après la migration du réseau virtuel.
    
- Identifiez la topologie du réseau source et toutes les ressources que vous utilisez actuellement. Cette disposition comprend notamment les équilibreurs de charge, les groupes de sécurité réseau et les adresses IP publiques.

- Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible utilisée. Contactez le support pour activer le quota requis.

- Assurez-vous que votre abonnement dispose de suffisamment de ressources pour prendre en charge l’ajout de réseaux virtuels pour ce processus.  Voir [Abonnement Azure et limites, quotas et contraintes de service](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Préparer et déplacer
Les étapes suivantes vous montrent comment préparer le réseau virtuel pour le déplacement à l’aide d’un modèle Resource Manager et déplacer le réseau virtuel vers la région cible à l’aide des commandes d’Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>Exporter le réseau virtuel et déployer le réseau virtuel cible avec PowerShell

1. Connectez-vous à votre abonnement Azure avec la commande [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) et suivez les instructions indiquées à l’écran :
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtenez l’ID de ressource du réseau virtuel que vous souhaitez déplacer vers la région cible, puis placez-le dans une variable à l'aide de [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) :

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportez le réseau virtuel source vers un fichier .json dans le répertoire où vous exécutez la commande [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) :
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Le fichier téléchargé est nommé d’après le groupe de ressources à partir duquel la ressource a été exportée.  Localisez le fichier qui a été exporté à partir de la commande nommée **\<resource-group-name>.json** et ouvrez-le dans l’éditeur de votre choix :
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Pour modifier le paramètre du nom du réseau virtuel, remplacez la valeur de la propriété **defaultValue** du nom du réseau virtuel source par le nom de votre réseau virtuel cible, en vous assurant que le nom est entre guillemets :
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Pour modifier la région cible dans laquelle le réseau virtuel sera déplacé, modifiez la propriété **location** sous Ressources :

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. Pour obtenir les codes d’emplacement des régions, vous pouvez utiliser l’applet de commande Azure PowerShell [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) en exécutant la commande suivante :

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Vous pouvez également modifier d’autres paramètres dans le fichier **\<resource-group-name>.json** si vous le souhaitez ; ces paramètres sont facultatifs en fonction de vos besoins :

    * **Espace d’adressage** : l’espace d’adressage du réseau virtuel peut être modifié avant l’enregistrement en modifiant la section **resources** > **addressSpace** et en modifiant la propriété **addressPrefixes** dans le fichier **\<resource-group-name>.json** :

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Sous-réseau** : le nom du sous-réseau et l’espace d’adressage du sous-réseau peuvent être modifiés ou ajoutés en modifiant la section **subnets** du fichier **\<resource-group-name>.json**. Le nom du sous-réseau peut être modifié en modifiant la propriété **name**. Vous pouvez modifier l’espace d’adressage du sous-réseau en modifiant la propriété **addressPrefix** dans le fichier **\<resource-group-name>.json** :

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         Dans le fichier **\<resource-group-name>.json**, pour modifier le préfixe d’adresse, vous devez le modifier à deux emplacements, la section ci-dessus et la section **type** indiquée ci-dessous.  Modifiez la propriété **addressPrefix** pour qu’elle corresponde à celle ci-dessus :

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  Enregistrez le fichier **\<resource-group-name>.json**.

10. Créez un groupe de ressources dans la région cible pour le réseau virtuel cible à déployer à l’aide de [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Déployez le fichier **\<resource-group-name>.json** modifié dans le groupe de ressources créé à l' étape précédente à l’aide de [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) :

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Pour vérifier que les ressources ont été créées dans la région cible, utilisez [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) et [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) :
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Abandonner 

Après le déploiement, si vous souhaitez recommencer ou ignorer le réseau virtuel dans la cible, supprimez le groupe de ressources qui a été créé dans la cible et le réseau virtuel déplacé sera supprimé.  Pour supprimer le groupe de ressources, utilisez [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) :

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Nettoyer

Pour valider les modifications et terminer le déplacement du réseau virtuel, supprimez le réseau virtuel ou groupe de ressources source avec [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) ou [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) :

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déplacé un réseau virtuel Azure d’une région à une autre et vous avez nettoyé les ressources source.  Pour en savoir plus sur le déplacement de ressources entre régions et la récupération d’urgence dans Azure, consultez :


- [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Déplacer des machines virtuelles Azure vers une autre région](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
