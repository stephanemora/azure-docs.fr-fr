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
ms.openlocfilehash: fb1f1d098970927ba04c840e77ec0a0b8d76ca02
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561316"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Sécurité et gouvernance de l’entreprise pour Azure Machine Learning

Dans cet article, vous allez découvrir les fonctionnalités de sécurité disponibles pour Azure Machine Learning.

Quand vous utilisez un service cloud, une bonne pratique consiste à limiter l’accès aux seuls utilisateurs qui en ont besoin. Commencez par comprendre le modèle d’authentification et d’autorisation utilisé par le service. Vous pouvez également restreindre l’accès réseau ou joindre de manière sécurisée des ressources de votre réseau local au cloud. Le chiffrement des données est également essentiel, aussi bien au repos et pendant le déplacement de données entre les services. Vous pouvez également créer des stratégies pour appliquer certaines configurations ou consigner les cas où des configurations non conformes sont créées. Enfin, vous devez pouvoir superviser le service et produire un journal d’audit de toutes les activités.

> [!NOTE]
> Les informations contenues dans cet article fonctionnent avec le kit de développement logiciel (SDK) Python Azure Machine Learning 1.0.83.1 ou version ultérieure.

## <a name="authentication--authorization"></a>Authentification et autorisation

La plupart des authentifications auprès de ressources Azure Machine Learning utilisent Azure Active Directory (Azure AD) pour l’authentification et le contrôle d’accès en fonction du rôle (RBAC Azure) pour l’autorisation. Les exceptions sont les suivantes :

* __SSH__ : vous pouvez activer l’accès SSH à certaines ressources de calcul, telles qu’une instance de calcul Azure Machine Learning. L’accès SSH utilise une authentification basée sur une clé. Pour plus d’informations sur la création de clés SSH, consultez [Créer et gérer des clés SSH](../virtual-machines/linux/create-ssh-keys-detailed.md). Pour plus d’informations sur l’activation de l’accès SSH, consultez [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md).
* __Modèles déployés en tant que services web__ : les déploiements de services web peuvent utiliser un contrôle d’accès basé sur une __clé__ ou un __jeton__. Les clés sont des chaînes statiques. Les jetons sont récupérés à l’aide d’un compte Azure AD. Pour plus d’informations, consultez [Configurer l’authentification pour des modèles déployés en tant que services web](how-to-authenticate-web-service.md).

Les services spécifiques dont dépend Azure Machine Learning, tels que les services de stockage de données Azure, disposent de leurs propres méthodes d’authentification et d’autorisation. Pour plus d’informations sur l’authentification des services de stockage, consultez [Se connecter aux services de stockage](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Authentification Azure AD

L’authentification multifacteur est prise en charge si Azure Active Directory (Azure AD) est configuré pour l’utiliser. Voici le déroulement du processus d’authentification :

1. Le client se connecte à Azure AD et obtient un jeton Azure Resource Manager.  Les utilisateurs et les principaux de service sont entièrement pris en charge.
1. Le client présente le jeton à Azure Resource Manager et à tout Azure Machine Learning.
1. Le service Machine Learning fournit un jeton de service Machine Learning à la cible de calcul utilisateur (par exemple, Capacité de calcul Machine Learning). Ce jeton est utilisé par la cible de calcul utilisateur pour rappeler le service Machine Learning une fois l’exécution terminée. L’étendue se limite à l’espace de travail.

[![Authentification dans Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Pour plus d’informations, consultez [Authentification pour espace de travail Azure Machine Learning](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>Azure RBAC

Vous pouvez créer plusieurs espaces de travail, et chacun d’eux peut être partagé par plusieurs personnes. Vous pouvez contrôler les fonctionnalités ou opérations de l’espace de travail auxquelles les utilisateurs peuvent accéder en affectant leur compte Azure AD à des rôles de RBAC Azure. Les rôles intégrés sont les suivants :

* Propriétaire
* Contributeur
* Lecteur

Les propriétaires et les contributeurs peuvent utiliser toutes les cibles de calcul et tous les magasins de données qui sont joints à l’espace de travail.  

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

Si les rôles prédéfinis ne répondent pas à vos besoins, vous pouvez créer des rôles personnalisés. Les rôles personnalisés contrôlent toutes les opérations au sein d’un espace de travail, telles que la création d’un calcul, l’envoi d’une exécution, l’inscription d’un magasin de banques ou le déploiement d’un modèle. Les rôles personnalisés peuvent avoir des autorisations de lecture, d’écriture ou de suppression sur les diverses ressources d’un espace de travail, telles que les clusters, les magasins de données, les modèles et les points de terminaison. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique. Pour plus d’informations, consultez [Gérer des utilisateurs et des rôles dans un espace de travail Azure Machine Learning](how-to-assign-roles.md).

> [!IMPORTANT]
> Azure Machine Learning dépend d’autres services Azure tels que Stockage Blob Azure et Azure Kubernetes Service. Chaque service Azure possède ses propres configurations de RBAC Azure. Pour atteindre le niveau de contrôle d’accès souhaité, il se peut que vous deviez appliquer les deux configurations de RBAC Azure pour Azure Machine Learning et celles pour les services utilisés avec Azure Machine Learning.

> [!WARNING]
> Azure Machine Learning est pris en charge avec la collaboration interentreprises Azure Active Directory, mais n’est pas actuellement pris en charge avec la collaboration entreprise-client Azure Active Directory.

### <a name="managed-identities"></a>Identités managées

Chaque espace de travail est également associé à une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) attribuée par le système, qui porte le même nom que l’espace de travail. L’identité managée est utilisée pour accéder en toute sécurité aux ressources qu’utilise l’espace de travail. Elle dispose des autorisations suivantes sur les ressources attachées :

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

Si vous le souhaitez, vous pouvez configurer vos propres identités managées pour une utilisation avec Machines virtuelles Azure et un cluster de calcul Azure Machine Learning. Avec une machine virtuelle, l’identité managée permet d’accéder à votre espace de travail à partir du Kit de développement logiciel (SDK) au lieu du compte Azure AD de l’utilisateur. Avec un cluster de calcul, l’identité managée permet d’accéder à des ressources telles que des magasins de stockage sécurisés auxquels l’utilisateur exécutant le travail d’apprentissage n’a peut-être pas accès. Pour plus d’informations, consultez [Authentification pour espace de travail Azure Machine Learning](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Sécurité et isolation de réseau

Pour restreindre l’accès physique aux ressources Azure Machine Learning, vous pouvez utiliser Azure Virtual Network (VNet). Les réseaux virtuels vous permettent de créer des environnements réseau partiellement ou entièrement isolés de l’Internet public. Cela a pour effet de réduire la surface d’attaque de votre solution, ainsi que les risques d’exfiltration de données.

Pour plus d’informations, consultez les documents suivants :

* [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md)
* [Sécuriser les ressources d’espace de travail](how-to-secure-workspace-vnet.md)
* [Sécuriser l’environnement d’entraînement](how-to-secure-training-vnet.md)
* [Sécuriser l’environnement d’inférence](how-to-secure-inferencing-vnet.md)
* [Utiliser le studio dans un réseau virtuel sécurisé](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Chiffrement des données

Azure Machine Learning utilise série de ressources de calcul et de magasins de données. Pour en savoir plus sur la façon dont ceux-ci prennent en charge le chiffrement de données au repos et en transit, consultez [Chiffrement des données avec Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Données générées par Microsoft

Lorsque vous utilisez des services comme Machine Learning automatisé, Microsoft peut générer des données prétraitées temporaires pour l’apprentissage de plusieurs modèles. Ces données sont stockées dans un magasin de données de votre espace de travail, ce qui vous permet d’appliquer des contrôles d'accès et un chiffrement appropriés.

Vous pouvez aussi chiffrer des [informations de diagnostic enregistrées à partir de votre point de terminaison déployé](how-to-enable-app-insights.md) dans votre instance d’Azure Application Insights.

## <a name="monitoring"></a>Surveillance

Il existe plusieurs scénarios de surveillance avec Azure Machine Learning, en fonction du rôle et de l’objet de la surveillance.

| Role | Surveillance d’utilisation | Description |
| ---- | ----- | ----- |
| Admin, DevOps, MLOps | [Métriques Azure Monitor](#azure-monitor), [journal d’activité](#activity-log), [analyse des vulnérabilités](#vulnerability-scanning) | Informations sur le niveau de service |
| Scientifique des données. MLOps | [Surveiller les exécutions](#monitor-runs) | Informations consignées lors des exécutions de formation |
| MLOps | [Collecter les données de modèle](how-to-enable-data-collection.md), [Surveiller avec Application Insights](how-to-enable-app-insights.md) | Informations consignées par les modèles déployés en tant que services web ou modules IoT Edge|

### <a name="monitor-runs"></a>Surveiller les exécutions

Vous pouvez surveiller des expérimentations dans Azure Machine Learning, y compris des informations de journalisation à partir de vos scripts d’apprentissage. Vous pouvez consulter ces informations via le Kit de développement logiciel (SDK), Azure CLI et Studio. Pour plus d’informations, consultez les articles suivants :

* [Démarrer, superviser et annuler des exécutions d’entraînement](how-to-manage-runs.md)
* [Activer les journaux d’activité](how-to-track-experiments.md)
* [Afficher les journaux d’activité](how-to-monitor-view-training-logs.md)
* [Visualiser les exécutions avec TensorBoard](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Vous pouvez utiliser des métriques Azure Monitor pour voir et superviser les métriques de votre espace de travail Azure Machine Learning. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre espace de travail, puis sélectionnez **Métriques** :

[![Capture d’écran montrant des exemples de métriques pour un espace de travail](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Les métriques incluent des informations sur les exécutions, les déploiements et les inscriptions.

Pour plus d’informations, consultez [Mesures dans Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

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

Azure Security Center fournit des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides. Pour Azure Machine Learning, vous devez activer l’analyse de vos ressources Azure Container Registry et Azure Container Service. Pour plus d’informations, consultez [Analyse d’images Azure Container Registry par Security Center](../security-center/defender-for-container-registries-introduction.md) et [Intégration d’Azure Kubernetes Service à Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditer et gérer la conformité

[Azure Policy](../governance/policy/index.yml) est un outil de gouvernance qui vous permet de vous assurer que les ressources Azure sont conformes à vos stratégies. Avec Azure Machine Learning, vous pouvez attribuer les stratégies suivantes :

* **Clé gérée par le client** : auditez ou appliquez une valeur indiquant si les espaces de travail doivent utiliser une clé gérée par le client.
* **Liaison privée** : Auditer si les espaces de travail utilisent un point de terminaison privé pour communiquer avec un réseau virtuel.

Pour plus d’informations sur Azure Policy, consultez la [documentation relative à Azure Policy](../governance/policy/overview.md).

Pour plus d’informations sur les stratégies spécifiques à Azure Machine Learning, consultez [Audit et gestion de la conformité avec Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Verrous de ressources

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les services web Azure Machine Learning avec TLS](how-to-secure-web-service.md)
* [Utiliser un modèle Machine Learning déployé en tant que service web](how-to-consume-web-service.md)
* [Utiliser Azure Machine Learning avec un pare-feu Azure](how-to-access-azureml-behind-firewall.md)
* [Utiliser Azure Machine Learning avec un réseau virtuel Azure](how-to-network-security-overview.md)
* [Chiffrement au repos et en transit](concept-data-encryption.md)
* [Générer une API de recommandation en temps réel sur Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
