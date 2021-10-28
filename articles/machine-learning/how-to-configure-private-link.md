---
title: Configurer un point de terminaison privé
titleSuffix: Azure Machine Learning
description: Utilisez un point de terminaison privé pour accéder en toute sécurité à votre espace de travail Azure Machine Learning à partir d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/07/2021
ms.openlocfilehash: cd08a4cb752ede0f958adde438cb516791f4ae76
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215132"
---
# <a name="configure-a-private-endpoint-for-an-azure-machine-learning-workspace"></a>Configurer un point de terminaison privé pour un espace de travail Azure Machine Learning

Dans ce document, vous allez apprendre à configurer un point de terminaison privé pour votre espace de travail Azure Machine Learning. Pour plus d’informations sur la création d’un réseau virtuel pour Azure Machine Learning, consultez [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md).

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

* Vous devez disposer d’un réseau virtuel existant dans lequel créer le point de terminaison privé. 
* [Désactivez les stratégies réseau pour les points de terminaison privés](../private-link/disable-private-endpoint-network-policy.md) avant d’ajouter le point de terminaison privé.

## <a name="limitations"></a>Limites

* Si vous activez l’accès public à un espace de travail sécurisé avec point de terminaison privé et utilisez Azure Machine Learning studio sur l’Internet public, certaines fonctionnalités telles que le concepteur peuvent ne pas parvenir à accéder à vos données. Ce problème se produit quand les données sont stockées sur un service sécurisé derrière le réseau virtuel. Par exemple, un compte de stockage Azure.
* Il se peut que vous rencontriez des problèmes en tentant d’accéder au point de terminaison privé de votre espace de travail si vous utilisez Mozilla Firefox. Un de ces problème peut être lié à DNS sur HTTPS dans Mozilla. Nous vous recommandons d’utiliser Microsoft Edge ou Google Chrome comme solution de contournement.
* L’utilisation d’un point de terminaison privé n’a pas d’effet sur le plan de contrôle Azure (opérations de gestion), comme la suppression de l’espace de travail ou la gestion des ressources de calcul. Par exemple, la création, la mise à jour ou la suppression d’une cible de calcul. Ces opérations sont effectuées sur l’Internet public comme d’habitude. Les opérations de plan de données telles que l’utilisation du studio Azure Machine Learning, des API (y compris les pipelines publiés) ou du SDK utilisent le point de terminaison privé.
* Lors de la création d’une instance de calcul ou d’un cluster de calcul dans un espace de travail avec un point de terminaison privé, l’instance de calcul et le cluster de calcul doivent se trouver dans la même région Azure que l’espace de travail.
* Lors de la création ou de l’attachement d’un cluster Azure Kubernetes Service à un espace de travail avec un point de terminaison privé, le cluster doit se trouver dans la même région que l’espace de travail.
* Lorsque vous utilisez un espace de travail avec plusieurs points de terminaison privés (préversion), l’un des points de terminaison privés doit se trouver dans le même réseau virtuel que les services de dépendance suivants :

    * compte de stockage Azure qui assure le stockage par défaut de l’espace de travail
    * Azure Key Vault pour l’espace de travail
    * Azure Container Registry pour l’espace de travail.

    Par exemple, un réseau virtuel (« services ») contiendrait un point de terminaison privé pour les services de dépendance et l’espace de travail. Cette configuration permet à l’espace de travail de communiquer avec les services. Un autre réseau virtuel (« clients ») pourrait contenir uniquement un point de terminaison privé pour l’espace de travail et être utilisé uniquement pour la communication entre les ordinateurs de développement client et l’espace de travail.

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

L’[extension Azure CLI 1.0 pour Machine Learning](reference-azure-machine-learning-cli.md) fournit la commande [az ml workspace private-endpoint add](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_add).

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

L’[extension Azure CLI 1.0 pour Machine Learning](reference-azure-machine-learning-cli.md) fournit la commande [az ml workspace private-endpoint delete](/cli/azure/ml(v1)/workspace/private-endpoint#az_ml_workspace_private_endpoint_delete).

# <a name="portal"></a>[Portail](#tab/azure-portal)

Dans l’espace de travail Azure Machine Learning dans le portail, sélectionnez __Connexions de point de terminaison privé__, puis sélectionnez le point de terminaison que vous voulez supprimer. Pour finir, sélectionnez __Supprimer__.

---

## <a name="securely-connect-to-your-workspace"></a>Vous connecter à votre espace de travail de manière sécurisée

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="multiple-private-endpoints-preview"></a>Plusieurs points de terminaison privés (préversion)

En fonctionnalité d’évaluation, Azure Machine Learning prend en charge plusieurs points de terminaison privés pour un espace de travail. Plusieurs points de terminaison privés sont souvent utilisés lorsque vous souhaitez conserver des environnements distincts. Scénarios possibles grâce à l’utilisation de plusieurs points de terminaison privés :

* Environnements de développement client dans un réseau virtuel distinct.
* Un cluster Azure Kubernetes Service (AKS) dans un réseau virtuel distinct.
* Autres services Azure dans un réseau virtuel distinct. Par exemple, Azure Synapse et Azure Data Factory peuvent utiliser un réseau virtuel managé par Microsoft. Dans les deux cas, un point de terminaison privé pour l’espace de travail peut être ajouté au réseau virtuel managé utilisé par ces services. Pour plus d’informations sur l’utilisation d’un réseau virtuel managé avec ces services, consultez les articles suivants :

    * [Points de terminaison privés managés Synapse](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)
    * [Réseau virtuel managé Azure Data Factory](../data-factory/managed-virtual-network-private-endpoint.md).

    > [!IMPORTANT]
    > [La protection contre l’exfiltration des données de Synapse](../synapse-analytics/security/workspace-data-exfiltration-protection.md) n’est pas prise en charge avec Azure Machine Learning.

> [!IMPORTANT]
> Chaque réseau virtuel qui contient un point de terminaison privé pour l’espace de travail doit également être en mesure d’accéder au compte de stockage Azure, à l’Azure Key Vault et à l’Azure Container Registry utilisés par l’espace de travail. Par exemple, vous pouvez créer un point de terminaison privé pour les services dans chaque réseau virtuel.

La procédure d’ajout de plusieurs points de terminaison est la même que celle décrite dans la section [Ajouter un point de terminaison privé à un espace de travail](#add-a-private-endpoint-to-a-workspace).

### <a name="scenario-isolated-clients"></a>Scénario : clients isolés

Si vous souhaitez isoler les clients de développement, afin qu’ils n’aient pas d’accès direct aux ressources de calcul utilisées par Azure Machine Learning, procédez comme suit :

> [!NOTE]
> Ces étapes partent du principe que vous disposez d’un espace de travail, d’un compte de stockage Azure, d’Azure Key Vault et d’Azure Container Registry. Chacun de ces services possède des points de terminaison privés dans un réseau virtuel existant.

1. Créez un autre réseau virtuel pour les clients. Ce réseau virtuel peut contenir des machines virtuelles Azure qui jouent le rôle de vos clients, ou il peut contenir une passerelle VPN utilisée par les clients locaux pour se connecter au réseau virtuel.
1. Ajoutez un nouveau point de terminaison privé pour le compte de stockage Azure, l’Azure Key Vault et l’Azure Container Registry utilisés par votre espace de travail. Ces points de terminaison privés doivent exister dans le réseau virtuel client.
1. Si vous disposez d’un espace de stockage supplémentaire utilisé par votre espace de travail, ajoutez un nouveau point de terminaison privé pour ce stockage. Le point de terminaison privé doit exister dans le réseau virtuel du client et l’intégration de la zone DNS privée doit y être activée.
1. Ajoutez un nouveau point de terminaison privé à votre espace de travail. Ce point de terminaison privé doit exister dans le réseau virtuel du client et l’intégration de la zone DNS privée doit y être activée.
1. Suivez les étapes de l’article [Utiliser Studio dans un réseau virtuel](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) pour permettre à Studio d’accéder aux comptes de stockage.

Le schéma suivant illustre cette configuration. Le réseau virtuel de __Charge de travail__ contient des calculs créés par l’espace de travail pour l’apprentissage et le déploiement. Le réseau virtuel __Client__ contient des clients ou des connexions ExpressRoute/VPN clientes. Les deux réseaux virtuels contiennent des points de terminaison privés pour l’espace de travail, un compte de stockage Azure, Azure Key Vault et Azure Container Registry.

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-client.png" alt-text="Diagramme du réseau virtuel isolé du client":::

### <a name="scenario-isolated-azure-kubernetes-service"></a>Scénario : service Azure Kubernetes isolé

Si vous souhaitez créer un Azure Kubernetes Service isolé utilisé par l’espace de travail, procédez comme suit :

> [!NOTE]
> Ces étapes partent du principe que vous disposez d’un espace de travail, d’un compte de stockage Azure, d’Azure Key Vault et d’Azure Container Registry. Chacun de ces services possède des points de terminaison privés dans un réseau virtuel existant.

1. Créez une instance Azure Kubernetes Service. Au cours de la création, AKS crée un réseau virtuel qui contient le cluster AKS.
1. Ajoutez un nouveau point de terminaison privé pour le compte de stockage Azure, l’Azure Key Vault et l’Azure Container Registry utilisés par votre espace de travail. Ces points de terminaison privés doivent exister dans le réseau virtuel client.
1. Si vous disposez d’un autre espace de stockage utilisé par votre espace de travail, ajoutez un nouveau point de terminaison privé pour ce stockage. Le point de terminaison privé doit exister dans le réseau virtuel du client et l’intégration de la zone DNS privée doit y être activée.
1. Ajoutez un nouveau point de terminaison privé à votre espace de travail. Ce point de terminaison privé doit exister dans le réseau virtuel du client et l’intégration de la zone DNS privée doit y être activée.
1. Attachez le cluster AKS à l’espace de travail Azure Machine Learning. Pour plus d’informations, consultez [Créer et attacher un cluster Azure Kubernetes Service](how-to-create-attach-kubernetes.md#attach-an-existing-aks-cluster).

:::image type="content" source="./media/how-to-configure-private-link/multiple-private-endpoint-workspace-aks.png" alt-text="Diagramme de réseau virtuel AKS isolé":::

## <a name="enable-public-access"></a>Activer l’accès public

Dans certains cas, vous pouvez autoriser une personne à se connecter à votre espace de travail sécurisé sur un point de terminaison public, plutôt que par le biais du réseau virtuel. Après la configuration d’un espace de travail avec un point de terminaison privé, vous pouvez éventuellement activer l’accès public à l’espace de travail. Cela n’a pas pour effet de supprimer le point de terminaison privé. Toutes les communications assurées entre les composants derrière le réseau virtuel sont toujours sécurisées. Un accès public est permis uniquement à l’espace de travail, en plus de l’accès privé via le réseau virtuel.

> [!WARNING]
> Lors de la connexion via le point de terminaison public :
> * __Certaines fonctionnalités de Studio ne peuvent pas accéder à vos données__. Ce problème se produit quand _les données sont stockées sur un service sécurisé derrière le réseau virtuel_. Par exemple, un compte de stockage Azure. 
> * L’utilisation de Jupyter, JupyterLab et RStudio sur une instance de calcul, y compris les notebooks en cours d’exécution, __n’est pas prise en charge__.

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