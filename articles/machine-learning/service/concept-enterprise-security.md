---
title: Sécurité d’entreprise
titleSuffix: Azure Machine Learning service
description: 'Utiliser en toute sécurité de service Azure Machine Learning : l’authentification, l’autorisation, la sécurité du réseau, chiffrement de données et de surveillance.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821339"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Sécurité d’entreprise pour le service Azure Machine Learning

Dans cet article, vous allez découvrir les fonctionnalités de sécurité disponibles avec le service d’apprentissage Azure.

Lorsque vous utilisez un service cloud, il est recommandé de restreindre l’accès uniquement aux utilisateurs qui en ont besoin. Cela commence par décrire le modèle d’authentification et d’autorisation utilisé par le service. Vous pouvez également restreindre l’accès réseau, ou joindre en toute sécurité des ressources dans votre réseau local avec ceux présents dans le cloud. Chiffrement des données est également essentiel, au repos et pendant que les données sont déplacées entre les services. Enfin, vous devez être en mesure de surveiller le service et de produire un journal d’audit de toutes les activités.

## <a name="authentication"></a>Authentification
Multi-Factor authentication est pris en charge si Azure Active Directory (Azure AD) est configuré pour le même.
* Client se connecte à Azure AD et obtient la jeton de Azure Resource Manager.  Les utilisateurs et principaux de Service sont entièrement pris en charge.
* Client présente le jeton à Azure Resource Manager et tous les services Azure Machine Learning
* Service Azure Machine Learning fournit un jeton d’Azure Machine Learning pour le calcul de l’utilisateur. Par exemple, Machine Learning Compute. Ce jeton est utilisé par l’utilisateur Azure Machine Learning de calcul pour effectuer un rappel dans le service Azure Machine Learning (limite l’étendue à l’espace de travail) après l’exécution est terminée.

![Capture d’écran montrant le fonctionnement de l’authentification dans le service Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Clés d’authentification pour le déploiement de service Web

Lorsque vous activez l’authentification pour un déploiement, vous créez automatiquement des clés d’authentification.

* L’authentification est activée par défaut lors du déploiement sur Azure Kubernetes Service.
* L’authentification est désactivée par défaut lors du déploiement sur Azure Container Instances.

Pour contrôler l’authentification, utilisez le paramètre `auth_enabled` pendant la création ou la mise à jour d’un déploiement.

Si l’authentification est activée, vous pouvez utiliser la méthode `get_keys` pour récupérer une clé d’authentification primaire et secondaire :

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Si vous devez régénérer une clé, utilisez [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Authorization

Vous pouvez créer plusieurs espaces de travail, et chacun d’eux peut être partagé par plusieurs personnes. Lorsque vous partagez un espace de travail, vous pouvez contrôler l’accès à celui-ci en attribuant les rôles suivants aux utilisateurs :
* Propriétaire
* Contributeur
* Lecteur
    
Le tableau suivant répertorie certaines des principales opérations de service Azure Machine Learning et les rôles qui les exécutent :

| Service Azure Machine Learning opération | Propriétaire | Contributeur | Lecteur |
| ---- |:----:|:----:|:----:|
| Créer un espace de travail | ✓ | ✓ | |
| Partager l’espace de travail | ✓ | |  |
| Créer le calcul | ✓ | ✓ | |
| Attacher le calcul | ✓ | ✓ | |
| Attacher des banques de données | ✓ | ✓ | |
| Exécutez une expérience | ✓ | ✓ | |
| Afficher les exécutions/mesures | ✓ | ✓ | ✓ |
| Inscrire le modèle | ✓ | ✓ | |
| Création d’image | ✓ | ✓ | |
| Déployer un service web | ✓ | ✓ | |
| Vue modèles/images | ✓ | ✓ | ✓ |
| Appeler le service web | ✓ | ✓ | ✓ |

Si les rôles intégrés ne répondent pas à vos besoins, vous pouvez également créer des rôles personnalisés. Notez que les rôles personnalisés seulement que nous prenons en charge pour les opérations sur l’espace de travail et Machine Learning Compute. Les rôles personnalisés peut-être lire, écrire ou supprimer des autorisations sur l’espace de travail et de la ressource de calcul dans cet espace de travail. Vous pouvez rendre disponible à un niveau de l’espace de travail spécifique, un niveau de groupe de ressources spécifique ou un niveau d’abonnement spécifique. Pour plus d’informations, consultez [gérer les utilisateurs et rôles dans un espace de travail Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Sécurisation de calcul et données
Propriétaires et les collaborateurs peuvent utiliser toutes les calcul cibles et magasins de données qui sont attachés à l’espace de travail.  
Chaque espace de travail a également un MSI d’affecté par le système associé (avec le même nom que l’espace de travail) avec les autorisations suivantes sur les ressources jointes utilisées dans l’espace de travail :

Pour plus d’informations sur les identités gérées, consultez [gérés d’identités pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Ressource | Autorisations |
| ----- | ----- |
| Espace de travail | Contributeur | 
| Compte de stockage | Contributeur aux données Blob du stockage | 
| Key Vault | Accès à tous les certificats de clés, Secrets, | 
| Azure Container Registry | Contributeur | 
| Groupe de ressources qui contient l’espace de travail | Contributeur | 
| Groupe de ressources qui contient le coffre de clés (s’il diffère de celui qui contient l’espace de travail) | Contributeur | 

Il est recommandé que les administrateurs ne révoquent pas l’accès de l’identité gérée pour les ressources mentionnées ci-dessus. L’accès peut être restauré avec l’opération de resynchronisation des clés.

Service Azure Machine Learning crée une application supplémentaire (le nom commence par aml-) avec l’accès au niveau du contributeur dans votre abonnement pour chaque région de l’espace de travail. Pour ex. Si vous avez un espace de travail dans l’est des États-Unis et un autre espace de travail en Europe du Nord dans le même abonnement, vous verrez 2 de telles applications. Cela est nécessaire afin que les ressources de calcul Azure Machine Learning service peut aider à gérer.


## <a name="network-security"></a>Sécurité du réseau

Le service Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul. Les ressources de calcul (cibles de calcul) sont utilisées pour entraîner et déployer des modèles. Ces cibles de calcul peuvent être créées à l’intérieur d’un réseau virtuel. Par exemple, vous pouvez utiliser la machine virtuelle Microsoft Data Science Virtual Machine pour entraîner un modèle, puis déployer le modèle sur Azure Kubernetes Service (AKS).  

Pour plus d’informations, consultez [comment exécuter des expériences d’inférence dans un réseau virtuel](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Chiffrement des données

### <a name="encryption-at-rest"></a>Chiffrement au repos
#### <a name="azure-blob-storage"></a>un stockage Azure Blob
Service Azure Machine Learning stocke les captures instantanées, les sorties et les journaux dans le compte de stockage d’objets Blob Azure qui est lié à l’espace de travail du service Azure Machine Learning et se trouve dans l’abonnement de l’utilisateur. Toutes les données stockées dans le stockage Blob Azure sont chiffrées au repos à l’aide de clés de Microsoft-Managed.

Pour plus d’informations sur la façon d’apporter vos propres clés pour les données stockées dans stockage Blob Azure, consultez [Storage Service Encryption à l’aide de clés gérées par le client dans Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Données d’apprentissage sont généralement également stockées dans le stockage Blob Azure afin qu’il soit accessible pour le calcul de formation. Ce stockage n’est pas géré par Azure Machine Learning mais n’est pas monté pour le calcul comme un système de fichiers distant.

#### <a name="cosmos-db"></a>Cosmos DB
Service Azure Machine Learning stocke les métriques et les métadonnées pour la base de données Cosmos qui réside dans un abonnement Microsoft géré par le service Azure Machine Learning. Toutes les données stockées dans Cosmos DB sont chiffrées au repos à l’aide de clés gérées Microsoft.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)
Toutes les images de conteneur dans votre Registre (ACR) sont chiffrées au repos. Azure chiffre une image avant de les stocker automatiquement et la déchiffre à la volée lorsque le service Azure Machine Learning extrait l’image.

#### <a name="machine-learning-compute"></a>Capacité de calcul Machine Learning
Le disque du système d’exploitation pour chaque nœud de calcul est stockée dans le stockage Azure est chiffré à l’aide de clés de gérées par Microsoft dans les comptes de stockage du service Azure Machine Learning. Ce calcul est éphémère et clusters sont généralement mis à l’échelle vers le bas lorsqu’il n’y a aucune exécution en file d’attente. La machine virtuelle sous-jacente est déprovisionnée et supprimés du disque du système d’exploitation. Chiffrement de disque Azure n’est pas pris en charge pour le disque du système d’exploitation.
Chaque machine virtuelle possède également un disque local temporaire pour les opérations de système d’exploitation. Ce disque peut également être éventuellement utilisé pour organiser les données de formation. Ce disque n’est pas chiffré. Pour plus d’informations sur le fonctionnement du chiffrement au repos dans Azure, consultez [chiffrement au repos des données Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Chiffrement en transit
Les deux communication interne entre les différents services micro-charges Azure Machine Learning et la communication externe de l’appel du point de terminaison de notation sont pris en charge à l’aide de SSL. Tous les accès de stockage Azure sont également sur un canal sécurisé. Pour plus d’informations, consultez [services web de sécuriser Azure Machine Learning à l’aide de SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilisation d’Azure Key Vault
Instance de coffre de clés associé à l’espace de travail est utilisée par le service Azure Machine Learning pour stocker les informations d’identification de différents types :
* La chaîne de connexion de compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux données de magasins. 

Les mots de passe SSH et clés de cibles telles que les machines virtuelles et HDI HDInsight de calcul sont stockés dans un coffre de clés distinct qui est associé à un abonnement Microsoft. Service Azure Machine Learning ne stocke pas les mots de passe ou clés fournies par l’utilisateur au lieu de cela il génère, autorise et stocke ses propres clés SSH pour vous connecter à la machine virtuelle/HDInsight pour exécuter les expériences. Chaque espace de travail a une associé attribué par le système géré identité (avec le même nom que l’espace de travail) qui a accès à toutes les clés, secrets et certificats dans le coffre de clés.

 
## <a name="monitoring"></a>Surveillance

Les utilisateurs peuvent voir le journal d’activité sous l’espace de travail pour voir les diverses opérations effectuées sur l’espace de travail et obtenir les informations de base telles que le nom de l’opération, un événement initié par, un horodatage etc.

La capture d’écran suivante montre le journal d’activité pour un espace de travail :

![Journal d’activité de capture d’écran affichant sous un espace de travail](./media/enterprise-readiness/workspace-activity-log.png)


Calcul de score détails de la requête sont stockés dans AppInsights, qui est créé dans l’abonnement d’utilisateur lors de la création de l’espace de travail. Cela inclut les champs comme HTTPMethod UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, etc. de durée.


## <a name="data-flow-diagram"></a>Diagramme de flux de données

### <a name="create-workspace"></a>Créer un espace de travail
Le diagramme suivant illustre le flux de travail créer espace de travail.
Utilisateur se connecte à Azure AD à partir de tous les clients de service Azure Machine Learning pris en charge (portail Azure CLI, Python, Kit de développement,) et demande le jeton d’Azure Resource Manager approprié.  Utilisateur appelle ensuite le Gestionnaire de ressources Azure pour créer l’espace de travail.  Fournisseur de ressources pour configurer l’espace de travail de service Azure Resource Manager contacts le Azure Machine Learning.  Ressources supplémentaires sont créés dans l’abonnement du client lors de la création d’espace de travail :
* Coffre de clés (pour stocker des secrets)
* Un compte de stockage Azure (y compris les objets Blob & partage de fichiers)
* Azure Container Registry (pour stocker les images docker pour inférence et l’expérimentation)
* Application Insights (pour stocker les données de télémétrie)

Autres services de calcul attachés à un espace de travail (Azure Kubernetes Service, etc. de la machine virtuelle) peuvent également être configurés par les clients en fonction des besoins. 

![Capture d’écran de créer des flux de travail espace de travail](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Enregistrez le code source (scripts de formation)
Le diagramme suivant illustre le flux de travail de capture instantanée de code.
Associé à un Azure Machine Learning espace de travail de service sont des répertoires (expérimentation), qui contient le code source (scripts de formation).  Celles-ci sont stockées sur l’ordinateur du client local et dans le cloud (dans le stockage Blob Azure, sous l’abonnement du client). Ces captures instantanées de code sont utilisées pour l’exécution ou l’inspection d’audit historiques.

![Capture d’écran de créer des flux de travail espace de travail](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Formation
Le diagramme suivant illustre le flux de travail de formation.
* Service Azure Machine Learning est appelé avec l’ID d’instantané pour la capture instantanée de code enregistrée plus tôt
* Azure Machine Learning crée service exécuter ID (facultatif) et le jeton de service Azure Machine Learning, qui est utilisé ultérieurement par les cibles de calcul tels que Machine Learning calcul/la machine virtuelle pour répondre à un service Azure Machine Learning
* Vous pouvez choisir un calcul gérée (par ex. Machine Learning Compute) ou de calcul non managé (par ex. Machine virtuelle) pour exécuter vos travaux de formation. Flux de données est indiqué pour les deux les scénarios ci-dessous :
* (Machine virtuelle/HDInsight/Local – accédé à l’aide des informations d’identification SSH dans Key Vault dans un abonnement à Microsoft) Service Azure Machine Learning s’exécute le code de gestion sur la cible de calcul qui :
    1.  Prépare l’environnement (Remarque : Docker est une option pour la machine virtuelle/locale. Consultez les étapes pour Machine Learning Compute ci-dessous comprendre comment en cours d’exécution d’expérience sur le fonctionnement du conteneur docker)
    2.  Télécharge le code
    3.  Configure les variables d’environnement/configurations
    4.  Exécute le script de l’utilisateur (instantané code mentionné ci-dessus)
* (Machine Learning Compute – accédé à l’aide d’identité de l’espace de travail géré) Notez que la Machine Learning Compute étant un calcul gérée, autrement dit, il est géré par Microsoft, par conséquent, il s’exécute sous l’abonnement Microsoft.
    1.  Construction de Docker à distance est lancée si nécessaire
    2.  Écrit le code de gestion à l’utilisateur partage de fichiers Azure
    3.  Conteneur démarre avec initiale de commandes, autrement dit, code de gestion de l’étape ci-dessus


#### <a name="querying-runs--metrics"></a>Interrogation des exécutions et des mesures
Cette étape est indiquée dans le flux où formation calcul écritures le *exécuter les métriques* vers le service Azure Machine Learning à partir d’où il est stocké dans la base de données Cosmos. Les clients peuvent appeler le service Azure Machine Learning qui à son tour extraire les métriques à partir de la base de données Cosmos et retourner au client.

![Capture d’écran de créer des flux de travail espace de travail](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Création de services web
Le diagramme suivant illustre le flux de travail inférence dans lequel le modèle est déployé comme un service web.
Voir les détails ci-dessous :
* Utilisateur inscrit un modèle à l’aide d’un client comme le Kit de développement logiciel Azure ML
* Utilisateur crée l’image à l’aide du modèle, fichier de score et autres dépendances de modèle
* L’Image Docker est créée et stockée dans ACR
* Service Web est déployée sur la cible de calcul (ACI/ACS) à l’aide de l’image créée ci-dessus
* Calcul de score détails de la requête sont stockés dans AppInsights, qui se trouve dans l’abonnement de l’utilisateur
* Données de télémétrie sont également envoyée à Microsoft/abonnement Azure

![Capture d’écran de créer des flux de travail espace de travail](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Exécuter des prédictions par lots](how-to-run-batch-predictions.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
* [SDK du service Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Utiliser Azure Machine Learning service avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
* [Bonnes pratiques pour la création de systèmes de recommandation](https://github.com/Microsoft/Recommenders)
* [Générer une API de recommandation en temps réel sur Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
