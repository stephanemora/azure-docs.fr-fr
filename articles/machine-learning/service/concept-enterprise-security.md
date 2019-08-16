---
title: Sécurité d’entreprise
titleSuffix: Azure Machine Learning service
description: 'Utilisez de manière sécurisée le service Azure Machine Learning : authentification, autorisation, sécurité réseau, chiffrement des données et supervision.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/10/2019
ms.openlocfilehash: f0fb6f0d2b2579679ee8a6ec43b3241377701d48
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780901"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Sécurité de l’entreprise pour le service Azure Machine Learning

Dans cet article, vous allez découvrir les fonctionnalités de sécurité disponibles avec le service Azure Machine Learning.

Quand vous utilisez un service cloud, il est recommandé de restreindre l’accès aux utilisateurs qui en ont besoin. Pour cela, il faut d’abord comprendre le modèle d’authentification et d’autorisation utilisé par le service. Vous pouvez également restreindre l’accès réseau, ou joindre de manière sécurisée des ressources de votre réseau local à celles présentes dans le cloud. Le chiffrement des données est également essentiel, aussi bien au repos et pendant le déplacement de données entre les services. Enfin, vous devez pouvoir superviser le service et produire un journal d’audit de toutes les activités.

## <a name="authentication"></a>Authentication

L’authentification multifacteur est prise en charge si Azure Active Directory (Azure AD) est configuré pour cela.

* Le client se connecte à Azure AD et obtient un jeton Azure Resource Manager.  Les utilisateurs et les principaux de service sont entièrement pris en charge.
* Le client présente le jeton à Azure Resource Manager et tous les services Azure Machine Learning.
* Le service Azure Machine Learning fournit un jeton Azure Machine Learning pour le calcul de l’utilisateur. Par exemple, la capacité de calcul Machine Learning. Ce jeton Azure Machine Learning est utilisé par le calcul de l’utilisateur pour effectuer un rappel dans le service Azure Machine Learning (les limites définies sont l’espace de travail) une fois l’exécution terminée.

![Capture d’écran montrant le fonctionnement de l’authentification dans le service Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Authentification pour le déploiement de service web

Azure Machine Learning prend en charge deux formes d’authentification pour les services web, la clé et le jeton. Chaque service web ne peut activer qu’une seule forme d’authentification à la fois.

|Méthode d'authentification|ACI|AKS|
|---|---|---|
|Clé|Désactivé par défaut| Activée par défaut|
|Jeton| Non disponible| Désactivée par défaut |

#### <a name="authentication-with-keys"></a>Authentification avec des clés

Lorsque vous activez l’authentification par clé pour un déploiement, vous créez automatiquement des clés d’authentification.

* L’authentification est activée par défaut lors du déploiement sur Azure Kubernetes Service.
* L’authentification est désactivée par défaut lors du déploiement sur Azure Container Instances.

Pour activer l’authentification par clé, utilisez le paramètre `auth_enabled` pendant la création ou la mise à jour d’un déploiement.

Si l’authentification par clé est activée, vous pouvez utiliser la méthode `get_keys` pour récupérer des clés d’authentification primaire et secondaire :

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Si vous devez regénérer une clé, utilisez [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Authentification avec des jetons

Lorsque vous activez l’authentification par jeton pour un service web, un utilisateur doit fournir un jeton web JSON Azure Machine Learning JWT au service web pour y accéder.

* L’authentification par jeton est désactivée par défaut lors d’un déploiement sur Azure Kubernetes Service.
* L’authentification par jeton n’est pas prise en charge lorsque vous déployez sur instances de conteneur Azure.

Pour contrôler l’authentification par jeton, utilisez le paramètre `token_auth_enabled` lorsque vous créez ou mettez à jour un déploiement.

Si l’authentification par jeton est activée, vous pouvez utiliser la méthode `get_token` pour récupérer un jeton JWT et le délai d’expiration du jeton :

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Vous devrez demander un nouveau jeton après l’heure de `refresh_by` du jeton.
>
> Nous vous recommandons vivement de créer votre espace de travail Azure Machine Learning dans la même région que celle de votre Azure Kubernetes Service. Pour s’authentifier avec un jeton, le service web appelle la région dans laquelle votre espace de travail Azure Machine Learning est créé. Si la région de votre espace de travail est indisponible, vous ne pouvez pas extraire de jeton pour votre service web, même si votre cluster se trouve dans une région différente de celle de votre espace de travail. Cela a pour effet qu’Azure AD Authentication devient indisponible tant que la région de votre espace de travail n’est pas disponible. Par ailleurs, plus la distance entre la région de votre cluster et celle de votre espace de travail est élevée, plus l’extraction de jeton prend de temps.

## <a name="authorization"></a>Authorization

Vous pouvez créer plusieurs espaces de travail, et chacun d’eux peut être partagé par plusieurs personnes. Lorsque vous partagez un espace de travail, vous pouvez contrôler l’accès à celui-ci en attribuant les rôles suivants aux utilisateurs :

* Propriétaire
* Contributeur
* Lecteur

Le tableau suivant liste certaines des principales opérations de service Azure Machine Learning et les rôles qui peuvent les effectuer :

| Opérations de service Azure Machine Learning | Propriétaire | Contributeur | Lecteur |
| ---- |:----:|:----:|:----:|
| Créer un espace de travail | ✓ | ✓ | |
| Partager un espace de travail | ✓ | |  |
| Créer un calcul | ✓ | ✓ | |
| Joindre un calcul | ✓ | ✓ | |
| Attacher des magasins de données | ✓ | ✓ | |
| Exécuter une expérience | ✓ | ✓ | |
| Voir les exécutions/métriques | ✓ | ✓ | ✓ |
| Inscrire le modèle | ✓ | ✓ | |
| Créer une image | ✓ | ✓ | |
| Déployer un service web | ✓ | ✓ | |
| Voir les modèles/images | ✓ | ✓ | ✓ |
| Appeler un service web | ✓ | ✓ | ✓ |

Si les rôles intégrés ne répondent pas à vos besoins, vous pouvez également créer des rôles personnalisés. Les seuls rôles personnalisés que nous prenons en charge s’appliquent aux opérations sur l’espace de travail et la capacité de calcul Machine Learning. Les rôles personnalisés peuvent disposer d’autorisations en lecture, écriture ou suppression sur l’espace de travail et la ressource de calcul dans cet espace de travail. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique. Pour plus d’informations, consultez [Gérer des utilisateurs et des rôles dans un espace de travail Azure Machine Learning](how-to-assign-roles.md).

### <a name="securing-compute-and-data"></a>Sécurisation du calcul et des données

Les propriétaires et les contributeurs peuvent utiliser toutes les cibles de calcul et tous les magasins de données qui sont joints à l’espace de travail.  
Chaque espace de travail a également une identité managée affectée par le système associée (portant le même nom que l’espace de travail) avec les autorisations suivantes sur les ressources jointes utilisées dans l’espace de travail :

Pour plus d’informations sur les identités managées, consultez [Identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Ressource | Autorisations |
| ----- | ----- |
| Espace de travail | Contributeur |
| Compte de stockage | Contributeur aux données Blob du stockage |
| Key Vault | Accès à l’ensemble des clés, secrets et certificats |
| Azure Container Registry | Contributeur |
| Groupe de ressources contenant l’espace de travail | Contributeur |
| Groupe de ressources contenant le coffre de clés (s’il diffère de celui contenant l’espace de travail) | Contributeur |

Nous recommandons aux administrateurs de ne pas révoquer l’accès de l’identité managée pour les ressources mentionnées ci-dessus. L’accès peut être restauré avec l’opération Resynchroniser les clés.

Le service Azure Machine Learning crée une application supplémentaire (dont le nom commence par `aml-`) avec l’accès de niveau contributeur dans votre abonnement pour chaque région de l’espace de travail. Par exemple, si vous avez un espace de travail dans la région USA Est et un autre espace de travail dans la région Europe Nord dans le même abonnement, vous verrez deux de ces applications. C’est nécessaire pour que le service Azure Machine Learning permette de gérer les ressources de calcul.

## <a name="network-security"></a>Sécurité du réseau

Le service Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul. Les ressources de calcul (cibles de calcul) sont utilisées pour entraîner et déployer des modèles. Ces cibles de calcul peuvent être créées à l’intérieur d’un réseau virtuel. Par exemple, vous pouvez utiliser la machine virtuelle Microsoft Data Science Virtual Machine pour entraîner un modèle, puis déployer le modèle sur Azure Kubernetes Service (AKS).  

Pour plus d’informations, consultez le [Guide pratique pour exécuter des expériences et une inférence dans un réseau virtuel](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Chiffrement des données

### <a name="encryption-at-rest"></a>Chiffrement au repos

#### <a name="azure-blob-storage"></a>un stockage Azure Blob

Le service Azure Machine Learning stocke les captures instantanées, les sorties et les journaux dans le compte Stockage Blob Azure qui est lié à l’espace de travail du service Azure Machine Learning et qui se trouve dans l’abonnement de l’utilisateur. Toutes les données stockées dans Stockage Blob Azure sont chiffrées au repos à l’aide de clés gérées par Microsoft.

Pour plus d’informations sur la façon d’apporter vos propres clés pour les données stockées dans Stockage Blob Azure, consultez [Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

En général, les données d’entraînement sont également stockées dans Stockage Blob Azure afin d’être accessibles pour le calcul d’entraînement. Ce stockage n’est pas managé par Azure Machine Learning mais il est monté pour effectuer des calculs comme un système de fichiers distant.

Pour plus d’informations sur la regénération des clés d’accès pour les comptes de stockage Azure utilisés avec votre espace de travail, consultez l’article [Regénérer les clés d’accès de stockage](how-to-change-storage-access-key.md).

#### <a name="cosmos-db"></a>Cosmos DB

Le service Azure Machine Learning stocke les métriques et les métadonnées dans la base de données Cosmos DB qui réside dans un abonnement Microsoft géré par le service Azure Machine Learning. Toutes les données stockées dans Cosmos DB sont chiffrées au repos à l’aide de clés gérées par Microsoft.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

Toutes les images conteneur présentes dans votre registre (ACR) sont chiffrées au repos. Azure chiffre automatiquement une image avant de la stocker et la déchiffre à la volée quand le service Azure Machine Learning tire (pull) l’image.

#### <a name="machine-learning-compute"></a>Capacité de calcul Machine Learning

Le disque de système d’exploitation de chaque nœud de calcul est stocké dans le Stockage Azure qui est chiffré à l’aide de clés gérées par Microsoft dans les comptes de stockage du service Azure Machine Learning. Ce calcul est éphémère et les clusters font généralement l’objet d’un scale-down quand aucune exécution n’est placée en file d’attente. La machine virtuelle sous-jacente est déprovisionnée et le disque de système d’exploitation est supprimé. Le chiffrement de disque Azure n’est pas pris en charge pour le disque de système d’exploitation.
Chaque machine virtuelle dispose également d’un disque temporaire local pour les opérations de système d’exploitation. Ce disque peut également être utilisé de façon facultative pour organiser les données d’entraînement. Ce disque n’est pas chiffré.
Pour plus d’informations sur le fonctionnement du chiffrement au repos dans Azure, consultez [Chiffrement des données au repos d’Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Chiffrement en transit

La communication interne entre les différents microservices Azure Machine Learning et la communication externe de l’appel du point de terminaison de notation sont toutes les deux prises en charge à l’aide de SSL. Tout l’accès au stockage Azure s’effectue également sur un canal sécurisé.
Pour plus d’informations, consultez [Sécuriser les services web Azure Machine Learning avec SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilisation d’Azure Key Vault

L’instance Key Vault associée à l’espace de travail est utilisée par le service Azure Machine Learning pour stocker les informations d’identification de différents types :

* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données

Les mots de passe et clés SSH pour les cibles de calcul telles que HDI HDInsight et les machines virtuelles sont stockés dans un coffre de clés distinct qui est associé à l’abonnement Microsoft. Le service Azure Machine Learning ne stocke pas les mots de passe ou clés fournis par l’utilisateur. Au lieu de cela, il génère, autorise et stocke ses propres clés SSH afin de se connecter aux machines virtuelles/HDInsight pour exécuter les expériences.
Chaque espace de travail a une identité managée affectée par le système associée (portant le même nom que l’espace de travail) qui a accès à l’ensemble des clés, secrets et certificats dans le coffre de clés.

## <a name="monitoring"></a>Surveillance

Les utilisateurs peuvent consulter le journal d’activité sous l’espace de travail pour voir les diverses opérations effectuées sur l’espace de travail et obtenir les informations de base telles que le nom de l’opération, la personne qui a lancé l’événement, l’horodatage, etc.

La capture d’écran suivante montre le journal d’activité d’un espace de travail :

![Capture d’écran montrant un journal d’activité sous un espace de travail](./media/enterprise-readiness/workspace-activity-log.png)

Les détails des demandes de notation sont stockés dans AppInsights, qui est créé dans l’abonnement de l’utilisateur lors de la création de l’espace de travail. Cela inclut des champs comme HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, Duration, etc.

## <a name="data-flow-diagram"></a>Diagramme de flux de données

### <a name="create-workspace"></a>Créer un espace de travail

Le diagramme suivant montre le workflow de création d’un espace de travail.
L’utilisateur se connecte à Azure AD à partir de n’importe quel client de service Azure Machine Learning pris en charge (CLI, SDK Python, portail Azure) et demande le jeton Azure Resource Manager approprié. L’utilisateur appelle ensuite Azure Resource Manager pour créer l’espace de travail.  Azure Resource Manager contacte le fournisseur de ressources du service Azure Machine Learning pour provisionner l’espace de travail.  Des ressources supplémentaires sont créées dans l’abonnement du client lors de la création de l’espace de travail :

* Un coffre de clés (pour stocker les secrets)
* Un compte de Stockage Azure (dont les objets blob et le partage de fichiers)
* Azure Container Registry (pour stocker les images Docker pour l’inférence/la notation et l’expérimentation)
* Application Insights (pour stocker les données de télémétrie)

D’autres calculs attachés à un espace de travail (Azure Kubernetes Service, machines virtuelles, etc.) peuvent également être provisionnés par les clients en fonction des besoins.

![Capture d’écran montrant le workflow de création d’un espace de travail](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Enregistrer le code source (scripts d’entraînement)

Le diagramme suivant montre le workflow de capture instantanée du code.
Les répertoires (expériences), qui contiennent le code source (scripts d’entraînement) sont associés à un espace de travail de service Azure Machine Learning.  Ces répertoires sont stockés sur l’ordinateur local du client et dans le cloud (dans Stockage Blob Azure, sous l’abonnement du client). Ces captures instantanées du code sont utilisées pour l’exécution ou l’inspection de l’audit d’historique.

![Capture d’écran montrant le workflow de création d’un espace de travail](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Formation

Le diagramme suivant montre le workflow d’entraînement.

* Le service Azure Machine Learning est appelé avec l’ID de capture instantanée pour la capture instantanée du code enregistrée précédemment
* Le service Azure Machine Learning crée un ID d’exécution (facultatif) et un jeton de service Azure Machine Learning, qui est utilisé ultérieurement par les cibles de calcul comme la capacité de calcul Machine Learning/la machine virtuelle pour répondre au service Azure Machine Learning
* Vous pouvez choisir un calcul managé (par exemple, la capacité de calcul Machine Learning) ou un calcul non managé (par exemple, une machine virtuelle) pour exécuter vos travaux d’entraînement. Le flux de données est expliqué pour les deux scénarios ci-dessous :
* (Machines virtuelles/HDInsight : accès à l’aide des informations d’identification SSH dans Key Vault dans l’abonnement Microsoft) Le service Azure Machine Learning exécute le code de gestion sur la cible de calcul qui :

   1. Prépare l’environnement (Docker est également une option pour Machine virtuelle et Local. Pour comprendre le fonctionnement de l’exécution d’une expérience sur un conteneur Docker, voir les étapes suivantes relatives à la Capacité de calcul Machine Learning).
   1. Télécharge le code.
   1. Définit des variables et configurations d’environnement.
   1. Exécute le script utilisateur (capture instantanée du code mentionnée ci-dessus).

* (Capacité de calcul Machine Learning : accès à l’aide de l’identité managée de l’espace de travail) Notez qu’étant donné que la capacité de calcul Machine Learning est une capacité de calcul managée, c’est-à-dire qu’elle est gérée par Microsoft, elle s’exécute sous l’abonnement Microsoft.

   1. Une construction du Docker distant est démarrée si nécessaire.
   1. Écrit le code de gestion dans le partage de fichiers Azure de l’utilisateur.
   1. Démarre le conteneur avec une commande initiale, autrement dit, un code de gestion comme décrit à l’étape précédente.

#### <a name="querying-runs-and-metrics"></a>Interrogation des exécutions et des métriques

Cette étape est indiquée dans le flux où le calcul d’entraînement réécrit les *métriques d’exécution* dans le service Azure Machine Learning à partir duquel il est stocké dans la base de données Cosmos DB. Les clients peuvent appeler le service Azure Machine Learning qui, à son tour, extrait les métriques de la base de données Cosmos DB et les retourne au client.

![Capture d’écran montrant le workflow de création d’un espace de travail](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Création de services web

Le diagramme suivant montre le workflow d’inférence. L’inférence, ou notation du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production.
Consultez les détails ci-dessous :

* L’utilisateur inscrit un modèle à l’aide d’un client comme le SDK Azure ML
* L’utilisateur crée une image à l’aide du modèle, du fichier des scores et d’autres dépendances de modèle
* L’image Docker est créée et stockée dans ACR
* Le service web est déployé sur la cible de calcul (ACI/AKS) à l’aide de l’image créée ci-dessus
* Les détails des demandes de notation sont stockés dans AppInsights, qui se trouve dans l’abonnement de l’utilisateur
* Les données de télémétrie sont également envoyées (push) à l’abonnement Microsoft/Azure

![Capture d’écran montrant le workflow de création d’un espace de travail](./media/enterprise-readiness/inferencing.png)

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
