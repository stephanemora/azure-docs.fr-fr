---
title: Configurer un point de terminaison privé
titleSuffix: Azure Machine Learning
description: Utilisez un point de terminaison privé pour accéder en toute sécurité à votre espace de travail Azure Machine Learning à partir d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/10/2021
ms.openlocfilehash: 9a8e4351b88c1b9c4f166dff71fe906177870d9a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984695"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Configurer un point de terminaison privé pour un espace de travail Azure Machine Learning

Dans ce document, vous allez apprendre à configurer un point de terminaison privé pour votre espace de travail Azure Machine Learning. Pour plus d’informations sur la création d’un réseau virtuel pour Azure Machine Learning, consultez [Vue d’ensemble de l’isolement et la confidentialité des réseaux virtuels](how-to-network-security-overview.md)

Azure Private Link vous permet de vous connecter à votre espace de travail à l’aide d’un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées au sein de votre réseau virtuel. Vous pouvez alors limiter l’accès à votre espace de travail pour qu’il ne se fasse que sur les adresses IP privées. Un point de terminaison privé permet de réduire le risque d’exfiltration de données. Pour plus d’informations sur les points de terminaison privés, consultez l’article [Azure Private Link](../private-link/private-link-overview.md).

> [!WARNING]
> La sécurisation d’un espace de travail avec des points de terminaison privés ne suffit pas à garantir une sécurité de bout en bout. Vous devez sécuriser tous les composants individuels de votre solution. Par exemple, si vous utilisez un point de terminaison privé pour l’espace de travail, mais que votre compte de stockage Azure ne se trouve pas derrière le réseau virtuel, le trafic entre l’espace de travail et le stockage n’utilise pas le réseau virtuel pour la sécurité.
>
> Pour plus d’informations sur la sécurisation des ressources utilisées par Azure Machine Learning, consultez les articles suivants :
>
> * [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md)
> * [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
> * [Sécuriser les environnements de formation](how-to-secure-training-vnet.md)
> * [Sécuriser les environnements d’inférence](how-to-secure-inferencing-vnet.md)
> * [Utiliser Azure Machine Learning Studio dans un réseau virtuel](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

* Si vous prévoyez d’utiliser un espace de travail avec point de terminaison privé activé avec une clé gérée par le client, vous devez demander cette fonctionnalité à l’aide d’un ticket de support. Pour plus d’informations, consultez [Gérer et augmenter les quotas](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

* Vous devez disposer d’un réseau virtuel existant dans lequel créer le point de terminaison privé. Vous devez également [désactiver les stratégies réseau pour les points de terminaison privés](../private-link/disable-private-endpoint-network-policy.md) avant d’ajouter le point de terminaison privé.

## <a name="limitations"></a>Limites

* L’utilisation d’un espace de travail Azure Machine Learning avec point de terminaison privé n’est pas disponible dans les régions Azure Government.
* Si vous activez l’accès public à un espace de travail sécurisé avec point de terminaison privé et utilisez Azure Machine Learning Studio sur l’Internet public, certaines fonctionnalités telles que le concepteur peuvent ne pas parvenir à accéder à vos données. Ce problème se produit quand les données sont stockées sur un service sécurisé derrière le réseau virtuel. Par exemple, un compte de stockage Azure.
* Il se peut que vous rencontriez des problèmes en tentant d’accéder au point de terminaison privé de votre espace de travail si vous utilisez Mozilla Firefox. Un de ces problème peut être lié à DNS sur HTTPS dans Mozilla. Nous vous recommandons d’utiliser Microsoft Edge ou Google Chrome comme solution de contournement.
* L’utilisation d’un point de terminaison privé n’a pas d’effet sur le plan de contrôle Azure (opérations de gestion), comme la suppression de l’espace de travail ou la gestion des ressources de calcul. Par exemple, la création, la mise à jour ou la suppression d’une cible de calcul. Ces opérations sont effectuées sur l’Internet public comme d’habitude. Les opérations de plan de données telles que l’utilisation du studio Azure Machine Learning, des API (y compris les pipelines publiés) ou du SDK utilisent le point de terminaison privé.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Créer un espace de travail qui utilise un point de terminaison privé

Utilisez l’une des méthodes suivantes pour créer un espace de travail avec un point de terminaison privé. Chacune de ces méthodes __nécessite un réseau virtuel existant__ :

> [!TIP]
> Si vous souhaitez créer un espace de travail, un point de terminaison privé et un réseau virtuel en même temps, consultez [Utiliser un modèle Azure Resource Manager pour créer un espace de travail pour Azure Machine Learning](how-to-create-workspace-template.md).

# <a name="python"></a>[Python](#tab/python)

Fournie par le SDK Python Azure Machine Learning, la classe [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) peut être utilisée avec [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) pour créer un espace de travail avec un point de terminaison privé. Cette classe nécessite un réseau virtuel existant.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’[extension Azure CLI 1.0 pour Machine Learning](reference-azure-machine-learning-cli.md) fournit la commande [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create). Les paramètres suivants de cette commande permettent de créer un espace de travail avec un réseau privé mais requièrent un réseau virtuel existant :

* `--pe-name`: Nom du point de terminaison privé qui est créé.
* `--pe-auto-approval`: Si les connexions de point de terminaison privé à l’espace de travail doivent être approuvées automatiquement.
* `--pe-resource-group`: Groupe de ressources dans lequel créer le point de terminaison privé. Doit être le même groupe qui contient le réseau virtuel.
* `--pe-vnet-name`: Réseau virtuel existant dans lequel créer le point de terminaison privé.
* `--pe-subnet-name`: Nom du sous-réseau dans lequel créer le point de terminaison privé. La valeur par défaut est `default`.

Ces paramètres s’ajoutent à d’autres paramètres obligatoires pour la commande create. Par exemple, la commande suivante crée un nouvel espace de travail dans la région USA Ouest en utilisant un groupe de ressources et un réseau virtuel existants :

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

L’onglet __Mise en réseau__ d’Azure Machine Learning Studio vous permet de configurer un point de terminaison privé. Toutefois, il nécessite un réseau virtuel existant. Pour plus d’informations, consultez [Créer des espaces de travail dans le portail](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Ajouter un point de terminaison privé à un espace de travail

Utilisez l’une des méthodes suivantes pour ajouter un point de terminaison privé à un espace de travail existant :

> [!WARNING]
>
> Si des cibles de calcul existantes sont associées à cet espace de travail et qu’elles ne se trouvent pas derrière le même réseau virtuel dans lequel le point de terminaison privé est créé, elles ne fonctionneront pas.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Pour plus d’informations sur les classes et les méthodes utilisées dans cet exemple, consultez [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) et [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’[extension Azure CLI 1.0 pour Machine Learning](reference-azure-machine-learning-cli.md) fournit la commande [az ml workspace private-endpoint add](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_add).

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans l’espace de travail Azure Machine Learning dans le portail, sélectionnez __Connexions de point de terminaison privé__ puis __+ Point de terminaison privé__. Utilisez les champs pour créer un point de terminaison privé.

* Lorsque vous sélectionnez la __Région__, sélectionnez la même région que celle de votre réseau virtuel. 
* Lorsque vous sélectionnez le __Type de ressource__, utilisez __Microsoft.MachineLearningServices/workspaces__. 
* Définissez la __Ressource__ sur le nom de votre espace de travail.

Pour finir, sélectionnez __Créer__ pour créer le point de terminaison privé.

---

## <a name="remove-a-private-endpoint"></a>Supprimer un point de terminaison privé

Utilisez l’une des méthodes suivantes pour supprimer un point de terminaison privé d’un espace de travail :

# <a name="python"></a>[Python](#tab/python)

Utilisez [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) pour supprimer un point de terminaison privé.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’[extension Azure CLI 1.0 pour Machine Learning](reference-azure-machine-learning-cli.md) fournit la commande [az ml workspace private-endpoint delete](/cli/azure/ml/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete).

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans l’espace de travail Azure Machine Learning dans le portail, sélectionnez __Connexions de point de terminaison privé__, puis sélectionnez le point de terminaison que vous voulez supprimer. Pour finir, sélectionnez __Supprimer__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Utilisation d’un espace de travail sur un point de terminaison privé

Étant donné que les communications avec l’espace de travail sont autorisées uniquement à partir du réseau virtuel, tous les environnements de développement qui utilisent l’espace de travail doivent être faire partie du réseau virtuel. Par exemple, une machine virtuelle sur le réseau virtuel.

> [!IMPORTANT]
> Pour éviter toute interruption temporaire de la connectivité, Microsoft recommande de vider le cache DNS sur les ordinateurs qui se connectent à l’espace de travail après avoir activé un point de terminaison privé. 

Pour plus d’informations sur Machines virtuelles Microsoft Azure, consultez la [documentation relative à Machines Virtuelles](../virtual-machines/index.yml).

## <a name="enable-public-access"></a>Activer l’accès public

Dans certains cas, vous pouvez autoriser une personne à se connecter à votre espace de travail sécurisé sur un point de terminaison public, plutôt que par le biais du réseau virtuel. Après la configuration d’un espace de travail avec un point de terminaison privé, vous pouvez éventuellement activer l’accès public à l’espace de travail. Cela n’a pas pour effet de supprimer le point de terminaison privé. Toutes les communications assurées entre les composants derrière le réseau virtuel sont toujours sécurisées. Un accès public est permis uniquement à l’espace de travail, en plus de l’accès privé via le réseau virtuel.

> [!WARNING]
> Lors de la connexion au point de terminaison public, certaines caractéristiques du studio ne parviendront pas à accéder à vos données. Ce problème se produit quand les données sont stockées sur un service sécurisé derrière le réseau virtuel. Par exemple, un compte de stockage Azure. Notez également que les fonctionnalités d’instance de calcul Jupyter/JupyterLab/RStudio et les notebooks en cours d’exécution ne fonctionneront pas.

Pour activer l’accès public à un espace de travail avec point de terminaison privé, procédez comme suit :

# <a name="python"></a>[Python](#tab/python)

Utilisez [Workspace.delete_private_endpoint_connection](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) pour supprimer un point de terminaison privé.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

L’[extension Azure CLI 1.0 pour Machine Learning](reference-azure-machine-learning-cli.md) fournit la commande [az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update). Pour activer l’accès public à l’espace de travail, ajoutez le paramètre `--allow-public-access true`.

# <a name="portal"></a>[Portail](#tab/azure-portal)

Actuellement, il n’existe aucun moyen d’activer cette fonctionnalité à l’aide du portail.

---


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la sécurisation de votre espace de travail Azure Machine Learning, consultez l’article [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md).

* Si vous envisagez d’utiliser une solution DNS personnalisée dans votre réseau virtuel, consultez [Comment utiliser un espace de travail avec un serveur DNS personnalisé](how-to-custom-dns.md).
