---
title: Utiliser la maintenance planifiée pour votre cluster Azure Kubernetes Service (AKS) [préversion]
titleSuffix: Azure Kubernetes Service
description: Découvrez comment utiliser la maintenance planifiée dans Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f984eaa49539890b8ae4d5132f9a03594e2f19f8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073789"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Utiliser la maintenance planifiée pour planifier les fenêtres de maintenance de votre cluster Azure Kubernetes service (AKS) [préversion]

Votre cluster AKS fait l’objet d’une maintenance régulière et automatique. Par défaut, ce travail peut se produire à tout moment. La maintenance planifiée vous permet de planifier des fenêtres de maintenance hebdomadaire qui mettent à jour votre plan de contrôle ainsi que vos pods Kube-System sur une instance VMSS, et réduisent l’impact de la charge de travail. Une fois planifiée, toute la maintenance aura lieu pendant la fenêtre que vous avez sélectionnée. Vous pouvez planifier une ou plusieurs fenêtres hebdomadaires sur votre cluster en spécifiant un jour ou une plage horaire pour un jour spécifique. Les fenêtres de maintenance sont configurées à l’aide d’Azure CLI.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Limites

Lorsque vous utilisez la maintenance planifiée, les restrictions suivantes s’appliquent :

- AKS se réserve le droit d’arrêter ces fenêtres pour des opérations de maintenance non planifiées/réactives qui sont urgentes ou critiques.
- Actuellement, la réalisation des opérations de maintenance obéit au principe du *meilleur effort uniquement* et il n’est pas garanti qu’elle ait lieu dans une fenêtre spécifiée.
- Les mises à jour ne peuvent pas être bloquées pendant plus de sept jours.

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Vous devez également disposer de l’extension Azure CLI *aks-preview* version 0.5.4 ou ultérieure. Installez l’extension d’Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add]. Ou installez toutes les mises à jour disponibles à l’aide de la commande [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Autoriser la maintenance tous les lundis entre 1 h et 2 h

Pour ajouter une fenêtre de maintenance, vous pouvez utiliser la commande `az aks maintenanceconfiguration add`.

> [!IMPORTANT]
> Les fenêtres de maintenance planifiée sont spécifiées en temps universel coordonné (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

L’exemple de sortie suivant montre la fenêtre de maintenance de 1 h à 2 h tous les lundis.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Pour permettre la maintenance à tout moment de la journée, omettez le paramètre *start-hour*. Par exemple, la commande suivante définit la fenêtre de maintenance pour la journée entière tous les lundis :

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Ajouter une configuration de maintenance avec un fichier JSON

Vous pouvez également utiliser un fichier JSON pour créer une fenêtre de maintenance au lieu d’utiliser des paramètres. Créez un fichier `test.json` avec le contenu suivant :

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

Le fichier JSON ci-dessus spécifie les fenêtres de maintenance tous les mardis de 1 h à 3 h et tous les mercredis de 1 h à 2 h et de 6 h à 7 h. Il existe également une exception de *2021-05-26T03:00:00Z* à *2021-05-30T12:00:00Z* où la maintenance n’est pas autorisée même si la période chevauche une fenêtre de maintenance. La commande suivante ajoute les fenêtres de maintenance à partir de `test.json`.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Mettre à jour une fenêtre de maintenance existante

Pour mettre à jour une configuration de maintenance existante, utilisez la commande `az aks maintenanceconfiguration update`.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Répertorier toutes les fenêtres de maintenance dans un cluster existant

Pour voir toutes les fenêtres de configuration de maintenance actuelles dans votre cluster AKS, utilisez la commande `az aks maintenanceconfiguration list`.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

Dans la sortie ci-dessous, vous pouvez voir que deux fenêtres de maintenance sont configurées pour myAKSCluster. Une fenêtre est le lundi à 1 h et l’autre est le vendredi à 4 h.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Afficher une fenêtre spécifique de configuration de la maintenance dans un cluster AKS

Pour voir une fenêtre spécifique de configuration de la maintenance dans votre cluster AKS, utilisez la commande `az aks maintenanceconfiguration show`.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

L’exemple de sortie suivant affiche la fenêtre de maintenance *par défaut* :

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Supprimer une fenêtre donnée de configuration de la maintenance dans un cluster AKS existant

Pour supprimer une fenêtre donnée de configuration de la maintenance dans votre cluster AKS, utilisez la commande `az aks maintenanceconfiguration delete`.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="using-planned-maintenance-with-cluster-auto-upgrade"></a>Utilisation de la maintenance planifiée avec la mise à niveau automatique du cluster

La maintenance planifiée détecte si vous utilisez la mise à niveau automatique de cluster et planifiez vos mises à niveau automatiquement au cours de votre fenêtre de maintenance. Pour plus d’informations sur la mise à niveau automatique des clusters, consultez [Mettre à niveau un cluster Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec la mise à niveau de votre cluster AKS, consultez [Mettre à niveau un cluster AKS][aks-upgrade].


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
