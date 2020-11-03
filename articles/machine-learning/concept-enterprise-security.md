---
title: Sécurité et gouvernance en entreprise
titleSuffix: Azure Machine Learning
description: 'Utilisez de manière sécurisée Azure Machine Learning : authentification, autorisation, sécurité réseau, chiffrement des données et supervision.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: b45c5cd1a750ee4b3f182920c4ee2f2e47756867
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899328"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sécurité et gouvernance de l’entreprise pour Azure Machine Learning

Dans cet article, vous allez découvrir les fonctionnalités de sécurité disponibles pour Azure Machine Learning.

Quand vous utilisez un service cloud, une bonne pratique consiste à limiter l’accès aux seuls utilisateurs qui en ont besoin. Commencez par comprendre le modèle d’authentification et d’autorisation utilisé par le service. Vous pouvez également restreindre l’accès réseau ou joindre de manière sécurisée des ressources de votre réseau local au cloud. Le chiffrement des données est également essentiel, aussi bien au repos et pendant le déplacement de données entre les services. Vous pouvez également créer des stratégies pour appliquer certaines configurations ou consigner dans le journal les configurations non conformes créées. Enfin, vous devez pouvoir superviser le service et produire un journal d’audit de toutes les activités.

> [!NOTE]
> Les informations contenues dans cet article fonctionnent avec le kit de développement logiciel (SDK) Python Azure Machine Learning 1.0.83.1 ou version ultérieure.

## <a name="authentication"></a>Authentification

L’authentification multifacteur est prise en charge si Azure Active Directory (Azure AD) est configuré pour l’utiliser. Voici le déroulement du processus d’authentification :

1. Le client se connecte à Azure AD et obtient un jeton Azure Resource Manager.  Les utilisateurs et les principaux de service sont entièrement pris en charge.
1. Le client présente le jeton à Azure Resource Manager et à tout Azure Machine Learning.
1. Le service Machine Learning fournit un jeton de service Machine Learning à la cible de calcul utilisateur (par exemple, Capacité de calcul Machine Learning). Ce jeton est utilisé par la cible de calcul utilisateur pour rappeler le service Machine Learning une fois l’exécution terminée. L’étendue se limite à l’espace de travail.

[![Authentification dans Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Pour plus d’informations, consultez [Configurer l’authentification pour des ressources et workflows Azure Machine Learning](how-to-setup-authentication.md). Cet article fournit des informations et des exemples sur l’authentification, y compris l’utilisation des principaux de service et des flux de travail automatisés.

### <a name="authentication-for-web-service-deployment"></a>Authentification pour le déploiement de service web

Azure Machine Learning prend en charge deux formes d’authentification pour les services web : la clé et le jeton. Chaque service web ne peut activer qu’une seule forme d’authentification à la fois.

|Méthode d'authentification|Description|Azure Container Instances|AKS|
|---|---|---|---|
|Clé|Les clés sont statiques et n’ont pas besoin d’actualisation. Elles peuvent être regénérées manuellement.|Désactivée par défaut| Activée par défaut|
|par jeton|Les jetons expirent après un laps de temps spécifié et doivent ensuite être actualisés.| Non disponible| Désactivée par défaut |

Pour obtenir des exemples de code, consultez la section [Authentification auprès d’un service web](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorisation

Vous pouvez créer plusieurs espaces de travail, et chacun d’eux peut être partagé par plusieurs personnes. Quand vous partagez un espace de travail, vous pouvez contrôler l’accès à celui-ci en attribuant ces rôles aux utilisateurs :

* Propriétaire
* Contributeur
* Lecteur

Le tableau suivant liste certaines des principales opérations Azure Machine Learning et les rôles qui peuvent les effectuer :

| Opération Azure Machine Learning | Propriétaire | Contributeur | Lecteur |
| ---- |:----:|:----:|:----:|
| Créer un espace de travail | ✓ | ✓ | |
| Partager l’espace de travail | ✓ | |  |
| Créer une cible de calcul | ✓ | ✓ | |
| Joindre une cible de calcul | ✓ | ✓ | |
| Attacher des magasins de données | ✓ | ✓ | |
| Exécuter une expérience | ✓ | ✓ | |
| Voir les exécutions/métriques | ✓ | ✓ | ✓ |
| Inscrire le modèle | ✓ | ✓ | |
| Créer une image | ✓ | ✓ | |
| Déployer un service web | ✓ | ✓ | |
| Voir les modèles/images | ✓ | ✓ | ✓ |
| Appeler un service web | ✓ | ✓ | ✓ |

Si les rôles prédéfinis ne répondent pas à vos besoins, vous pouvez créer des rôles personnalisés. Les rôles personnalisés sont pris en charge pour contrôler toutes les opérations au sein d’un espace de travail, telles que la création d’un calcul, l’envoi d’une exécution, l’inscription d’un magasin de banques ou le déploiement d’un modèle. Les rôles personnalisés peuvent avoir des autorisations de lecture, d’écriture ou de suppression sur les diverses ressources d’un espace de travail, telles que les clusters, les magasins de données, les modèles et les points de terminaison. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique. Pour plus d’informations, consultez [Gérer des utilisateurs et des rôles dans un espace de travail Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning est pris en charge avec la collaboration interentreprises Azure Active Directory, mais n’est pas actuellement pris en charge avec la collaboration entreprise-client Azure Active Directory.

### <a name="securing-compute-targets-and-data"></a>Sécurisation des cibles et données de calcul

Les propriétaires et les contributeurs peuvent utiliser toutes les cibles de calcul et tous les magasins de données qui sont joints à l’espace de travail.  

Chaque espace de travail est également associé à une identité managée attribuée par le système qui porte le même nom que l’espace de travail. L’identité managée dispose des autorisations suivantes sur les ressources attachées utilisées dans l’espace de travail.

Pour plus d’informations sur les identités managées, consultez [Identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Ressource | Autorisations |
| ----- | ----- |
| Espace de travail | Contributeur |
| Compte de stockage | Contributeur aux données Blob du stockage |
| Coffre de clés | Accès à l’ensemble des clés, secrets et certificats |
| Azure Container Registry | Contributeur |
| Groupe de ressources contenant l’espace de travail | Contributeur |
| Groupe de ressources contenant le coffre de clés (s’il diffère de celui contenant l’espace de travail) | Contributeur |

Nous déconseillons aux administrateurs de révoquer l’accès de l’identité managée aux ressources mentionnées dans le tableau précédent. Vous pouvez restaurer l’accès par une opération de resynchronisation des clés.

Azure Machine Learning crée une application supplémentaire (dont le nom commence par `aml-` ou `Microsoft-AzureML-Support-App-`) avec l’accès de niveau contributeur dans votre abonnement pour chaque région de l’espace de travail. Par exemple, si vous avez un espace de travail dans la région USA Est et un dans la région Europe Nord au sein du même abonnement, vous voyez deux de ces applications. Ces applications permettent à Azure Machine Learning de vous aider à gérer les ressources de calcul.

## <a name="network-security"></a>Sécurité du réseau

Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul. Les ressources de calcul (cibles de calcul) sont utilisées pour entraîner et déployer des modèles. Vous pouvez créer ces cibles de calcul dans un réseau virtuel. Par exemple, vous pouvez utiliser Data Science Virtual Machine pour entraîner un modèle, puis le déployer sur AKS.  

Pour en savoir plus, consultez [Vue d’ensemble de l’isolement et la confidentialité des réseaux virtuels](how-to-network-security-overview.md).

Vous pouvez également activer le service Liaison privée Azure pour votre espace de travail. Ce service vous permet de restreindre les communications vers votre espace de travail en provenance d’un réseau virtuel Azure. Pour plus d’informations, consultez [Comment configurer le service Liaison privée Azure](how-to-configure-private-link.md).

## <a name="data-encryption"></a>Chiffrement des données

> [!IMPORTANT]
> Pour le chiffrement de niveau production au cours de __l’apprentissage__ , Microsoft recommande d’utiliser le cluster de calcul Azure Machine Learning. Pour le chiffrement de niveau production au cours de __l’inférence__ , Microsoft recommande d’utiliser Azure Kubernetes Service.
>
> L’instance de calcul Azure Machine Learning constitue un environnement de dev/test. Lorsque vous l’utilisez, nous vous recommandons de stocker vos fichiers, notamment les notebooks et les scripts, dans un partage de fichiers. Vos données doivent être stockées dans un magasin de données.

### <a name="encryption-at-rest"></a>Chiffrement au repos

> [!IMPORTANT]
> Si votre espace de travail contient des données sensibles, nous vous recommandons de définir l’[indicateur hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) lors de la création de votre espace de travail. L’indicateur `hbi_workspace` ne peut être défini qu’au moment de la création d’un espace de travail. Il ne peut pas être modifié pour un espace de travail existant.

L'indicateur `hbi_workspace` contrôle la quantité de [données que Microsoft collecte à des fins de diagnostic](#microsoft-collected-data), et permet un [chiffrement supplémentaire dans des environnements gérés par Microsoft](../security/fundamentals/encryption-atrest.md). Il active par ailleurs les opérations suivantes :

* Lance le chiffrement du disque de travail local dans votre cluster de calcul Azure Machine Learning, à condition que vous n’ayez créé aucun cluster dans cet abonnement. Autrement, vous devez ouvrir un ticket de support pour activer le chiffrement du disque de travail de vos clusters de calcul. 
* Nettoie votre disque de travail local entre les exécutions.
* Transmet en toute sécurité les informations d’identification de votre compte de stockage, de votre registre de conteneurs et de votre compte SSH de la couche d’exécution à vos clusters de calcul en utilisant votre coffre de clés.
* Active le filtrage IP pour s’assurer que les pools Batch sous-jacents ne peuvent pas être appelés par des services externes autres que AzureMachineLearningService.

#### <a name="azure-blob-storage"></a>Stockage Blob Azure

Azure Machine Learning stocke les captures instantanées, les sorties et les journaux dans le compte Stockage Blob Azure qui est lié à l’espace de travail Azure Machine Learning et à votre abonnement. Toutes les données stockées dans Stockage Blob Azure sont chiffrées au repos à l’aide de clés gérées par Microsoft.

Pour plus d’informations sur la façon d’utiliser vos propres clés pour les données stockées dans Stockage Blob Azure, consultez [Chiffrement de Stockage Azure à l’aide de clés gérées par le client dans Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

En général, les données d’entraînement sont également stockées dans Stockage Blob Azure afin d’être accessibles pour les cibles de calcul d’entraînement. Ce stockage n’est pas managé par Azure Machine Learning mais il est monté sur des cibles de calcul en tant que système de fichiers distant.

Si vous devez opérer une __rotation ou révocation__ de votre clé, vous pouvez le faire à tout moment. Lors de la rotation d’une clé, le compte de stockage commence à utiliser la nouvelle clé (dernière version) pour chiffrer les données au repos. Lors de la révocation (désactivation) d’une clé, le compte de stockage se charge des demandes qui échouent. La prise d’effet de la rotation ou de la révocation nécessite généralement une heure.

Pour des informations sur la regénération des clés d’accès, consultez [Regénérer les clés d’accès au stockage](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning stocke les métriques et les métadonnées dans une instance d’Azure Cosmos DB. Cette instance est associée à un abonnement Microsoft géré par Azure Machine Learning. Toutes les données stockées dans Azure Cosmos DB sont chiffrées au repos à l’aide de clés gérées par Microsoft.

Pour utiliser vos propres clés (gérées par le client) afin de chiffrer l’instance d’Azure Cosmos DB, vous pouvez créer une instance de Cosmos DB dédiée à utiliser avec votre espace de travail. Nous vous recommandons cette approche si vous souhaitez stocker vos données, telles que des informations sur l’historique des exécutions, en dehors de l’instance de Cosmos DB mutualisée hébergée dans notre abonnement Microsoft. 

Pour activer l’approvisionnement d’une instance de Cosmos DB dans votre abonnement avec des clés gérées par le client, effectuez les actions suivantes :

* Inscrivez les fournisseurs de ressources Microsoft.MachineLearning et Microsoft.DocumentDB dans votre abonnement, si ce n’est pas déjà fait.

* Utilisez les paramètres suivants lors de la création de l’espace de travail Azure Machine Learning. Les deux paramètres sont obligatoires et pris en charge dans les kits de développement logiciel (SDK), l’interface CLI, les API REST et les modèle Resource Manager.

    * `resource_cmk_uri`: Ce paramètre est l’URI de ressource complète de la clé gérée par le client dans votre coffre de clés, y compris les [informations de version pour la clé](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Ce paramètre est l’ID de la ressource du coffre de clés dans votre abonnement. Ce coffre de clés doit être dans les mêmes région et abonnement que vous utiliserez pour l’espace de travail Azure Machine Learning. 
    
        > [!NOTE]
        > Cette instance de coffre de clés peut être différente du coffre de clés créé par Azure Machine Learning lorsque vous configurez l’espace de travail. Si vous voulez utiliser la même instance de coffre de clés pour l’espace de travail, passez le même coffre de clés pendant la configuration de l’espace de travail en utilisant le [paramètre key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Si vous devez opérer une __rotation ou révocation__ de votre clé, vous pouvez le faire à tout moment. Lors de la rotation d’une clé, Cosmos DB commence à utiliser la nouvelle clé (dernière version) pour chiffrer les données au repos. Lors de la révocation (désactivation) d’une clé, Cosmos DB se charge des demandes qui échouent. La prise d’effet de la rotation ou de la révocation nécessite généralement une heure.

Pour plus d’informations sur les clés gérées par le client avec Cosmos DB, consultez [Configurer des clés gérées par le client pour votre compte Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Toutes les images conteneur dans votre registre ( Azure Container Registry) sont chiffrées au repos. Azure chiffre automatiquement une image avant de la stocker et la déchiffre quand Azure Machine Learning extrait l’image.

Pour utiliser vos propres clés (gérées par le client) pour chiffrer votre Azure Container Registry, vous devez créer votre propre ACR et le joindre pendant la configuration de l’espace de travail ou chiffrer l’instance par défaut qui est créée au moment de la configuration de l’espace de travail.

> [!IMPORTANT]
> Azure Machine Learning nécessite l’activation du compte administrateur sur votre instance Azure Container Registry. Par défaut, ce paramètre est désactivé lorsque vous créez un registre de conteneurs. Pour plus d’informations sur l’activation du compte administrateur, consultez [Compte administrateur](/azure/container-registry/container-registry-authentication#admin-account).
>
> Une fois qu’un Azure Container Registry a été créé pour un espace de travail, ne le supprimez pas. Cela entraînerait l’arrêt de votre espace de travail Azure Machine Learning.

Pour un exemple de création d’un espace de travail en utilisant un Azure Container Registry existant, consultez les articles suivants :

* [Créer un espace de travail pour Azure Machine Learning avec Azure CLI](how-to-manage-workspace-cli.md).
* [Créer un espace de travail avec kit de développement logiciel (SDK) Python](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Utiliser un modèle Azure Resource Manager pour créer un espace de travail pour Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instance

Vous pouvez chiffrer une ressource Azure Container instance (ACI) déployée en utilisant des clés gérées par le client. Vous pouvez stocker la clé gérée par le client utilisée pour ACI dans l’Azure Key Vault pour votre espace de travail. Pour plus d’informations sur la génération d’une clé, consultez [Chiffrer des données avec une clé gérée par le client](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Pour utiliser la clé lors du déploiement d’un modèle sur Azure Container instance, créez une nouvelle configuration de déploiement en utilisant la commande `AciWebservice.deploy_configuration()`. Fournissez les informations de clé à l’aide des paramètres suivants :

* `cmk_vault_base_url`: URL du coffre de clés contenant la clé.
* `cmk_key_name`: Nom de la clé.
* `cmk_key_version`: Version de la clé.

Pour plus d’informations sur la création et l’utilisation d’une configuration de déploiement, consultez les articles suivants :

* Informations de référence sur [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Où et comment déployer](how-to-deploy-and-where.md)
* [Déployer un modèle sur Azure Container Instances](how-to-deploy-azure-container-instance.md)

Pour plus d’informations sur l’utilisation d’une clé gérée par le client avec ACI, consultez [Chiffrer des données avec une clé gérée par le client](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Vous pouvez chiffrer une ressource Azure Kubernetes Service déployée en utilisant des clés gérées par un client à tout moment. Pour plus d’informations, consultez [Apporter vos propres clés avec Azure Container Service](../aks/azure-disk-customer-managed-keys.md). 

Ce processus vous permet de chiffrer à la fois les données et le disque de système d’exploitation des machines virtuelles déployées dans le cluster Kubernetes.

> [!IMPORTANT]
> Ce processus fonctionne uniquement avec AKS K8s version 1.17 ou ultérieure. Azure Machine Learning a ajouté un support pour AKS 1.17 le 13 janvier 2020.

#### <a name="machine-learning-compute"></a>Capacité de calcul Machine Learning

Le disque de système d’exploitation de chaque nœud de calcul stocké dans Stockage Azure est chiffré à l’aide de clés gérées par Microsoft dans les comptes de stockage Azure Machine Learning. Cette cible de calcul est éphémère et les clusters font généralement l’objet d’un scale-down quand aucune exécution n’est placée en file d’attente. La machine virtuelle sous-jacente est déprovisionnée et le disque de système d’exploitation supprimé. Azure Disk Encryption n’est pas pris en charge pour le disque de système d’exploitation.

Chaque machine virtuelle dispose également d’un disque temporaire local pour les opérations de système d’exploitation. Si vous le souhaitez, vous pouvez utiliser le disque pour indexer les données d’entraînement. Le disque est chiffré par défaut pour les espaces de travail avec le paramètre `hbi_workspace` défini sur `TRUE`. Cet environnement ne perdure que pour la durée de votre exécution et la prise en charge du chiffrement est limitée aux clés gérées par le système uniquement.

#### <a name="azure-databricks"></a>Azure Databricks

Vous pouvez utiliser la plateforme Azure Databricks dans des pipelines Azure Machine Learning. Par défaut, le système de fichiers Databricks (DBFS) qu’utilise Azure Databricks est chiffré à l’aide d’une clé gérée par Microsoft. Pour configurer Azure Databricks afin d’utiliser des clés gérées par le client, voir [Configurer les clés gérées par le client sur DBFS (racine) par défaut](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Chiffrement en transit

Azure Machine Learning utilise le protocole TLS pour sécuriser la communication interne entre ses différents microservices. Tout l’accès au stockage Azure se produit également sur un canal sécurisé.

Pour sécuriser les appels externes au point de terminaison de scoring, Azure Machine Learning utilise le protocole TLS. Pour plus d’informations, consultez [Utiliser TLS pour sécuriser un service web par le biais d’Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilisation d’Azure Key Vault

Azure Machine Learning utilise l’instance Azure Key Vault associée à l’espace de travail pour stocker les informations d’identification de différents types :

* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données

Les mots de passe et clés SSH des cibles de calcul telles que HDI HDInsight et les machines virtuelles sont stockés dans un coffre de clés distinct qui est associé à l’abonnement Microsoft. Azure Machine Learning ne stocke pas les mots de passe ni les clés fournis par les utilisateurs. Il génère, autorise et stocke plutôt ses propres clés SSH pour se connecter aux machines virtuelles et à HDInsight afin d’exécuter les expériences.

Chaque espace de travail est associé à une identité managée attribuée par le système qui porte le même nom que l’espace de travail. Cette identité managée a accès à l’ensemble des clés, secrets et certificats contenus dans le coffre de clés.

## <a name="data-collection-and-handling"></a>Collecte et gestion de données

### <a name="microsoft-collected-data"></a>Données collectées par Microsoft

Microsoft peut collecter des informations ne permettant pas d’identifier les utilisateurs telles que des noms de ressource (par exemple le nom du jeu de données ou le nom de l’essai d’apprentissage automatique), ou des variable d'environnement de tâche à des fins de diagnostic. De telles données sont stockées à l’aide des clés gérées par Microsoft dans un stockage hébergé dans des abonnements appartenant à Microsoft, conformément aux [normes de gestion des données et à la politique de confidentialité standard de Microsoft](https://privacy.microsoft.com/privacystatement).

Microsoft recommande également de ne pas stocker d’informations sensibles (comme les secrets de clé de compte) dans les variables d'environnement. Les variable d'environnement sont enregistrées, chiffrées et stockées par nous. De même, lorsque vous nommez [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true), évitez d’inclure des informations sensibles telles que des noms d’utilisateurs ou des noms de projets secrets. Ces informations peuvent apparaître dans les journaux de télémétrie accessibles aux ingénieurs du Support Microsoft.

Vous pouvez refuser la collecte des données de diagnostic en définissant le paramètre `hbi_workspace` sur `TRUE` pendant la configuration de l’espace de travail. Cette fonctionnalité est prise en charge lorsque le kit de développement logiciel (SDK) AzureML Python, l’interface CLI, les API REST ou les modèles Azure Resource Manager sont utilisés.

### <a name="microsoft-generated-data"></a>Données générées par Microsoft

Lorsque vous utilisez des services comme Machine Learning automatisé, Microsoft peut générer des données prétraitées temporaires pour l’apprentissage de plusieurs modèles. Ces données sont stockées dans un magasin de données de votre espace de travail, ce qui vous permet d’appliquer des contrôles d'accès et un chiffrement appropriés.

Vous pouvez aussi chiffrer des [informations de diagnostic enregistrées à partir de votre point de terminaison déployé](how-to-enable-app-insights.md) dans votre instance d’Azure Application Insights.

## <a name="monitoring"></a>Surveillance

### <a name="metrics"></a>Mesures

Vous pouvez utiliser des métriques Azure Monitor pour voir et superviser les métriques de votre espace de travail Azure Machine Learning. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail, puis sélectionnez **Métriques**  :

[![Capture d’écran montrant des exemples de métriques pour un espace de travail](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Les métriques incluent des informations sur les exécutions, les déploiements et les inscriptions.

Pour plus d’informations, consultez [Mesures dans Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Journal d’activité

Vous pouvez afficher le journal d’activité d’un espace de travail pour voir les différentes opérations qui y sont exécutées. Le journal contient des informations de base comme le nom de l’opération, l’initiateur de l’événement et l’horodatage.

Cette capture d’écran montre le journal d’activité d’un espace de travail :

[![Capture d’écran montrant le journal d’activité d’un espace de travail](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Les détails des requêtes de scoring sont stockés dans Application Insights. Application Insights est créé dans votre abonnement quand vous créez un espace de travail. Les informations enregistrées incluent des champs tels que :

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Certaines actions dans l’espace de travail Azure Machine Learning ne consignent pas d’informations dans le journal d’activité. Par exemple, le départ de l’exécution d’entraînement et l’inscription d’un modèle ne sont pas consignés.
>
> Certaines de ces actions apparaissent dans la zone **Activités** de votre espace de travail, mais ces notifications n’indiquent pas qui a lancé l’activité.

### <a name="vulnerability-scanning"></a>Analyse des vulnérabilités

Azure Security Center fournit des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides. Pour Azure Machine Learning, vous devez activer l’analyse de vos ressources Azure Container Registry et Azure Container Service. Pour plus d’informations, consultez [Analyse d’images Azure Container Registry par Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) et [Intégration d’Azure Kubernetes Service à Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

## <a name="data-flow-diagrams"></a>Diagrammes de flux de données

### <a name="create-workspace"></a>Créer un espace de travail

Le diagramme suivant montre le workflow de création d’un espace de travail.

* Vous vous connectez à Azure AD à partir de l’un des clients Azure Machine Learning pris en charge (Azure CLI, SDK Python, portail Microsoft Azure) et demandez le jeton Azure Resource Manager approprié.
* Vous appelez Azure Resource Manager pour créer l’espace de travail. 
* Azure Resource Manager contacte le fournisseur de ressources Azure Machine Learning pour provisionner l’espace de travail.

Des ressources supplémentaires sont créées dans l’abonnement de l’utilisateur lors de la création de l’espace de travail :

* Key Vault (pour stocker des secrets)
* Un compte de stockage Azure (dont les objets blob et le partage de fichiers)
* Azure Container Registry (pour stocker les images Docker pour l’inférence/le scoring et l’expérimentation)
* Application Insights (pour stocker les données de télémétrie)

L’utilisateur peut également provisionner d’autres cibles de calcul attachées à un espace de travail (comme Azure Kubernetes Service ou des machines virtuelles) selon les besoins.

[![Créer un workflow d’espace de travail](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Enregistrer le code source (scripts d’entraînement)

Le diagramme suivant montre le workflow de capture instantanée du code.

Des répertoires (expériences) qui contiennent le code source (scripts d’entraînement) sont associés à un espace de travail Azure Machine Learning. Ces scripts sont stockés sur votre machine locale et dans le cloud (dans le Stockage Blob Azure de votre abonnement). Les instantanés de code sont utilisés pour l’exécution ou l’inspection de l’audit d’historique.

[![Workflow de la capture instantanée de code](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>Entrainement

Le diagramme suivant montre le workflow d’entraînement.

* Azure Machine Learning est appelé avec l’ID de capture instantanée pour la capture instantanée du code enregistrée dans la section précédente.
* Azure Machine Learning crée un ID d’exécution (facultatif) et un jeton de service Machine Learning, qui est utilisé par la suite par des cibles de calcul comme la Capacité de calcul Machine Learning/les machines virtuelles pour communiquer avec le service Machine Learning.
* Vous pouvez choisir une cible de calcul gérée (comme Capacité de calcul Machine Learning) ou une cible de calcul non gérée (comme des machines virtuelles) pour exécuter vos travaux d’apprentissage. Voici les flux de données des deux scénarios :
   * Machines virtuelles/HDInsight, accessibles par le biais d’informations d’identification SSH contenues dans un coffre de clés dans l’abonnement Microsoft. Azure Machine Learning exécute le code de gestion sur la cible de calcul qui :

   1. Prépare l’environnement (Docker est une option pour les machines virtuelles et les ordinateurs locaux. Consultez les étapes suivantes pour la Capacité de calcul Machine Learning afin de comprendre le fonctionnement de l’exécution des expériences sur des conteneurs Docker.)
   1. Télécharge le code.
   1. Définit des variables d’environnement et des configurations.
   1. Exécute des scripts utilisateur (capture instantanée de code mentionnée dans la section précédente).

   * Capacité de calcul Machine Learning, accessible par le biais d’une identité managée par l’espace de travail.
Étant donné que la Capacité de calcul Machine Learning est une cible de calcul managée (c’est-à-dire qu’elle est gérée par Microsoft), elle s’exécute sous votre abonnement Microsoft.

   1. Une construction du Docker distant est démarrée si nécessaire.
   1. Le code de gestion est écrit dans le partage Azure Files de l’utilisateur.
   1. Le conteneur est démarré avec une commande initiale ; autrement dit, avec le code de gestion décrit à l’étape précédente.

#### <a name="querying-runs-and-metrics"></a>Interrogation des exécutions et des métriques

Dans le diagramme de flux ci-dessous, cette étape se produit quand la cible de calcul d’entraînement réécrit les métriques d’exécution dans Azure Machine Learning à partir du stockage de la base de données Cosmos DB. Les clients peuvent appeler Azure Machine Learning. Machine Learning tire (pull) ensuite les métriques de la base de données Cosmos DB pour les renvoyer au client.

[![Workflow de l’entraînement](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Création de services web

Le diagramme suivant montre le workflow d’inférence. L’inférence, ou scoring du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production.

Voici les détails :

* L’utilisateur inscrit un modèle à l’aide d’un client comme le SDK Azure Machine Learning.
* L’utilisateur crée une image à l’aide d’un modèle, d’un fichier de scores et d’autres dépendances de modèle.
* L’image Docker est créée et stockée dans Azure Container Registry.
* Le service web est déployé sur la cible de calcul (Container Instances/AKS) à l’aide de l’image créée à l’étape précédente.
* Les détails des requêtes de scoring sont stockés dans la fonctionnalité Application Insights qui se trouve dans l’abonnement de l’utilisateur.
* Des données de télémétrie sont également envoyées (push) à l’abonnement Microsoft/Azure.

[![Workflow de l’inférence](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="audit-and-manage-compliance"></a>Auditer et gérer la conformité

[Azure Policy](/azure/governance/policy) est un outil de gouvernance qui vous permet de vous assurer que les ressources Azure sont conformes à vos stratégies. Avec Azure Machine Learning, vous pouvez attribuer les stratégies suivantes :

* **Clé gérée par le client**  : auditez ou appliquez une valeur indiquant si les espaces de travail doivent utiliser une clé gérée par le client.
* **Liaison privée**  : Auditer si les espaces de travail utilisent un point de terminaison privé pour communiquer avec un réseau virtuel.

Pour plus d’informations sur Azure Policy, consultez la [documentation relative à Azure Policy](/azure/governance/policy/overview).

Pour plus d’informations sur les stratégies spécifiques à Azure Machine Learning, consultez [Audit et gestion de la conformité avec Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Verrous de ressources

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les services web Azure Machine Learning avec TLS](how-to-secure-web-service.md)
* [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
* [Utiliser Azure Machine Learning avec un pare-feu Azure](how-to-access-azureml-behind-firewall.md)
* [Utiliser Azure Machine Learning avec un réseau virtuel Azure](how-to-network-security-overview.md)
* [Bonnes pratiques pour la création de systèmes de recommandation](https://github.com/Microsoft/Recommenders)
* [Générer une API de recommandation en temps réel sur Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
