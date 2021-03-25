---
title: Registre redondant interzone pour la haute disponibilité
description: Découvrez comment activer la redondance de zone dans Azure Container Registry. Créez un registre de conteneurs ou une réplication dans une zone de disponibilité Azure. La redondance de zone est une fonctionnalité du niveau de service Premium.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203859"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Activer la redondance de zone dans Azure Container Registry à des fins de résilience et de haute disponibilité

En plus de la [géoréplication](container-registry-geo-replication.md), qui réplique les données du registre dans une ou plusieurs régions Azure pour assurer la disponibilité et réduire la latence des opérations régionales, Azure Container Registry prend en charge la *redondance de zone* facultative. La [redondance de zone](../availability-zones/az-overview.md#availability-zones) assure la résilience et la haute disponibilité d’un registre ou d’une ressource de réplication (réplica) dans une région spécifique.

Cet article montre comment configurer un registre de conteneurs ou un réplica redondant interzone avec Azure CLI, le Portail Azure ou un modèle Azure Resource Manager. 

La redondance de zone est une fonctionnalité **d’évaluation** du niveau de service Premium de Container Registry. Pour plus d’informations sur les niveaux de service et les limites de registre, consultez [Niveaux de service d’Azure Container Registry](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* Actuellement pris en charge dans les régions suivantes : USA Est, USA Est 2, USA Ouest 2, Europe Nord, Europe Ouest, Japon Est.
* Les conversions de région en zones de disponibilité ne sont pas prises en charge actuellement. Pour activer la prise en charge des zones de disponibilité dans une région, le registre doit être créé dans la région souhaitée, avec la prise en charge des zones de disponibilité activée, ou une région répliquée doit être ajoutée avec la prise en charge des zones de disponibilité activée.
* La redondance de zone ne peut pas être désactivée dans une région.
* [ACR Tasks](container-registry-tasks-overview.md) ne prend pas encore en charge les zones de disponibilité.

## <a name="about-zone-redundancy"></a>À propos de la redondance de zone

Utilisez les [zones de disponibilité](../availability-zones/az-overview.md) Azure pour créer un registre de conteneurs Azure résilient et à haute disponibilité dans une région Azure. Par exemple, les organisations peuvent configurer un registre de conteneurs Azure redondant interzone avec d’autres [ressources Azure prises en charge](../availability-zones/az-region.md) pour répondre aux exigences en matière de résidence des données ou de conformité, tout en assurant une haute disponibilité dans une région.

Azure Container Registry prend également en charge la [géoréplication](container-registry-geo-replication.md), qui réplique le service dans plusieurs régions, en activant la redondance et la localité pour calculer les ressources dans d’autres emplacements. La combinaison de zones de disponibilité pour la redondance au sein d’une région et de la géoréplication dans plusieurs régions améliore la fiabilité et les performances d’un registre.

Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Pour garantir la résilience, un minimum de trois zones distinctes sont activées dans toutes les régions. Chaque zone possède un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Lorsqu’il est configuré pour la redondance de zone, un registre (ou un réplica du registre dans une autre région) est répliqué dans toutes les zones de disponibilité de la région, ce qui le maintient disponible en cas de défaillance du centre de données.

## <a name="create-a-zone-redundant-registry---cli"></a>Création d’un registre redondant interzone (CLI)

Pour activer la redondance de zone avec Azure CLI, vous devez disposer de la version 2.17.0 ou d’une version ultérieure d’Azure CLI, ou d’Azure Cloud Shell. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Si besoin, exécutez la commande [az group create](/cli/azure/group#az_group_create) afin de créer un groupe de ressources pour le registre.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Création d’un registre compatible avec les zones

Exécutez la commande [az acr create](/cli/azure/acr#az_acr_create) pour créer un registre redondant interzone dans le niveau de service Premium. Choisissez une région qui [prend en charge les zones de disponibilité](../availability-zones/az-region.md) pour Azure Container Registry. Dans l’exemple suivant, la redondance de zone est activée dans la région *eastus*. Pour connaître d’autres options de registre, consultez l’aide de la commande `az acr create`.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Dans la sortie de la commande, repérez la propriété `zoneRedundancy` du registre. Lorsqu’elle est activée, le registre est redondant interzone :

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Création d’une réplication redondante interzone

Exécutez la commande [az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) pour créer un réplica de registre redondant interzone dans une région qui [prend en charge les zones de disponibilité](../availability-zones/az-region.md) pour Azure Container Registry, par exemple *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Dans la sortie de la commande, repérez la propriété `zoneRedundancy` du réplica. Lorsqu’elle est activée, le réplica est redondant interzone :

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>Créer un registre redondant interzone (portail)

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
1. Sélectionnez **Créer une ressource** > **Conteneurs** > **Container Registry**.
1. Sous l’onglet **Informations de base**, sélectionnez ou créez un groupe de ressources, puis entrez un nom de registre unique. 
1. Dans **Emplacement**, sélectionnez une région qui prend en charge la redondance de zone pour Azure Container Registry, par exemple *USA Est*.
1. Dans **Référence (SKU)** , sélectionnez **Premium**.
1. Dans **Zones de disponibilité**, sélectionnez **Activé**.
1. Si vous le souhaitez, configurez des paramètres de registre supplémentaires, puis sélectionnez **Vérifier + créer**.
1. Sélectionnez **Créer** pour déployer l’instance du registre.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Activer la redondance de zone dans Portail Azure":::

Pour créer un réplica redondant interzone :

1. Accédez à votre registre de conteneurs de niveau Premium, puis sélectionnez **Réplications**.
1. Sur la carte qui s’affiche, sélectionnez un hexagone vert dans une région qui prend en charge la redondance de zone pour Azure Container Registry, par exemple **USA Ouest 2**. Sinon, sélectionnez **+ Ajouter**.
1. Dans la fenêtre **Créer une réplication**, confirmez **l’Emplacement**. Dans **Zones de disponibilité**, sélectionnez **Activé**, puis **Créer**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Activation de la réplication redondante interzone sur le Portail Azure":::

## <a name="create-a-zone-redundant-registry---template"></a>Créer un registre redondant interzone (modèle)

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Si nécessaire, exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources pour le registre dans une région qui [prend en charge les zones de disponibilité](../availability-zones/az-region.md) pour Azure Container Registry, par exemple *eastus*. Cette région est utilisée par le modèle pour définir l’emplacement du registre.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Déployer le modèle 

Vous pouvez utiliser le modèle Resource Manager suivant pour créer un registre géorépliqué et redondant interzone. Par défaut, le modèle active la redondance de zone dans le registre et un réplica régional. 

Copiez le contenu suivant dans un nouveau fichier et enregistrez-le à l’aide d’un nom de fichier tel que `registryZone.json`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Exécutez la commande [az deployment group create](/cli/azure/group/deployment#az_group_deployment_create) suivante pour créer le Registre à l’aide du fichier de modèle précédent. Si indiqué, fournissez :

* un nom de registre unique, ou déployez le modèle sans paramètres et il créera un nom unique pour vous
* un emplacement pour le réplica qui prend en charge les zones de disponibilité, par exemple *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Dans la sortie de la commande, penchez-vous sur la propriété `zoneRedundancy` pour le registre et le réplica. Lorsque elle est activée, chaque ressource est redondante interzone :

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [régions qui prennent en charge les zones de disponibilité](../availability-zones/az-region.md).
* En savoir plus sur la construction de la [fiabilité](/azure/architecture/framework/resiliency/overview) dans Azure.
