---
title: Désactiver l’exportation des artefacts
description: Définissez une propriété de registre pour empêcher l’exfiltration de données à partir d’un registre de conteneurs Azure Premium.
ms.topic: how-to
ms.date: 07/27/2021
ms.openlocfilehash: 8fa32197069376c71c035df0285852f4041efc64
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531386"
---
# <a name="disable-export-of-artifacts-from-an-azure-container-registry"></a>Désactiver l’exportation d’artefacts à partir d’un registre de conteneurs Azure 

Pour empêcher les utilisateurs de registre d’une organisation de transmettre des artefacts de manière malveillante ou accidentelle à l’extérieur d’un réseau virtuel, vous pouvez configurer la *stratégie d’exportation* du registre pour désactiver les exportations.

La stratégie d’exportation est une propriété introduite dans la version d’API **2021-06-01-preview** pour les registres de conteneur Premium. La propriété `exportPolicy`, quand son état est défini sur `disabled`, bloque l’exportation des artefacts à partir d’un registre dont l’accès réseau est restreint quand un utilisateur tente d’effectuer les opérations suivantes :

* [Importer](container-registry-import-images.md) les artefacts du registre dans un autre registre de conteneurs Azure
* Créer un [pipeline d’exportation](container-registry-transfer-images.md) de registre pour transférer des artefacts vers un autre registre de conteneurs

> [!NOTE]
> La désactivation de l’exportation d’artefacts n’empêche pas les utilisateurs autorisés d’accéder au registre au sein du réseau virtuel pour tirer (pull) des artefacts ou effectuer d’autres opérations de plan de données. Pour auditer cette utilisation, nous vous recommandons de configurer les paramètres de diagnostic pour [superviser](monitor-service.md) les opérations de registre. 

## <a name="prerequisites"></a>Configuration requise

* Un registre de conteneurs Premium configuré avec un [point de terminaison privé](container-registry-private-link.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="other-requirements-to-disable-exports"></a>Autres conditions requises pour désactiver les exportations

* **Désactiver l’accès réseau public** : pour désactiver l’exportation des artefacts, l’accès public au registre doit également être désactivé (la propriété `publicNetworkAccess` du registre doit être définie sur `disabled`). Vous pouvez désactiver l’accès réseau public au registre avant de désactiver l’exportation ou en même temps.

    En désactivant l’accès au point de terminaison public du registre, vous avez la certitude que les opérations de registre sont autorisées uniquement au sein du réseau virtuel. L’accès public au registre pour tirer (pull) des artefacts et effectuer d’autres opérations est interdit. 

*  **Supprimer des pipelines d’exportation** : avant de définir l’état `exportPolicy` du registre sur `disabled`, supprimez tous les pipelines d’exportation existants configurés sur le registre. Si un pipeline est configuré, vous ne pouvez pas modifier l’état `exportPolicy`.

## <a name="disable-exportpolicy-for-an-existing-registry"></a>Désactiver exportPolicy pour un registre existant

Quand vous créez un registre, l’état `exportPolicy` est défini sur `enabled` par défaut, ce qui permet d’exporter les artefacts. Vous pouvez définir l’état sur `disabled` à l’aide d’un modèle ARM ou de la commande `az resource update`.

### <a name="arm-template"></a>Modèle ARM 

Incluez le code JSON suivant pour modifier l’état `exportPolicy` et définir la propriété `publicNetworkAccess` sur `disabled`. Découvrez-en plus sur le [déploiement de ressources à l’aide de modèles ARM](../azure-resource-manager/templates/deploy-cli.md).

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
      "publicNetworkAccess": "disabled",
      "policies": {
        "exportPolicy": {
          "status": "disabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

Exécutez [az resource update](/cli/azure/resource/#az_resource_update) pour définir l’état `exportPolicy` dans un registre existant sur `disabled`. Remplacez les noms de votre registre et de votre groupe de ressources.

Comme indiqué dans cet exemple, quand vous désactivez la propriété `exportPolicy`, vous définissez également la propriété `publicNetworkAccess` sur `disabled`.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=disabled" \
    --set "properties.publicNetworkAccess=disabled"  
```

La sortie indique que l’état de la stratégie d’exportation est désactivé.

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "identity": null,
  "kind": null,
  "location": "centralus",
  "managedBy": null,
  "name": "myregistry",
  "plan": null,
  "properties": {
    [...]
    "policies": {
      "exportPolicy": {
        "status": "disabled"
      },
      "quarantinePolicy": {
        "status": "disabled"
      },
      "retentionPolicy": {
        "days": 7,
        "lastUpdatedTime": "2021-07-20T23:20:30.9985256+00:00",
        "status": "disabled"
      },
      "trustPolicy": {
        "status": "disabled",
        "type": "Notary"
      },
    "privateEndpointConnections": [],
    "provisioningState": "Succeeded",
    "publicNetworkAccess": "Disabled",
    "zoneRedundancy": "Disabled"
[...]
}
```

## <a name="enable-exportpolicy"></a>Activer exportPolicy 

Après avoir désactivé l’état `exportPolicy` dans un registre, vous pouvez le réactiver à tout moment à l’aide d’un modèle ARM ou de la commande `az resource update`.

### <a name="arm-template"></a>Modèle ARM 

Incluez le code JSON suivant pour définir l’état `exportPolicy` sur `enabled`. Découvrez-en plus sur le [déploiement de ressources à l’aide de modèles ARM](../azure-resource-manager/templates/deploy-cli.md).

```json
{
[...]
"resources": [
    {
    "type": "Microsoft.ContainerRegistry/registries",
    "apiVersion": "2021-06-01-preview",
    "name": "myregistry",
    [...]
    "properties": {
     "policies": {
        "exportPolicy": {
          "status": "enabled"
         }
      }
      }
    }
]
[...]
}
```

### <a name="azure-cli"></a>Azure CLI

Exécutez [az resource update](/cli/azure/resource/#az_resource_update) pour définir l’état `exportPolicy` sur `enabled`. Remplacez les noms de votre registre et de votre groupe de ressources.

```azurecli
az resource update --resource-group myResourceGroup \
    --name myregistry \
    --resource-type "Microsoft.ContainerRegistry/registries" \
    --api-version "2021-06-01-preview" \
    --set "properties.policies.exportPolicy.status=enabled"
```
 
## <a name="next-steps"></a>Étapes suivantes

* Découvrez les [autorisations et rôles Azure Container Registry](container-registry-roles.md).
* Si vous souhaitez empêcher la suppression accidentelle des artefacts de registre, consultez [Verrouiller les images conteneur](container-registry-image-lock.md).
* Découvrez les [stratégies Azure](container-registry-azure-policy.md) intégrées pour sécuriser votre registre de conteneurs Azure.
