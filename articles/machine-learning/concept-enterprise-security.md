---
title: Sécurité d’entreprise
titleSuffix: Azure Machine Learning
description: 'Utilisez de manière sécurisée Azure Machine Learning : authentification, autorisation, sécurité réseau, chiffrement des données et supervision.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/17/2019
ms.openlocfilehash: 4b2f9e7f12b468f12fcfbe1b0af5c2918aa6c6ad
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535517"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Sécurité de l’entreprise pour Azure Machine Learning

Dans cet article, vous allez découvrir les fonctionnalités de sécurité disponibles pour Azure Machine Learning.

Quand vous utilisez un service cloud, une bonne pratique consiste à limiter l’accès aux seuls utilisateurs qui en ont besoin. Commencez par comprendre le modèle d’authentification et d’autorisation utilisé par le service. Vous pouvez également restreindre l’accès réseau ou joindre de manière sécurisée des ressources de votre réseau local au cloud. Le chiffrement des données est également essentiel, aussi bien au repos et pendant le déplacement de données entre les services. Enfin, vous devez pouvoir superviser le service et produire un journal d’audit de toutes les activités.

## <a name="authentication"></a>Authentication

L’authentification multifacteur est prise en charge si Azure Active Directory (Azure AD) est configuré pour l’utiliser. Voici le déroulement du processus d’authentification :

1. Le client se connecte à Azure AD et obtient un jeton Azure Resource Manager.  Les utilisateurs et les principaux de service sont entièrement pris en charge.
1. Le client présente le jeton à Azure Resource Manager et à tout Azure Machine Learning.
1. Le service Machine Learning fournit un jeton de service Machine Learning à la cible de calcul utilisateur (par exemple, Capacité de calcul Machine Learning). Ce jeton est utilisé par la cible de calcul utilisateur pour rappeler le service Machine Learning une fois l’exécution terminée. L’étendue se limite à l’espace de travail.

[![Authentification dans Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Consultez le guide pratique de [configuration de l’authentification](how-to-setup-authentication.md), où vous trouverez des exemples et instructions détaillés pour configurer l’authentification, y compris l’authentification du principal de service pour les workflows automatisés.

### <a name="authentication-for-web-service-deployment"></a>Authentification pour le déploiement de service web

Azure Machine Learning prend en charge deux formes d’authentification pour les services web : la clé et le jeton. Chaque service web ne peut activer qu’une seule forme d’authentification à la fois.

|Méthode d'authentification|Description|Azure Container Instances|AKS|
|---|---|---|---|
|Clé|Les clés sont statiques et n’ont pas besoin d’être actualisées. Elles peuvent être regénérées manuellement.|Désactivée par défaut| Activée par défaut|
|par jeton|Les jetons expirent après un laps de temps spécifié et doivent ensuite être actualisés.| Non disponible| Désactivée par défaut |

Consultez la section [Authentification de service web](how-to-setup-authentication.md#web-service-authentication) pour obtenir des exemples de code sur l’authentification auprès de services web dans Azure Machine Learning.

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
| Mettre à niveau l’espace de travail vers l’édition Enterprise | ✓ | |
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

Si les rôles prédéfinis ne répondent pas à vos besoins, vous pouvez créer des rôles personnalisés. Les rôles personnalisés sont pris en charge uniquement pour les opérations effectuées sur l’espace de travail et la capacité de calcul Machine Learning. Les rôles personnalisés peuvent disposer d’autorisations en lecture, écriture ou suppression sur l’espace de travail et sur la ressource de calcul dans cet espace de travail. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique. Pour plus d’informations, consultez [Gérer des utilisateurs et des rôles dans un espace de travail Azure Machine Learning](how-to-assign-roles.md).

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

Azure Machine Learning crée une application supplémentaire (dont le nom commence par `aml-` ou `Microsoft-AzureML-Support-App-`) avec l’accès de niveau contributeur dans votre abonnement pour chaque région de l’espace de travail. Par exemple, si vous avez un espace de travail dans la région USA Est et un autre espace de travail dans la région Europe Nord au sein du même abonnement, vous voyez deux de ces applications. Ces applications permettent à Azure Machine Learning de vous aider à gérer les ressources de calcul.

## <a name="network-security"></a>Sécurité du réseau

Azure Machine Learning s’appuie sur d’autres services Azure pour les ressources de calcul. Les ressources de calcul (cibles de calcul) sont utilisées pour entraîner et déployer des modèles. Vous pouvez créer ces cibles de calcul dans un réseau virtuel. Par exemple, vous pouvez utiliser Data Science Virtual Machine pour entraîner un modèle, puis le déployer sur AKS.  

Pour plus d’informations, consultez le [Guide pratique pour exécuter des expériences et une inférence dans un réseau virtuel](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Chiffrement des données

### <a name="encryption-at-rest"></a>Chiffrement au repos

#### <a name="azure-blob-storage"></a>Stockage Blob Azure

Azure Machine Learning stocke les captures instantanées, les sorties et les journaux dans le compte Stockage Blob Azure qui est lié à l’espace de travail Azure Machine Learning et à votre abonnement. Toutes les données stockées dans Stockage Blob Azure sont chiffrées au repos à l’aide de clés gérées par Microsoft.

Pour plus d’informations sur la façon d’utiliser vos propres clés pour les données stockées dans Stockage Blob Azure, consultez [Chiffrement de Stockage Azure à l’aide de clés gérées par le client dans Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

En général, les données d’entraînement sont également stockées dans Stockage Blob Azure afin d’être accessibles pour les cibles de calcul d’entraînement. Ce stockage n’est pas managé par Azure Machine Learning mais il est monté sur des cibles de calcul en tant que système de fichiers distant.

Pour plus d’informations sur la regénération des clés d’accès pour les comptes de stockage Azure utilisés avec votre espace de travail, consultez [Regénérer des clés d’accès de stockage](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning stocke les métriques et les métadonnées dans l’instance Azure Cosmos DB associée à un abonnement Microsoft géré par Azure Machine Learning. Toutes les données stockées dans Azure Cosmos DB sont chiffrées au repos à l’aide de clés gérées par Microsoft.

#### <a name="azure-container-registry"></a>Azure Container Registry

Toutes les images conteneur dans votre registre ( Azure Container Registry) sont chiffrées au repos. Azure chiffre automatiquement une image avant de la stocker et la déchiffre à la volée quand Azure Machine Learning tire (pull) l’image.

#### <a name="machine-learning-compute"></a>Capacité de calcul Machine Learning

Le disque de système d’exploitation de chaque nœud de calcul stocké dans Stockage Azure est chiffré à l’aide de clés gérées par Microsoft dans les comptes de stockage Azure Machine Learning. Cette cible de calcul est éphémère et les clusters font généralement l’objet d’un scale-down quand aucune exécution n’est placée en file d’attente. La machine virtuelle sous-jacente est déprovisionnée et le disque de système d’exploitation supprimé. Azure Disk Encryption n’est pas pris en charge pour le disque de système d’exploitation.

Chaque machine virtuelle dispose également d’un disque temporaire local pour les opérations de système d’exploitation. Si vous le souhaitez, vous pouvez utiliser le disque pour indexer les données d’entraînement. Le disque n’est pas chiffré.
Pour plus d’informations sur le fonctionnement du chiffrement au repos dans Azure, consultez [Chiffrement des données Azure au repos](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Chiffrement en transit

Vous pouvez utiliser SSL pour sécuriser les communications internes entre les microservices Azure Machine Learning et pour sécuriser les appels externes au point de terminaison de scoring. Tout l’accès au stockage Azure se produit également sur un canal sécurisé.

Pour plus d’informations, consultez [Utiliser SSL pour sécuriser un service web par le biais d’Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Utilisation d’Azure Key Vault

Azure Machine Learning utilise l’instance Azure Key Vault associée à l’espace de travail pour stocker les informations d’identification de différents types :

* Chaîne de connexion du compte de stockage associé
* Mots de passe pour les instances Azure Container Repository
* Chaînes de connexion aux magasins de données

Les mots de passe et clés SSH des cibles de calcul telles que HDI HDInsight et les machines virtuelles sont stockés dans un coffre de clés distinct qui est associé à l’abonnement Microsoft. Azure Machine Learning ne stocke pas les mots de passe ni les clés fournis par les utilisateurs. Il génère, autorise et stocke plutôt ses propres clés SSH pour se connecter aux machines virtuelles et à HDInsight afin d’exécuter les expériences.

Chaque espace de travail est associé à une identité managée attribuée par le système qui porte le même nom que l’espace de travail. Cette identité managée a accès à l’ensemble des clés, secrets et certificats contenus dans le coffre de clés.

## <a name="monitoring"></a>Surveillance

### <a name="metrics"></a>Mesures

Vous pouvez utiliser des métriques Azure Monitor pour voir et superviser les métriques de votre espace de travail Azure Machine Learning. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail, puis sélectionnez **Métriques** :

[![Capture d’écran montrant des exemples de métriques pour un espace de travail](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Les métriques incluent des informations sur les exécutions, les déploiements et les inscriptions.

Pour plus d’informations, consultez [Mesures dans Azure Monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Journal d’activité

Vous pouvez afficher le journal d’activité d’un espace de travail pour voir les différentes opérations qui y sont exécutées. Le journal contient des informations de base comme le nom de l’opération, l’initiateur de l’événement et l’horodatage.

Cette capture d’écran montre le journal d’activité d’un espace de travail :

[![Capture d’écran montrant le journal d’activité d’un espace de travail](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Les détails des requêtes de scoring sont stockés dans Application Insights. Application Insights est créé dans votre abonnement quand vous créez un espace de travail. Les informations consignées inclut des champs comme HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId et Duration.

> [!IMPORTANT]
> Certaines actions dans l’espace de travail Azure Machine Learning ne consignent pas d’informations dans le journal d’activité. Par exemple, le départ de l’exécution d’entraînement et l’inscription d’un modèle ne sont pas consignés.
>
> Certaines de ces actions apparaissent dans la zone **Activités** de votre espace de travail, mais ces notifications n’indiquent pas qui a lancé l’activité.

## <a name="data-flow-diagrams"></a>Diagrammes de flux de données

### <a name="create-workspace"></a>Créer un espace de travail

Le diagramme suivant montre le workflow de création d’un espace de travail.

* L’utilisateur se connecte à Azure AD à partir de l’un des clients Azure Machine Learning pris en charge (Azure CLI, SDK Python, portail Azure) et demande le jeton Azure Resource Manager approprié.
* L’utilisateur appelle Azure Resource Manager pour créer l’espace de travail. 
* Azure Resource Manager contacte le fournisseur de ressources Azure Machine Learning pour provisionner l’espace de travail.

Des ressources supplémentaires sont créées dans l’abonnement de l’utilisateur lors de la création de l’espace de travail :

* Key Vault (pour stocker des secrets)
* Un compte de stockage Azure (dont les objets blob et le partage de fichiers)
* Azure Container Registry (pour stocker les images Docker pour l’inférence/le scoring et l’expérimentation)
* Application Insights (pour stocker les données de télémétrie)

L’utilisateur peut également provisionner d’autres cibles de calcul attachées à un espace de travail (comme Azure Kubernetes Service ou des machines virtuelles) selon les besoins.

[![Créer un workflow d’espace de travail](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Enregistrer le code source (scripts d’entraînement)

Le diagramme suivant montre le workflow de capture instantanée du code.

Des répertoires (expériences) qui contiennent le code source (scripts d’entraînement) sont associés à un espace de travail Azure Machine Learning. Ces scripts sont stockés sur votre machine locale et dans le cloud (dans le Stockage Blob Azure de votre abonnement). Les instantanés de code sont utilisés pour l’exécution ou l’inspection de l’audit d’historique.

[![Workflow de la capture instantanée de code](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Entrainement

Le diagramme suivant montre le workflow d’entraînement.

* Azure Machine Learning est appelé avec l’ID de capture instantanée pour la capture instantanée du code enregistrée dans la section précédente.
* Azure Machine Learning crée un ID d’exécution (facultatif) et un jeton de service Machine Learning, qui est utilisé par la suite par des cibles de calcul comme la Capacité de calcul Machine Learning/les machines virtuelles pour communiquer avec le service Machine Learning.
* Vous pouvez choisir une cible de calcul managée (comme Capacité de calcul Machine Learning) ou une cible de calcul non managée (comme des machines virtuelles) pour exécuter vos travaux d’entraînement. Voici les flux de données des deux scénarios :
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

[![Workflow de l’entraînement](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Création de services web

Le diagramme suivant montre le workflow d’inférence. L’inférence, ou scoring du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production.

Voici les détails :

* L’utilisateur inscrit un modèle à l’aide d’un client comme le SDK Azure Machine Learning.
* L’utilisateur crée une image à l’aide d’un modèle, d’un fichier de scores et d’autres dépendances de modèle.
* L’image Docker est créée et stockée dans Azure Container Registry.
* Le service web est déployé sur la cible de calcul (Container Instances/AKS) à l’aide de l’image créée à l’étape précédente.
* Les détails des requêtes de scoring sont stockés dans Application Insights, qui se trouve dans l’abonnement de l’utilisateur.
* Des données de télémétrie sont également envoyées (push) à l’abonnement Microsoft/Azure.

[![Workflow de l’inférence](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
* [Exécuter des prédictions par lots](how-to-run-batch-predictions.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
* [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Utiliser Azure Machine Learning avec un réseau virtuel Azure](how-to-enable-virtual-network.md)
* [Bonnes pratiques pour la création de systèmes de recommandation](https://github.com/Microsoft/Recommenders)
* [Générer une API de recommandation en temps réel sur Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
