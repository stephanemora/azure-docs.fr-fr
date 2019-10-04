---
title: Déplacer un réseau virtuel Azure vers une autre région Azure à l’aide du portail Azure.
description: Utilisez un modèle Azure Resource Manager pour déplacer un réseau virtuel Azure d’une région Azure vers une autre à l’aide du portail Azure.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: a09ce7b77dfcaa51e7c82f67a5d20000f3e22b61
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219994"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Déplacer un réseau virtuel Azure vers une autre région à l’aide du portail Azure

Il existe différents scénarios dans lesquels vous pourriez souhaiter déplacer vos machines virtuelles Azure existantes d’une région à une autre. Par exemple, vous souhaiterez peut-être créer un réseau virtuel avec la même configuration pour tester et vérifier la disponibilité de votre réseau virtuel existant. Vous pouvez également déplacer un réseau virtuel de production vers une autre région dans le cadre de la planification de la récupération d’urgence.

Vous pouvez utiliser un modèle Azure Resource Manager pour terminer la migration du réseau virtuel vers une autre région. Pour ce faire, exportez le réseau virtuel vers un modèle, modifiez les paramètres pour qu’ils correspondent à la région de destination, puis déployez le modèle dans la nouvelle région.  Pour plus d’informations sur Resource Manager et les modèles, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Prérequis

- Vérifiez que le réseau virtuel Azure se trouve dans la région Azure à partir de laquelle vous souhaitez effectuer le déplacement.

- Pour exporter un réseau virtuel et déployer un modèle pour créer un réseau virtuel dans une autre région, vous avez besoin du rôle Contributeur de réseaux ou supérieur.

- Les homologations de réseaux virtuels ne seront pas recréées et échoueront si elles sont toujours présentes dans le modèle.  Vous devez supprimer tous les homologues de réseaux virtuels avant d’exporter le modèle, puis rétablir les homologues après la migration du réseau virtuel.

- Identifiez la topologie du réseau source et toutes les ressources que vous utilisez actuellement. Cette disposition comprend notamment les équilibreurs de charge, les groupes de sécurité réseau et les adresses IP publiques.

- Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible utilisée. Contactez le support pour activer le quota requis.

- Assurez-vous que votre abonnement dispose de suffisamment de ressources pour prendre en charge l’ajout de réseaux virtuels pour ce processus.  Voir [Abonnement Azure et limites, quotas et contraintes de service](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Préparer et déplacer
Les étapes suivantes vous montrent comment préparer le réseau virtuel pour le déplacement à l’aide d’un modèle Resource Manager et déplacer le réseau virtuel vers la région cible à l’aide du portail.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Exporter le modèle et le déployer à partir du portail

1. Connectez-vous au [Portail Azure](https://portal.azure.com) > **Groupes de ressources**.
2. Localisez le groupe de ressources qui contient le réseau virtuel source, puis cliquez dessus.
3. Sélectionnez > **Paramètres** > **Exporter le modèle**.
4. Choisissez **Déployer** dans le panneau **Exporter le modèle**.
5. Cliquez sur **MODÈLE** > **Modifier les paramètres** pour ouvrir le fichier **parameters.json** dans l’éditeur en ligne.
6. Pour modifier le paramètre du nom du réseau virtuel, modifiez la propriété **value** sous **parameters** :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```
7. Remplacez la valeur nom du réseau virtuel source dans l’éditeur par le nom de votre choix pour le réseau virtuel cible. Veillez à placer le nom entre guillemets.

8.  Cliquez sur **Enregistrer** dans l’éditeur.

9.  Cliquez sur **MODÈLE** > **Modifier le modèle** pour ouvrir le fichier **template.json** dans l’éditeur en ligne.

10. Pour modifier la région cible dans laquelle le réseau virtuel sera déplacé, modifiez la propriété **location** sous **resources** dans l’éditeur en ligne :

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

11. Pour obtenir les codes d’emplacement des régions, consultez [Emplacements Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Le code d’une région est le nom de la région sans espace : **Central US** = **centralus**.

12. Vous pouvez également modifier d’autres paramètres dans le modèle si vous le souhaitez ; ces paramètres sont facultatifs en fonction de vos besoins :

    * **Espace d’adressage** : l’espace d’adressage du réseau virtuel peut être modifié avant l’enregistrement en modifiant la section **resources** > **addressSpace** et en modifiant la propriété **addressPrefixes** dans le fichier **template.json** :

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

    * **Sous-réseau** : le nom du sous-réseau et l’espace d’adressage du sous-réseau peuvent être modifiés ou ajoutés en modifiant la section **subnets** du fichier **template.json**. Le nom du sous-réseau peut être changé en modifiant la propriété **name**. Vous pouvez changer l’espace d’adressage du sous-réseau en modifiant la propriété **addressPrefix** dans le fichier **template.json** :

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

         Dans le fichier **template.json**, pour changer le préfixe d’adresse, vous devez le modifier à deux emplacements, la section ci-dessus et la section **type** indiquée ci-dessous.  Modifiez la propriété **addressPrefix** pour qu’elle corresponde à celle ci-dessus :

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

13. Cliquez sur **Enregistrer** dans l’éditeur en ligne.

14. Cliquez **DE BASE** > **Abonnement** pour choisir l’abonnement dans lequel le réseau virtuel cible sera déployé.

15. Cliquez **DE BASE** > **Groupe de ressources** pour choisir le groupe de ressources dans lequel le réseau virtuel cible sera déployé.  Vous pouvez cliquer sur **Créer** pour créer un nouveau groupe de ressources pour le réseau virtuel cible.  Vérifiez que le nom n’est pas identique à celui du groupe de ressources source du réseau virtuel existant.

16. Vérifiez que **DE BASE** > **Emplacement** est défini sur l’emplacement cible où vous souhaitez déployer le réseau virtuel.

17. Sous **PARAMÈTRES**, vérifiez que le nom correspond au nom que vous avez entré dans l’éditeur de paramètres ci-dessus.

18. Cochez la case sous **CONDITIONS GÉNÉRALES**.

19. Cliquez sur le bouton **Acheter** pour déployer le réseau virtuel cible.

## <a name="discard"></a>Abandonner

Si vous souhaitez abandonner le réseau virtuel cible, supprimez le groupe de ressources qui contient le réseau virtuel cible.  Pour ce faire, sélectionnez le groupe de ressources à partir de votre tableau de bord dans le portail, puis sélectionnez **Supprimer** en haut de la page vue d’ensemble.

## <a name="clean-up"></a>Nettoyer

Pour valider les modifications et terminer le déplacement du réseau virtuel, supprimez le réseau virtuel ou groupe de ressources source. Pour ce faire, sélectionnez le réseau virtuel ou le groupe de ressources à partir de votre tableau de bord dans le portail, puis sélectionnez **Supprimer** en haut de chaque page.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déplacé un réseau virtuel Azure d’une région à une autre et vous avez nettoyé les ressources source.  Pour en savoir plus sur le déplacement de ressources entre régions et la récupération d’urgence dans Azure, consultez :


- [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Déplacer des machines virtuelles Azure vers une autre région](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
