---
title: Isolement réseau et confidentialité
titleSuffix: Azure Machine Learning
description: Utilisez un réseau virtuel Azure isolé avec Azure Machine Learning pour sécuriser les travaux d’expérimentation/d’entraînement ainsi que les travaux d’inférence/de scoring.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: df819f5ff641af014750d6501c8b168e54917318
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420530"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>Isolement réseau pendant l’entraînement et l’inférence avec les réseaux virtuels privés
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir comment sécuriser vos cycles de vie de Machine Learning en isolant les travaux d’entraînement et d’inférence Azure Machine Learning au sein d’un réseau virtuel Azure. Pour entraîner et déployer des modèles, Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul, aussi appelées [cibles de calcul](concept-compute-target.md). Ces cibles peuvent être créées à l’intérieur d’un réseau virtuel. Par exemple, vous pouvez utiliser une capacité de calcul Azure Machine Learning pour entraîner un modèle et le déployer sur Azure Kubernetes Service (AKS). 

Un __réseau virtuel__ agit en tant que limite de sécurité, isolant vos ressources Azure de l’Internet public. Vous pouvez également joindre un réseau virtuel Azure à votre réseau local. En joignant les réseaux, cela vous permet d’entraîner vos modèles et d’accéder à vos modèles déployés à des fins d’inférence de façon sécurisée.

## <a name="prerequisites"></a>Prérequis

+ Un [espace de travail](how-to-manage-workspace.md) Azure Machine Learning.

+ Connaissance générale du [service de réseau virtuel Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) et de la [mise en réseau IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Un réseau virtuel et un sous-réseau préexistants à utiliser avec vos ressources de calcul.

## <a name="private-endpoints"></a>Instances Private Endpoint

Vous pouvez aussi [activer Azure Private Link](how-to-configure-private-link.md) pour vous connecter à votre espace de travail à l’aide d’un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées au sein de votre réseau virtuel. [Découvrez comment configurer ce point de terminaison privé.](how-to-configure-private-link.md)



> [!TIP]
> Vous pouvez combiner un réseau virtuel et le service Liaison privée pour protéger la communication entre votre espace de travail et d’autres ressources Azure. Toutefois, certaines combinaisons nécessitent un espace de travail Enterprise Edition. Utilisez le tableau suivant pour comprendre les scénarios qui nécessitent Enterprise Edition :
>
> | Scénario | Entreprise</br>edition | De base</br>edition |
> | ----- |:-----:|:-----:| 
> | Ni réseau virtuel ni Liaison privée | ✔ | ✔ |
> | Espace de travail sans Liaison privée. Autres ressources (sauf Azure Container Registry) dans un réseau virtuel | ✔ | ✔ |
> | Espace de travail sans Liaison privée. Autres ressources avec Liaison privée | ✔ | |
> | Espace de travail avec Liaison privée. Autres ressources (sauf Azure Container Registry) dans un réseau virtuel | ✔ | ✔ |
> | Espace de travail et toute autre ressource avec Liaison privée | ✔ | |
> | Espace de travail avec Liaison privée. Autres ressources sans Liaison privée ou réseau virtuel | ✔ | ✔ |
> | Azure Container Registry dans un réseau virtuel | ✔ | |
> | Clés gérées par le client pour l’espace de travail | ✔ | |
> 

> [!WARNING]
> 
> La préversion des instances de calcul Azure Machine Learning n’est pas prise en charge dans un espace de travail où le service Liaison privée est activé.

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

Si vos données sont stockées sur un réseau virtuel, vous devez utiliser une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) d’espace de travail pour accorder l’accès à vos données à Studio.

> [!IMPORTANT]
> Alors que Studio fonctionne principalement avec les données stockées dans un réseau virtuel, ce n’est __pas__ le cas des notebooks intégrés. Les notebooks intégrés ne prennent pas en charge l’utilisation du stockage qui se trouve dans un réseau virtuel. Cela étant, vous pouvez utiliser des notebooks Jupyter à partir d’une instance de calcul. Pour plus d’informations, consultez la section [Accéder aux données d’un notebook d’instance de capacité de calcul](#access-data-in-a-compute-instance-notebook).

Si vous ne parvenez pas à accorder l’accès à Studio, vous recevez cette erreur, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` et les opérations suivantes seront désactivées :

* Aperçu des données dans Studio
* Visualisation des données dans le concepteur
* Envoi d’une expérience AutoML
* Démarrage d’un projet d’étiquetage

Studio prend en charge la lecture de données à partir des types de magasins de données suivants sur un réseau virtuel :

* Objets blob Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="add-resources-to-the-virtual-network"></a>Ajouter des ressources au réseau virtuel 

Ajoutez votre espace de travail et votre compte de stockage au même réseau virtuel pour qu’ils puissent accéder l’un à l’autre.

1. Pour connecter votre espace de travail au réseau virtuel, [activez Azure Private Link](how-to-configure-private-link.md). Cette capacité est actuellement disponible en version préliminaire dans les régions USA Est, USA Ouest 2 et USA Centre Sud.

1. Pour connecter votre compte de stockage au réseau virtuel, [configurez les paramètres de pare-feu et de réseaux virtuels](#use-a-storage-account-for-your-workspace).

### <a name="configure-a-datastore-to-use-managed-identity"></a>Configurer un magasin de données pour utiliser l’identité managée

Après avoir ajouté votre compte de service de stockage et votre espace de travail au réseau virtuel, vous devez configurer les magasins de données afin qu’ils utilisent l’identité managée pour accéder à vos données. Ces étapes ajoutent l’identité managée de l’espace de travail en tant que __Lecteur__ au service de stockage à l’aide du contrôle d’accès en fonction du rôle Azure (RBAC). L’accès __Lecteur__ permet à l’espace de travail de récupérer les paramètres du pare-feu et de s’assurer que les données ne quittent pas le réseau virtuel.

1. Dans Studio, sélectionnez __Magasins de données__.

1. Pour créer un magasin de données, sélectionnez __+ Nouveau magasin de données__. Pour mettre à jour un magasin existant, sélectionnez-le et sélectionnez __Mettre à jour les informations d’identification__.

1. Dans les paramètres du magasin de stockage, sélectionnez __Oui__ pour __Autoriser le service Azure Machine Learning à accéder au stockage en utilisant l’identité managée de l’espace de travail__.

> [!NOTE]
> La prise en compte de ces modifications peut prendre jusqu’à 10 minutes.

### <a name="azure-blob-storage-blob-data-reader"></a>Lecteur des données Blob de Stockage Blob Azure

Pour __Stockage Blob Azure__, l’identité managée de l’espace de travail est également ajoutée en tant que [Lecteur des données Blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) afin qu’elle puisse lire les données à partir du stockage d’objets blob.


### <a name="azure-machine-learning-designer-default-datastore"></a>Magasin de données par défaut du concepteur Azure Machine Learning

Le concepteur utilise le compte de stockage attaché à votre espace de travail pour stocker la sortie par défaut. Toutefois, vous pouvez le spécifier afin qu’il stocke la sortie vers un magasin de données auquel vous avez accès. Si votre environnement utilise des réseaux virtuels, vous pouvez utiliser ces contrôles pour vous assurer que les données restent accessibles et en sécurité.

Pour définir un nouveau stockage par défaut pour un pipeline :

1. Dans un brouillon de pipeline, sélectionnez l’**icône d’engrenage des Paramètres** située près du titre de votre pipeline.
1. Sélectionnez **Sélectionner le magasin de données par défaut**.
1. Spécifiez un nouveau magasin de données.

Vous pouvez également écraser le magasin de données par défaut sur une base par module. Cela vous donne le contrôle sur l’emplacement de stockage pour chaque module individuel.

1. Sélectionnez le module dont vous souhaitez spécifier la sortie.
1. Développez la section **Paramètres de sortie**.
1. Sélectionnez **Remplacer les paramètres de sortie par défaut**.
1. Sélectionnez **Définir les paramètres de sortie**.
1. Spécifiez un nouveau magasin de données.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Contrôle d’accès Azure Data Lake Storage Gen2

Vous pouvez utiliser à la fois des listes de contrôle d’accès (ACL) de style POSIX et le contrôle RBAC pour contrôler l’accès aux données au sein d’un réseau virtuel.

Pour utiliser le contrôle RBAC, ajoutez l’identité managée de l’espace de travail au rôle [Lecteur des données Blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

Pour utiliser des listes de contrôle d’accès, vous pouvez accorder l’accès à l’identité managée de l’espace de travail comme tout autre principal de sécurité. Pour plus d’informations, consultez [Listes de contrôle d’accès sur les fichiers et répertoires](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="azure-data-lake-storage-gen1-access-control"></a>Contrôle d’accès Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 prend uniquement en charge les listes de contrôle d’accès de type POSIX. Vous pouvez accorder à l’identité managée de l’espace de travail l’accès aux ressources comme n’importe quel autre principal de sécurité. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).


### <a name="azure-sql-database-contained-user"></a>Utilisateur autonome Azure SQL Database

Pour accéder aux données stockées dans Azure SQL Database à l’aide d’une identité managée, vous devez créer un utilisateur autonome SQL mappé à l’identité managée. Pour plus d’informations sur la création d’un utilisateur à partir d’un fournisseur externe, consultez [Créer des utilisateurs à relation contenant-contenu mappés à des identités Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Après avoir créé un utilisateur autonome SQL, vous devez lui accorder des autorisations à l’aide de la [commande T-SQL GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="connect-to-the-studio"></a>Se connecter à Studio

Si vous accédez à Studio à partir d’une ressource au sein d’un réseau virtuel (par exemple une instance de calcul ou une machine virtuelle), vous devez autoriser le trafic sortant du réseau virtuel vers Studio. 

Par exemple, si vous utilisez des groupes de sécurité réseau pour limiter le trafic sortant, ajoutez une règle à une destination d’__étiquette de service__ __AzureFrontDoor.Frontend__.

## <a name="use-a-storage-account-for-your-workspace"></a>Utilisez un compte de stockage pour votre espace de travail

> [!IMPORTANT]
> Vous pouvez placer le _compte de stockage par défaut_ pour Azure Machine Learning ou les _comptes de stockage autres que ceux par défaut_ dans un réseau virtuel.
>
> Le compte de stockage par défaut est automatiquement configuré lorsque vous créez un espace de travail.
>
> Pour les comptes de stockage autres que ceux par défaut, le paramètre `storage_account` dans la fonction [`Workspace.create()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) vous permet de spécifier un compte de stockage personnalisé par ID de ressource Azure.

Pour utiliser le service Stockage Azure de l’espace de travail sur un réseau virtuel, effectuez les étapes suivantes :

1. Créez une ressource de calcul (par exemple, une instance de Capacité de calcul Machine Learning ou cluster) dans un réseau virtuel ou attachez une ressource de capacité de calcul à l’espace de travail (par exemple, un cluster HDInsight, une machine virtuelle ou un cluster Azure Kubernetes Service). La ressource de capacité de calcul peut servir à l’expérimentation ou au déploiement de modèle.

   Pour plus d’informations, consultez les sections [Utiliser la Capacité de calcul Machine Learning](#amlcompute), [Utiliser une machine virtuelle ou un cluster HDInsight](#vmorhdi) et [Utiliser Azure Kubernetes Service](#aksvnet) de cet article.

1. Dans le portail Azure, accédez au service de stockage que vous souhaitez utiliser dans votre espace de travail.

   [![Image du portail Azure montrant le stockage Azure lié à l’espace de travail Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Dans la page du compte de service de stockage, sélectionnez __Pare-feu et réseaux virtuels__.

   ![La zone « Pare-feu et réseaux virtuels » de la page Stockage Azure dans le portail Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Dans la page __Pare-feux et réseaux virtuels__, effectuez les actions suivantes :
    - Sélectionnez __Réseaux sélectionnés__.
    - Cliquez sur __Réseaux virtuel__ puis choisissez le lien __Ajouter un réseau virtuel existant__. Cette action ajoute le réseau virtuel où réside votre capacité de calcul (voir l’étape 1).

        > [!IMPORTANT]
        > Le compte de stockage doit se trouver dans les mêmes réseau virtuel et sous-réseau que les instances de calcul ou clusters utilisés pour l'apprentissage ou l'inférence.

    - Vérifiez que __Autoriser les services Microsoft approuvés à accéder à ce compte de stockage__ est coché.

    > [!IMPORTANT]
    > Lorsque vous travaillez avec le SDK Azure Machine Learning, votre environnement de développement doit être en mesure de se connecter au compte de Stockage Azure. Lorsque le compte de stockage se trouve dans un réseau virtuel, le pare-feu doit autoriser l’accès à partir de l’adresse IP de l’environnement de développement.
    >
    > Pour activer l’accès au compte de stockage, consultez les __Pare-feux et réseaux virtuels__ du compte de stockage à partir d’un *navigateur web sur le client de développement*. Utilisez ensuite la case à cocher __Ajouter votre adresse IP client__ pour ajouter l’adresse IP du client à la __PLAGE D’ADRESSES__. Vous pouvez également utiliser le champ __PLAGE D’ADRESSES__ pour entrer manuellement l’adresse IP de l’environnement de développement. Une fois que l’adresse IP du client a été ajoutée, elle peut accéder au compte de stockage à l’aide du SDK.

   [![Le volet « Pare-feu et réseaux virtuels » du Portail Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>Utiliser des magasins de données et des jeux de données

Cette section traite de l’utilisation des magasins de données et des jeux de données pour l’expérience du SDK. Pour plus d’informations sur l’expérience Studio, consultez la section [Machine Learning Studio](#machine-learning-studio).

Par défaut, Azure Machine Learning effectue des vérifications de la validité des données et des informations d’identification quand vous tentez d’accéder à des données à l’aide du SDK. Si vos données se trouvent derrière un réseau virtuel, Azure Machine Learning ne peut pas y accéder et ne parvient pas à les vérifier. Pour éviter cela, vous devez créer des magasins de données et des jeux de données qui ignorent la validation.

### <a name="use-a-datastore"></a>Utiliser un magasin de données

 Azure Data Lake Store Gen1 et Azure Data Lake Store Gen2 ignore la validation par défaut ; aucune action supplémentaire n’est donc nécessaire. Toutefois, pour les services suivants, vous pouvez utiliser une syntaxe similaire pour ignorer la validation du magasin de données :

- Stockage Blob Azure
- Partage de fichiers Azure
- PostgreSQL
- Azure SQL Database

L’exemple de code suivant crée un magasin de données d’objets blob Azure et définit `skip_validation=True`.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-a-dataset"></a>Utiliser un jeu de données

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


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Clusters et instances de calcul 

Pour utiliser une [__cible de calcul__ Azure Machine Learning gérée](concept-compute-target.md#azure-machine-learning-compute-managed) ou une [__instance de calcul__ Azure Machine Learning](concept-compute-instance.md) dans un réseau virtuel, le réseau doit réunir les conditions suivantes :

> [!div class="checklist"]
> * Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail Azure Machine Learning.
> * Le sous-réseau spécifié pour l’instance ou le cluster de calcul doit avoir suffisamment d’adresses IP non attribuées pour toutes les machines virtuelles ciblées. Si le sous-réseau n’a pas suffisamment d’adresses IP non attribuées, le cluster de calcul est alloué partiellement.
> * Vérifiez si vos stratégies ou verrous de sécurité sur l’abonnement ou le groupe de ressources du réseau virtuel restreignent les autorisations pour gérer le réseau virtuel. Si vous souhaitez sécuriser le réseau virtuel en limitant le trafic, laissez certains ports ouverts pour le service Capacité de calcul. Pour plus d’informations, voir la section [Ports requis](#mlcports).
> * Si vous vous apprêtez à placer plusieurs instances ou clusters de calcul sur un réseau virtuel, vous devrez peut-être demander une augmentation du quota pour une ou plusieurs de vos ressources.
> * Si le ou les comptes de stockage Azure pour l’espace de travail sont également sécurisés dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que l’instance ou le cluster de capacité de calcul Azure Machine Learning. 
> * Pour que la fonctionnalité d’instance de calcul Jupyter fonctionne, vérifiez que la communication avec le socket web n’est pas désactivée.

> [!TIP]
> La capacité de calcul ou le cluster Machine Learning alloue automatiquement des ressources réseau supplémentaires au __groupe de ressources qui contient le réseau virtuel__. Pour chaque instance ou cluster de calcul, le service alloue les ressources suivantes :
> 
> * Un seul groupe de sécurité réseau
> * Une seule adresse IP publique
> * Un seul équilibreur de charge
> 
> Dans le cas de clusters, ces ressources sont supprimées (et recréées) chaque fois que le cluster n’a plus aucun nœud. Toutefois, pour une instance, les ressources sont conservées jusqu’à ce que l’instance soit complètement supprimée (l’arrêt ne supprime pas les ressources). 
> Ces ressources sont limitées par les [quotas de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) de l’abonnement.


### <a name="required-ports"></a><a id="mlcports"></a> Ports requis

Si vous envisagez de sécuriser le réseau virtuel en restreignant le trafic réseau vers/à partir de l’Internet public, vous devez autoriser les communications entrantes à partir du service Azure Batch.

Le service Batch ajoute des groupes de sécurité réseau (NSG) au niveau des interfaces réseau (NIC) qui sont attachées aux machines virtuelles. Ces groupes de sécurité réseau configurent automatiquement des règles de trafic entrant et sortant pour autoriser le trafic suivant :

- Trafic TCP entrant sur les ports 29876 et 29877 à partir d’une __balise de service__ de __BatchNodeManagement__.

    ![Règle de trafic entrant qui utilise la balise de service BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (facultatif) Trafic TCP entrant sur le port 22 pour autoriser l’accès à distance. Utilisez ce port uniquement si vous souhaitez vous connecter à l’aide du protocole SSH sur l’adresse IP publique.

- Le trafic sortant sur n’importe quel port vers le réseau virtuel.

- Le trafic sortant sur n’importe quel port vers internet.

- Le trafic TCP entrant de l’instance de calcul sur le port 44224 à partir d’une __Balise de service__ __AzureMachineLearning__.

> [!IMPORTANT]
> Soyez prudent si vous modifiez ou ajoutez des règles de trafic entrant ou sortant dans des groupes de sécurité réseau configurés par Batch. Si un groupe de sécurité réseau bloque la communication vers les nœuds de calcul, le service Capacité de calcul définit l’état de ces nœuds sur « inutilisable ».
>
> Vous n’avez pas besoin de spécifier des groupes de sécurité réseau au niveau du sous-réseau, car le service Azure Batch configure ses propres groupes de sécurité réseau. Toutefois, si le sous-réseau qui contient la capacité de calcul Azure Machine Learning est associé à des groupes de sécurité réseau ou à un pare-feu, vous devez également autoriser le trafic listé plus haut.

La configuration de la règle de groupe de sécurité réseau dans le Portail Azure est illustrée dans les images suivantes :

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Les règles de groupe de sécurité réseau de trafic entrant pour Capacité de calcul Machine Learning" border="true":::



![Les règles des groupes de sécurité réseau de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Limitez la connectivité sortante à partir du réseau virtuel

Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, effectuez les étapes suivantes :

- Refusez la connexion internet sortante à l’aide des règles NSG.

- Pour une __instance de calcul__ ou un __cluster de calcul__, limitez le trafic sortant aux éléments suivants :
   - Stockage Azure, en utilisant la __balise de service__ __Storage.RegionName__. Où `{RegionName}` est le nom d’une région Azure.
   - Azure Container Registry, en utilisant la __balise de service__ __AzureContainerRegistry.RegionName__. Où `{RegionName}` est le nom d’une région Azure.
   - Azure Machine Learning, à l’aide de la __balise du service__ de __AzureMachineLearning__
   - Azure Resource Manager, à l’aide de la __balise de service__ de __AzureResourceManager__
   - Azure Active Directory, à l’aide de la __balise de service__ de __AzureActiveDirectory__

La configuration de la règle de groupe de sécurité réseau dans le Portail Azure est illustrée dans l’image suivante :

[![Les règles des groupes de sécurité réseau de trafic sortant pour Capacité de calcul](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Si vous prévoyez d’utiliser des images Docker par défaut fournies par Microsoft et que vous activez les dépendances gérées par l’utilisateur, vous devez également utiliser les __étiquettes de service__ suivantes :
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Cette configuration est nécessaire si les scripts d’apprentissage contiennent du code de ce type :
>
> __Entraînement RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Entraînement Estimator__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Tunneling forcé

Si vous utilisez le [tunneling forcé](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) avec le calcul Azure Machine Learning, vous devez autoriser la communication avec l’Internet public depuis le sous-réseau contenant la ressource de calcul. Cette communication permet de planifier les tâches et d’accéder au Stockage Azure.

Pour ce faire, il y a deux manières de procéder :

* Utilisez un [NAT de réseau virtuel](../virtual-network/nat-overview.md). Une passerelle NAT fournit une connectivité Internet sortante pour un ou plusieurs sous-réseaux dans votre réseau virtuel. Pour en savoir plus, consultez [Conception de réseaux virtuels avec des ressources de passerelle NAT](../virtual-network/nat-gateway-resource.md).

* Ajoutez des [itinéraires définis par l’utilisateur (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) au sous-réseau qui contient la ressource de calcul. Établissez un UDR pour chaque adresse IP utilisée par le service Azure Batch dans la région où se trouvent vos ressources. Ces UDR autorisent le service Batch à communiquer avec les nœuds de calcul pour la planification des tâches. Ajoutez également l’adresse IP du service Azure Machine Learning dans lequel les ressources existent, car cela est nécessaire pour l’accès aux instances de calcul. Pour obtenir la liste des adresses IP du service Batch et du service Azure Machine Learning, utilisez l’une des méthodes suivantes :

    * Téléchargez les [plages d’adresses IP Azure et les balises de service](https://www.microsoft.com/download/details.aspx?id=56519), et recherchez `BatchNodeManagement.<region>` et `AzureMachineLearning.<region>` dans le fichier, où `<region>` est votre région Azure.

    * Utilisez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pour télécharger les informations. L’exemple suivant télécharge les informations d’adresse IP et filtre les informations pour la région USA Est 2 :

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```
    
    Lorsque vous ajoutez les UDR, définissez l’itinéraire pour chaque préfixe d’adresse IP Batch connexe et définissez __Type de tronçon suivant__ sur __Internet__. L’illustration suivante propose un exemple de cet UDR dans le portail Azure :

    ![Exemple de UDR pour un préfixe d’adresse](./media/how-to-enable-virtual-network/user-defined-route.png)

    Outre les UDR que vous définissez, le trafic sortant vers Stockage Azure doit être autorisé via votre appliance de réseau local. Plus précisément, les URL pour ce trafic présentent les formes suivantes : `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, et `<account>.blob.core.windows.net`. 

    Pour plus d’informations, consultez l’article [Créer un pool Azure Batch dans un réseau virtuel](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).


### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Créer un cluster de calcul dans un réseau virtuel

Pour créer un cluster Capacité de calcul Machine Learning, effectuez les étapes suivantes :

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/), puis sélectionnez votre abonnement et votre espace de travail.

1. Sélectionnez __Compute__ à gauche.

1. Sélectionnez __Clusters d'entraînement__ à partir du centre, puis sélectionnez __+__ .

1. Dans la boîte de dialogue __Nouveau cluster d'entraînement__, développez la section __Paramètres avancés__.

1. Pour configurer cette ressource de calcul afin d'utiliser un réseau virtuel, effectuez les actions suivantes dans la section __Configurer le réseau virtuel__ :

    1. Dans la liste déroulante __Groupe de ressources__, sélectionnez le groupe de ressources qui contient le réseau virtuel.
    1. Dans la liste déroulante __Réseau virtuel__, sélectionnez le réseau virtuel qui contient le sous-réseau.
    1. Dans la liste déroulante __Sous-réseau__, sélectionnez le sous-réseau à utiliser.

   ![Les paramètres de réseau virtuel Capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Vous pouvez également créer un cluster Capacité de calcul Machine Learning à l’aide du SDK Azure Machine Learning. Le code suivant crée un cluster Capacité de calcul dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Une fois le processus de création terminé, vous pouvez entraîner votre modèle en utilisant le cluster dans le cadre d’une expérience. Pour plus d’informations, consultez [Sélectionner et utiliser une cible de calcul pour l’entraînement](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Accéder aux données d’un notebook d’instance de capacité de calcul

Si vous utilisez des notebooks sur une instance de capacité de calcul Azure, vous devez vérifier que votre notebook s’exécute sur une ressource de calcul derrière le même réseau virtuel et le même sous-réseau que vos données. 

Vous devez configurer votre instance de capacité de calcul pour qu’elle se trouve sur le même réseau virtuel lors de la création sous **Paramètres avancés** > **Configurer le réseau virtuel**. Vous ne pouvez pas ajouter une instance de capacité de calcul existante à un réseau virtuel.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pour ajouter Azure Kubernetes Service (AKS) dans un réseau virtuel de votre espace de travail, effectuez les étapes suivantes :

> [!IMPORTANT]
> Avant de commencer la procédure suivante, suivez les conditions préalables de la section [Configurer la mise en réseau avancée dans AKS (Azure Kubernetes Service)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) pour savoir comment planifier l’adressage IP de votre cluster.
>
> L’instance AKS et le réseau virtuel Azure doivent être dans la même région. Si vous sécurisez le ou les comptes de stockage Azure utilisés par l’espace de travail dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que l’instance AKS.

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com/), puis sélectionnez votre abonnement et votre espace de travail.

1. Sélectionnez __Compute__ à gauche.

1. Sélectionnez __Clusters d'inférence__ à partir du centre, puis sélectionnez __+__ .

1. Dans la boîte de dialogue __Nouveau cluster d'inférence__, sélectionnez __Avancé__ sous __Configuration réseau__.

1. Pour configurer cette ressource de calcul afin d'utiliser un réseau virtuel, procédez comme suit :

    1. Dans la liste déroulante __Groupe de ressources__, sélectionnez le groupe de ressources qui contient le réseau virtuel.
    1. Dans la liste déroulante __Réseau virtuel__, sélectionnez le réseau virtuel qui contient le sous-réseau.
    1. Dans la liste déroulante __Sous-réseau__, sélectionnez le sous-réseau.
    1. Dans la __plage d’adresses de service Kubernetes__, entrez la plage d’adresses du service Kubernetes. Cette plage d’adresses utilise une plage d’adresses IP de notation CIDR (Classless Inter-Domain Routing) pour définir les adresses IP disponibles pour le cluster. Elle ne doit empiéter sur aucune plage d’adresses IP de sous-réseau (par exemple, 10.0.0.0/16).
    1. Dans la zone __plage d’adresses IP du service DNS Kubernetes__, entrez l’adresse IP du service DNS Kubernetes. Cette adresse IP est affectée au service DNS Kubernetes. Elle doit se situer dans la plage d’adresses du service Kubernetes (par exemple, 10.0.0.10).
    1. Dans la zone __adresse du pont Docker__, entrez l’adresse du pont Docker. Cette adresse IP est affectée au pont Docker. Elle ne doit appartenir à aucune plage d’adresses IP de sous-réseau, ni à la plage d’adresses du service Kubernetes (par exemple, 172.17.0.1/16).

   ![Azure Machine Learning : Paramètres de réseau virtuel Capacité de calcul Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Vérifiez que le groupe de sécurité réseau qui contrôle le réseau virtuel dispose d’une règle de sécurité du trafic entrant activée pour le point de terminaison de notation, de sorte qu’elle puisse être appelée en dehors du réseau virtuel.
   > [!IMPORTANT]
   > Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Règle de sécurité de trafic entrant](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Vous pouvez également utiliser le SDK Azure Machine Learning pour ajouter Azure Kubernetes Service dans un réseau virtuel. Si vous avez déjà un cluster AKS dans un réseau virtuel, attachez-le à l’espace de travail comme décrit dans [Comment déployer dans AKS](how-to-deploy-and-where.md). Le code suivant crée une instance Azure Kubernetes Service dans le sous-réseau `default` d’un réseau virtuel nommé `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Une fois le processus de création terminé, vous pouvez effectuer une inférence, ou un scoring de modèle, sur un cluster AKS derrière un réseau virtuel. Pour plus d’informations, consultez [Guide pratique pour déployer sur AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Utiliser des adresses IP privées avec Azure Kubernetes Service

Par défaut, une adresse IP publique est affectée aux déploiements AKS. Quand vous utilisez AKS à l’intérieur d’un réseau virtuel, vous pouvez utiliser une adresse IP privée à la place. Les adresses IP privées sont accessibles uniquement à partir du réseau virtuel ou des réseaux joints.

Une adresse IP privée est activée en configurant AKS pour utiliser un _équilibreur de charge interne_. 

#### <a name="network-contributor-role"></a>Rôle du contributeur de réseau

> [!IMPORTANT]
> Si vous créez ou attachez un cluster AKS en fournissant un réseau virtuel que vous avez précédemment créé, vous devez accorder au principal de service ou à l’identité managée de votre cluster AKS le rôle de _contributeur de réseau_ sur le groupe de ressources contenant le réseau virtuel. Vous devez le faire avant d’essayer de modifier l’équilibreur de charge interne en adresse IP privée.
>
> Pour ajouter l’identité en tant que contributeur de réseau, procédez comme suit :

1. Pour rechercher le principal de service ou l’ID d’identité managée pour AKS, utilisez les commandes Azure CLI suivantes. Remplacez `<aks-cluster-name>` par le nom du cluster. Remplacez `<resource-group-name>` par le nom du groupe de ressources _contenant le cluster AKS_ :

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Si cette commande renvoie la valeur `msi`, utilisez la commande suivante pour identifier l’ID du principal de service pour l’identité managée :

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Pour rechercher l’ID du groupe de ressources contenant votre réseau virtuel, utilisez la commande suivante. Remplacez `<resource-group-name>` par le nom du groupe de ressources _contenant le réseau virtuel_ :

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Pour ajouter le principal de service ou l’identité managée en tant que contributeur de réseau, utilisez la commande suivante. Remplacez `<SP-or-managed-identity>` par l’ID renvoyé pour le principal de service ou l’identité managée. Remplacez `<resource-group-id>` par l’ID renvoyé pour le groupe de ressources contenant le réseau virtuel.

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Pour plus d’informations sur l’utilisation de l’équilibreur de charge interne avec AKS, consultez [Utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS)](/azure/aks/internal-lb).

#### <a name="enable-private-ip"></a>Activer l’adresse IP privée

> [!IMPORTANT]
> Vous ne pouvez pas activer une adresse IP privée lors de la création du cluster Azure Kubernetes Service. Elle doit être activée en tant que mise à jour d’un cluster existant.

L’extrait de code suivant montre comment __créer un nouveau cluster AKS__, puis le mettre à jour afin d’utiliser un équilibreur de charge interne/une adresse IP privée :

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

Le contenu du fichier `body.json` référencé par la commande est semblable au document JSON suivant :

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

Lorsque vous __attachez un cluster existant__ à votre espace de travail, vous devez attendre la fin de l’opération d’attachement pour configurer l’équilibreur de charge.

Pour plus d’informations sur l’attachement d’un cluster, consultez [Attacher un cluster AKS existant](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster).

Une fois le cluster existant attaché, vous pouvez le mettre à jour pour utiliser une adresse IP privée.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="use-azure-container-instances-aci"></a>Utiliser Azure Container Instances (ACI)

Les instances de conteneur Azure (ACI) sont créées dynamiquement lors du déploiement d’un modèle. Pour permettre à Azure Machine Learning de créer un réseau ACI à l’intérieur du réseau virtuel, vous devez activer la __délégation de sous-réseau__ pour le sous-réseau utilisé par le déploiement.

> [!WARNING]
> Lorsque vous utilisez Azure Container Instances dans un réseau virtuel, ce dernier doit se trouver dans le même groupe de ressources que votre espace de travail Azure Machine Learning.
>
> Lorsque vous utilisez Azure Container Instances à l’intérieur du réseau virtuel, l’instance Azure Container Registry (ACR) de votre espace de travail ne doit pas se trouver dans le réseau virtuel.

Pour utiliser ACI sur un réseau virtuel à votre espace de travail, effectuez les étapes suivantes :

1. Pour activer la délégation de sous-réseau sur votre réseau virtuel, utilisez les informations de l’article [Ajouter ou supprimer une délégation de sous-réseau](../virtual-network/manage-subnet-delegation.md). Vous pouvez activer la délégation lors de la création d’un réseau virtuel ou l’ajouter à un réseau existant.

    > [!IMPORTANT]
    > Lorsque vous activez la délégation, utilisez `Microsoft.ContainerInstance/containerGroups` comme valeur __Déléguer le sous-réseau à un service__.

2. Déployez le modèle à l’aide de [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), en utilisant les paramètres `vnet_name` et `subnet_name`. Appliquez ces paramètres au nom du réseau virtuel et au sous-réseau où vous avez activé la délégation.

## <a name="azure-firewall"></a>Pare-feu Azure

Pour obtenir des informations sur l’utilisation d’Azure Machine Learning avec le pare-feu Azure, consultez [Utiliser l’espace de travail Azure Machine Learning derrière le pare-feu Azure](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> Azure Container Registry (ACR) peut être placé dans un réseau virtuel, mais vous devez remplir les conditions préalables suivantes :
>
> * Vous devez disposer d'un espace de travail Azure Machine Learning édition Entreprise. Pour plus d'informations sur la mise à niveau, consultez [Mise à niveau vers l'édition Enterprise](how-to-manage-workspace.md#upgrade).
> * La région de votre espace de travail Azure Machine Learning doit être une [région accessible via un lien privé](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
> * Vous devez disposer de la version Premium d'Azure Container Registry. Pour plus d'informations sur la mise à niveau, consultez [Changer de référence SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Votre instance d'Azure Container Registry doit se trouver dans les mêmes réseau virtuel et sous-réseau que le compte de stockage et les cibles de calcul utilisés pour l'apprentissage ou l'inférence.
> * Votre espace de travail Azure Machine Learning doit contenir un [cluster de calcul Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute).
>
>     Lorsque ACR se trouve derrière un réseau virtuel, Azure Machine Learning ne peut pas l'utiliser pour créer directement les images Docker. Le cluster de calcul est alors utilisé pour créer les images.

1. Pour rechercher le nom de l'instance Azure Container Registry de votre espace de travail, utilisez l'une des méthodes suivantes :

    __Azure portal__

    Dans la section Présentation de votre espace de travail, la valeur __Registre__ permet d'accéder à l'instance d'Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Instance Azure Container Registry de l’espace de travail" border="true":::

    __Azure CLI__

    Si vous avez [installé l'extension Machine Learning pour Azure CLI](reference-azure-machine-learning-cli.md), vous pouvez utiliser la commande `az ml workspace show` pour afficher les informations de l'espace de travail.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Cette commande renvoie une valeur semblable `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. La dernière partie de la chaîne correspond au nom de l'instance Azure Container Registry de l'espace de travail.

1. Pour limiter l'accès à votre réseau virtuel, suivez les étapes décrites dans [Configurer l'accès réseau pour le registre](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Lors de l'ajout du réseau virtuel, sélectionnez le réseau virtuel et le sous-réseau de vos ressources Azure Machine Learning.

1. Utilisez le kit de développement logiciel (SDK) Python Azure Machine Learning pour configurer un cluster de calcul de manière à créer les images Docker. L'extrait de code suivant illustre comment procéder :

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Votre compte de stockage, votre cluster de calcul et Azure Container Registry doivent tous se trouver dans le même sous-réseau du réseau virtuel.
    
    Pour plus d'informations, consultez les informations de référence disponibles sur la méthode [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

1. Vous devez appliquer le modèle Azure Resource Manager suivant. Ce modèle permet à votre espace de travail de communiquer avec ACR.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>Instance de coffre de clés 

L’instance de coffre de clés associée à l’espace de travail est utilisée par Azure Machine Learning pour stocker les informations d’identification suivantes :
* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données

Pour utiliser les fonctionnalités d’expérimentation Azure Machine Learning avec Azure Key Vault derrière un réseau virtuel, effectuez les étapes suivantes :

1. Accédez au coffre de clés associé à l’espace de travail.

   [![Le coffre de clés associé à l’espace de travail Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Sur la page __Key Vault__, sélectionnez le volet de gauche __Pare-feux et réseaux virtuels__.

   ![La section « Pare-feux et réseaux virtuels » dans le volet Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Dans la page __Pare-feux et réseaux virtuels__, effectuez les actions suivantes :
    - Sous __Autoriser l’accès depuis__, cliquez sur __Réseaux sélectionnés__.
    - Sous __Réseaux virtuels__, sélectionnez __Ajouter des réseaux virtuels existants__ pour ajouter le réseau virtuel où se trouve votre calcul d’expérimentation.
    - Sous __Autoriser les services Microsoft approuvés pour contourner ce pare-feu__, sélectionnez __Oui__.

   [![La section « Pare-feux et réseaux virtuels » dans le volet Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Vous devez remplir les conditions suivantes pour pouvoir utiliser Azure Databricks dans un réseau virtuel avec votre espace de travail :

> [!div class="checklist"]
> * Le réseau virtuel doit être dans les mêmes abonnement et région que l’espace de travail Azure Machine Learning.
> * Si le ou les comptes de stockage Azure pour l’espace de travail sont également sécurisés dans un réseau virtuel, ils doivent se trouver dans le même réseau virtuel que le cluster Azure Databricks.
> * Outre les sous-réseaux __databricks-private__ et __databricks-public__ utilisés par Azure Databricks, le sous-réseau __default__ créé pour le réseau virtuel est également requis.

Pour obtenir des informations spécifiques sur l’utilisation d’Azure Databricks avec un réseau virtuel, consultez [Déployer des Azure Databricks dans votre réseau virtuel Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Machine virtuelle ou cluster HDInsight

> [!IMPORTANT]
> Azure Machine Learning prend uniquement en charge les machines virtuelles exécutant Ubuntu.

Pour utiliser une machine virtuelle ou un cluster Azure HDInsight dans un réseau virtuel avec votre espace de travail, effectuez les étapes suivantes :

1. Créez une machine virtuelle ou un cluster HDInsight à l’aide du portail Azure ou de l’interface de ligne de commande Azure, et placez le cluster dans un réseau virtuel Azure. Pour plus d’informations, consultez les articles suivants :
    * [Créer et gérer des réseaux virtuels Azure pour des machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Étendre HDInsight à l’aide d’un réseau virtuel Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Pour autoriser Azure Machine Learning à communiquer avec le port SSH sur la machine virtuelle ou le cluster, configurez une entrée source pour le groupe de sécurité réseau. Le port SSH est généralement le port 22. Pour autoriser le trafic provenant de cette source, effectuez les actions suivantes :

    * Dans la liste déroulante __Source__, sélectionnez __Balise de service__.

    * Dans la liste déroulante __Balise de service source__, sélectionnez __AzureMachineLearning__.

    * Dans la liste déroulante __Plages de port source__, sélectionnez __*__ .

    * Dans la liste déroulante __Destination__, sélectionnez __Tous__.

    * Dans la liste déroulante __Plages de port de destination__, sélectionnez __22__.

    * Sous __Protocole__, sélectionnez __Tous__.

    * Sous __Action__, sélectionnez __Autoriser__.

   ![Règles de trafic entrant pour effectuer des expériences sur une machine virtuelle ou un cluster HDInsight à l’intérieur d’un réseau virtuel](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Conservez les règles de trafic sortant par défaut pour le groupe de sécurité réseau. Pour plus d’informations, consultez les règles de sécurité par défaut dans [Groupes de sécurité](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Si vous ne souhaitez pas utiliser les règles de trafic sortant par défaut et souhaitez limiter l’accès sortant de votre réseau virtuel, consultez la section [Limiter la connectivité sortante à partir du réseau virtuel](#limiting-outbound-from-vnet).

1. Attachez la machine virtuelle ou le cluster HDInsight à votre espace de travail Azure Machine Learning. Pour plus d’informations, consultez [Configurer des cibles de calcul pour l’entraînement des modèles](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Étapes suivantes

* [Configurer des environnements d’entraînement](how-to-set-up-training-targets.md)
* [Configurer des points de terminaison privés](how-to-configure-private-link.md)
* [Où déployer les modèles](how-to-deploy-and-where.md)
* [Utiliser TLS pour sécuriser un service web par le biais d’Azure Machine Learning](how-to-secure-web-service.md)
