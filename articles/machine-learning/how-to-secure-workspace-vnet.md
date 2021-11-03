---
title: Sécuriser un espace de travail Azure Machine Learning à l’aide de réseaux virtuels
titleSuffix: Azure Machine Learning
description: Utilisez un réseau virtuel Azure isolé pour sécuriser votre espace de travail Azure Machine Learning et les ressources associées.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, security
ms.openlocfilehash: 12c405c31c1918e917c2db7dca85a74128724b26
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057739"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Sécuriser un espace de travail Azure Machine Learning à l’aide de réseaux virtuels

Dans cet article, vous allez apprendre comment sécuriser un espace de travail Azure Machine Learning et ses ressources associées dans un réseau virtuel.

> [!TIP]
> Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :
>
> * [Présentation du réseau virtuel](how-to-network-security-overview.md)
> * [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
> * [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
> * [Activer les fonctionnalités du studio](how-to-enable-studio-virtual-network.md)
> * [Utiliser le DNS personnalisé](how-to-custom-dns.md)
> * [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)
>
> Pour obtenir un tutoriel sur la création d’un espace de travail sécurisé, consultez [Tutoriel : Créer un espace de travail sécurisé](tutorial-create-secure-workspace.md).

Dans cet article, vous découvrirez comment activer les ressources d’espaces de travail suivantes dans un réseau virtuel :
> [!div class="checklist"]
> - Espace de travail Azure Machine Learning
> - Comptes de stockage Azure
> - Magasins de données et jeux de données Azure Machine Learning
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Prérequis

+ Lisez l’article [Vue d’ensemble de la sécurité réseau](how-to-network-security-overview.md) pour comprendre les scénarios courants des réseaux virtuels et l’architecture globale des réseaux virtuels.

+ Pour plus d’informations sur les meilleures pratiques, consultez [Meilleures pratiques en lien avec Azure Machine Learning pour la sécurité de l’entreprise](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security).

+ Un réseau virtuel et un sous-réseau existants à utiliser avec vos ressources de calcul.

    > [!TIP]
    > Si vous prévoyez d’utiliser Azure Container Instances dans le réseau virtuel (pour déployer des modèles), l’espace de travail et le réseau virtuel doivent se trouver dans le même groupe de ressources. Sinon, ils peuvent se trouver dans des groupes différents.

+ Pour déployer des ressources dans un réseau virtuel ou un sous-réseau, votre compte d’utilisateur doit disposer d’autorisations pour les actions suivantes dans le contrôle d’accès en fonction du rôle Azure (Azure RBAC) :

    - « Microsoft.Network/virtualNetworks/join/action » sur la ressource de réseau virtuel.
    - « Microsoft.Network/virtualNetworks/subnet/join/action » sur la ressource de sous-réseau virtuel.

    Pour plus d’informations sur Azure RBAC avec la mise en réseau, consultez [Rôles intégrés pour la mise en réseau](../role-based-access-control/built-in-roles.md#networking).

### <a name="azure-container-registry"></a>Azure Container Registry

* Vous devez disposer de la version Premium d’Azure Container Registry. Pour plus d'informations sur la mise à niveau, consultez [Changer de référence SKU](../container-registry/container-registry-skus.md#changing-tiers).

* Votre instance d'Azure Container Registry doit se trouver dans les mêmes réseau virtuel et sous-réseau que le compte de stockage et les cibles de calcul utilisés pour l'apprentissage ou l'inférence.

* Votre espace de travail Azure Machine Learning doit contenir un [cluster de calcul Azure Machine Learning](how-to-create-attach-compute-cluster.md).

## <a name="limitations"></a>Limites

### <a name="azure-storage-account"></a>Compte Stockage Azure

Si l'espace de travail Azure Machine Learning et le compte de stockage Azure utilisent tous deux un point de terminaison privé pour se connecter au VNet, ils doivent se trouver dans le même sous-réseau.

### <a name="azure-container-registry"></a>Azure Container Registry

Lorsque ACR se trouve derrière un réseau virtuel, Azure Machine Learning ne peut pas l'utiliser pour créer directement les images Docker. Le cluster de calcul est alors utilisé pour créer les images.

> [!IMPORTANT]
> Le cluster de calcul utilisé pour créer des images Docker doit être en mesure d’accéder aux référentiels de packages utilisés pour l’apprentissage et le déploiement de vos modèles. Il se peut que vous deviez ajouter des règles de sécurité réseau qui autorisent l’accès aux référentiels publics, [utiliser des packages Python privés](how-to-use-private-python-packages.md), ou utiliser des [images Docker personnalisées](how-to-train-with-custom-image.md) incluant déjà les packages.

> [!WARNING]
> Si votre instance Azure Container Registry utilise un point de terminaison privé pour communiquer avec le réseau virtuel, vous ne pouvez pas utiliser une identité managée avec un cluster de calcul Azure Machine Learning. Pour utiliser une identité gérée avec un cluster de calcul, utilisez un point de terminaison de service avec l’instance Azure Container Registry pour l’espace de travail.

## <a name="required-public-internet-access"></a>Accès Internet public obligatoire

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

Pour plus d’informations sur l’utilisation d’une solution de pare-feu, consultez [Utiliser un pare-feu avec Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## <a name="secure-the-workspace-with-private-endpoint"></a>Sécuriser l’espace de travail avec point de terminaison privé

Azure Private Link vous permet de vous connecter à votre espace de travail à l’aide d’un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées au sein de votre réseau virtuel. Vous pouvez alors limiter l’accès à votre espace de travail pour qu’il ne se fasse que sur les adresses IP privées. Un point de terminaison privé permet de réduire le risque d’exfiltration de données.

Pour plus d’informations sur la configuration d’un point de terminaison privé pour votre espace de travail, consultez [Guide pratique pour configurer un point de terminaison privé](how-to-configure-private-link.md).

> [!WARNING]
> La sécurisation d’un espace de travail avec des points de terminaison privés ne suffit pas à garantir une sécurité de bout en bout. Vous devez suivre les étapes décrites dans le reste de cet article et dans la série d’articles traitant des réseaux virtuels pour sécuriser des composants individuels de votre solution. Par exemple, si vous utilisez un point de terminaison privé pour l’espace de travail, mais que votre compte de stockage Azure ne se trouve pas derrière le réseau virtuel, le trafic entre l’espace de travail et le stockage n’utilise pas le réseau virtuel pour la sécurité.

## <a name="secure-azure-storage-accounts"></a>Sécuriser des comptes de Stockage Azure

Azure Machine Learning prend en charge les comptes de stockage configurés pour utiliser un point de terminaison privé ou un point de terminaison de service. 

# <a name="private-endpoint"></a>[Point de terminaison privé](#tab/pe)

1. Dans le portail Azure, sélectionnez le compte de stockage Azure.
1. Consultez les informations contenues dans [Utiliser des points de terminaison privés pour Stockage Azure](../storage/common/storage-private-endpoints.md#creating-a-private-endpoint) pour savoir comment ajouter des points de terminaison privés pour les sous-ressources de stockage suivantes :

    * **Objet blob**
    * **File**
    * **File d’attente** : nécessaire uniquement si vous envisagez d’utiliser [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) dans un pipeline Azure Machine Learning.
    * **Tableau** : nécessaire uniquement si vous envisagez d’utiliser [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) dans un pipeline Azure Machine Learning.

    :::image type="content" source="./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png" alt-text="Capture d’écran montrant la page de configuration du point de terminaison privé avec les options d’objets blob et de fichier":::

    > [!TIP]
    > Lors de la configuration d’un compte de stockage qui n’est **pas** le stockage par défaut, sélectionnez le type de **sous-ressource cible** correspondant au compte de stockage que vous souhaitez ajouter.

1. Après avoir créé les points de terminaison privés pour les sous-ressources, sélectionnez l’onglet __Pare-feu et réseaux virtuels__ sous __Mise en réseau__ pour le compte de stockage.
1. Choisissez __Réseaux sélectionnés__, puis, sous __Instances de ressource__, sélectionnez `Microsoft.MachineLearningServices/Workspace` comme __Type de ressource__. Sélectionnez votre espace de travail à l’aide du __nom de l’instance__. Pour plus d’informations, consultez [Accès approuvé basé sur l’identité managée affectée par le système](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity).

    > [!TIP]
    > Vous pouvez également sélectionner __Autoriser les services Azure de la liste des services approuvés à accéder à ce de stockage__ afin d’autoriser plus largement l’accès à partir de services de confiance. Pour plus d’informations, consultez [Configurer Pare-feu et réseaux virtuels dans Stockage Azure](../storage/common/storage-network-security.md#trusted-microsoft-services).

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-no-vnet.png" alt-text="Zone Mise en réseau sur la page Stockage Azure dans le portail Azure lors de l’utilisation d’un point de terminaison privé":::

1. Sélectionnez __Enregistrer__ pour enregistrer la configuration.

> [!TIP]
> Lorsque vous utilisez un point de terminaison privé, vous pouvez également désactiver l’accès public. Pour plus d’informations, consultez [Interdire l’accès en lecture public](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

# <a name="service-endpoint"></a>[Point de terminaison de service](#tab/se)

1. Dans le portail Azure, sélectionnez le compte de stockage Azure.

1. Dans la section __Sécurité + mise en réseau__ située à gauche de la page, sélectionnez __Mise en réseau__, puis l’onglet __Pare-feu et réseaux virtuels__.

1. Sélectionnez __Réseaux sélectionnés__. Sous __Réseaux virtuels__, sélectionnez le lien __Ajouter un réseau virtuel existant__, puis le réseau virtuel que votre espace de travail utilise.

    > [!IMPORTANT]
    > Le compte de stockage doit se trouver dans les mêmes réseau virtuel et sous-réseau que les instances de calcul ou clusters utilisés pour l'apprentissage ou l'inférence.

1. Sous __Instances de ressources__, sélectionnez `Microsoft.MachineLearningServices/Workspace` comme __type de ressource__, puis votre espace de travail à l’aide du __nom d’instance__. Pour plus d’informations, consultez [Accès approuvé basé sur l’identité managée affectée par le système](../storage/common/storage-network-security.md#trusted-access-based-on-system-assigned-managed-identity).

    > [!TIP]
    > Vous pouvez également sélectionner __Autoriser les services Azure de la liste des services approuvés à accéder à ce de stockage__ afin d’autoriser plus largement l’accès à partir de services de confiance. Pour plus d’informations, consultez [Configurer Pare-feu et réseaux virtuels dans Stockage Azure](../storage/common/storage-network-security.md#trusted-microsoft-services).

    :::image type="content" source="./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png" alt-text="Zone Mise en réseau sur la page Stockage Azure dans le portail Azure":::

1. Sélectionnez __Enregistrer__ pour enregistrer la configuration.

> [!TIP]
> Lorsque vous utilisez un point de terminaison de service, vous pouvez également désactiver l’accès public. Pour plus d’informations, consultez [Interdire l’accès en lecture public](../storage/blobs/anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

---

## <a name="secure-azure-key-vault"></a>Sécuriser un coffre de clés Azure Key Vault

Azure Machine Learning utilise une instance Key Vault associée pour stocker les informations d’identification suivantes :
* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données

Le coffre de clés Azure peut être configuré pour utiliser un point de terminaison privé ou un point de terminaison de service. Pour utiliser les fonctionnalités d’expérimentation Azure Machine Learning avec Azure Key Vault derrière un réseau virtuel, effectuez les étapes suivantes :

> [!TIP]
> Que vous utilisiez un point de terminaison privé ou un point de terminaison de service, le coffre de clés doit se trouver sur le même réseau que le point de terminaison privé de l’espace de travail.

# <a name="private-endpoint"></a>[Point de terminaison privé](#tab/pe)

Pour plus d’informations sur l’utilisation d’un point de terminaison privé avec Azure Key Vault, consultez [Intégrer Key Vault avec Azure Private Link](../key-vault/general/private-link-service.md#establish-a-private-link-connection-to-key-vault-using-the-azure-portal).


# <a name="service-endpoint"></a>[Point de terminaison de service](#tab/se)

1. Accédez au coffre de clés associé à l’espace de travail.

1. Sur la page __Key Vault__, dans le volet de gauche, sélectionnez __Mise en réseau__.

1. Sous l’onglet __Pare-feux et réseaux virtuels__, effectuez les actions suivantes :
    1. Sous __Autoriser l’accès depuis__, cliquez sur __Point de terminaison privé et réseaux sélectionnés__.
    1. Sous __Réseaux virtuels__, sélectionnez __Ajouter des réseaux virtuels existants__ pour ajouter le réseau virtuel où se trouve votre calcul d’expérimentation.
    1. Sous __Autoriser les services Microsoft approuvés pour contourner ce pare-feu__, sélectionnez __Oui__.

    :::image type="content" source="./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png" alt-text="Section Pare-feux et réseaux virtuels du volet Key Vault":::

Pour plus d’informations, consultez [Configurer les paramètres réseau Azure Key Vault](../key-vault/general/how-to-azure-key-vault-network-security.md).

---

## <a name="enable-azure-container-registry-acr"></a>Activer ACR (Azure Container Registry)

> [!TIP]
> Si vous n’avez pas utilisé un Azure Container Registry (ACR) existant lors de la création de l’espace de travail, il se peut qu’il n’y en ait pas. Par défaut, l’espace de travail ne crée pas d’instance ACR tant qu’il n’en a pas besoin. Pour forcer la création d’une instance, formez ou déployez un modèle à l’aide de votre espace de travail avant de suivre les étapes décrites dans cette section.

Azure Container Registry peut être configuré pour utiliser un point de terminaison privé. Procédez comme suit pour configurer votre espace de travail afin qu’il utilise ACR quand il se trouve dans le réseau virtuel :

1. Recherchez le nom de l'instance Azure Container Registry de votre espace de travail en utilisant l'une des méthodes suivantes :

    __Azure portal__

    Dans la section Présentation de votre espace de travail, la valeur __Registre__ permet d'accéder à l'instance d'Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Instance Azure Container Registry de l’espace de travail" border="true":::

    __Azure CLI__

    Si vous avez [installé l'extension Machine Learning pour Azure CLI](reference-azure-machine-learning-cli.md), vous pouvez utiliser la commande `az ml workspace show` pour afficher les informations de l'espace de travail.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Cette commande renvoie une valeur semblable `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. La dernière partie de la chaîne correspond au nom de l'instance Azure Container Registry de l'espace de travail.

1. Limitez l’accès à votre réseau virtuel à l’aide de la procédure décrite dans [Établir une connexion privée à une instance Azure Container Registry](../container-registry/container-registry-private-link.md). Lors de l'ajout du réseau virtuel, sélectionnez le réseau virtuel et le sous-réseau de vos ressources Azure Machine Learning.

1. Configurez l’ACR pour l’espace de travail afin d’[autoriser l’accès de services approuvés](../container-registry/allow-access-trusted-services.md).

1. Créer un cluster de calcul Azure Machine Learning Il est utilisé pour générer des images Docker quand ACR se trouve derrière un réseau virtuel. Pour plus d’informations, consultez [Créer un cluster de calcul](how-to-create-attach-compute-cluster.md).

1. Utilisez le SDK Python Azure Machine Learning pour configurer l’espace de travail afin de générer des images Docker avec l’instance de calcul. L’extrait de code suivant montre comment mettre à jour l’espace de travail pour définir un calcul de build. Remplacez `mycomputecluster` par le nom du cluster à utiliser :

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Votre compte de stockage, votre cluster de calcul et Azure Container Registry doivent tous se trouver dans le même sous-réseau du réseau virtuel.
    
    Pour plus d'informations, consultez les informations de référence disponibles sur la méthode [update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

> [!TIP]
> Quand ACR se trouve derrière un réseau virtuel, vous pouvez également [désactiver l’accès public](../container-registry/container-registry-access-selected-networks.md#disable-public-network-access) à celui-ci.

## <a name="datastores-and-datasets"></a>Magasins de données et jeux de données
Le tableau suivant liste les services pour lesquels vous devez ignorer la validation :

| Service | Omission nécessaire de la validation ? |
| ----- |:-----:|
| Stockage Blob Azure | Oui |
| Partage de fichiers Azure | Oui |
| Azure Data Lake Store Gen1 | Non |
| Azure Data Lake Store Gen2 | Non |
| Azure SQL Database | Oui |
| PostgreSQL | Oui |

> [!NOTE]
> Azure Data Lake Store Gen1 et Azure Data Lake Store Gen2 ignorent la validation par défaut ; vous n’avez donc rien à faire.

L’exemple de code suivant crée un magasin de données d’objets blob Azure et définit `skip_validation=True`.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Utiliser des jeux de données

La syntaxe permettant d’ignorer la validation du jeu de données est similaire pour les types de jeu de données suivants :
- Fichier délimité
- JSON 
- Parquet
- SQL
- Fichier

Le code suivant crée un jeu de données JSON et définit `validate=False`.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 
```

## <a name="securely-connect-to-your-workspace"></a>Vous connecter à votre espace de travail de manière sécurisée

[!INCLUDE [machine-learning-connect-secure-workspace](../../includes/machine-learning-connect-secure-workspace.md)]

## <a name="workspace-diagnostics"></a>Diagnostics de l’espace de travail

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## <a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série sur la sécurisation d’un workflow Azure Machine Learning. Consultez les autres articles de cette série :

* [Présentation du réseau virtuel](how-to-network-security-overview.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Activer les fonctionnalités du studio](how-to-enable-studio-virtual-network.md)
* [Utiliser le DNS personnalisé](how-to-custom-dns.md)
* [Utiliser un pare-feu](how-to-access-azureml-behind-firewall.md)