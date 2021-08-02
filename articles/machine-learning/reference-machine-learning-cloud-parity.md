---
title: Disponibilité des fonctionnalités dans les différentes régions cloud
titleSuffix: Azure Machine Learning
description: Cet article répertorie les différences de disponibilité des fonctionnalités entre le cloud public et les régions Azure Government, Azure Allemagne et Azure China 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 06/10/2021
ms.custom: references_regions
ms.openlocfilehash: 0e8c63827ba6e2e022e0d7876952b0a159c1e569
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984623"
---
# <a name="azure-machine-learning-feature-availability-across-clouds-regions"></a>Disponibilité des fonctionnalités Azure Machine Learning dans les différentes régions cloud

Découvrez les fonctionnalités Azure Machine Learning disponibles dans les régions Azure Government, Azure Allemagne et Azure China 21Vianet. 

Dans la liste des régions Azure à l’échelle internationale, il existe plusieurs régions qui servent des marchés spécifiques en plus des régions de cloud public. Par exemple, les régions Azure Government et Azure China 21Vianet. En plus des régions de cloud public, Azure Machine Learning est déployé dans les régions suivantes :

* Régions Azure Government **US-Arizona** et **US-Virginia**.
* Région Azure China 21Vianet **China-East-2**.

Les informations contenues dans le reste de ce document fournissent des informations sur les fonctionnalités Azure Machine Learning disponibles dans ces régions, ainsi que des informations relatives à l’utilisation de ces fonctionnalités spécifiques à la région.
## <a name="azure-government"></a>Azure Government 

| Fonctionnalité | État du cloud public  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **[Machine learning automatisé](concept-automated-ml.md)** | | | |
| Créer et exécuter des expériences dans des blocs-notes                                    | GA                   | YES                | YES         |
| Créer et exécuter des expériences dans l’expérience web Studio                        | Version préliminaire publique       | YES                | YES         |
| Fonctionnalités de prévision du secteur                                  | GA                   | YES                | YES         |
| Prise en charge de l’apprentissage profond et d’autres apprenants avancés                      | GA                   | YES                | YES         |
| Prise en charge de données volumineuses (jusqu’à 100 Go)                                          | Version préliminaire publique       | YES                | YES         |
| Intégration avec Azure Databricks                                              | GA                   | Non                 | Non          |
| Intégrations avec SQL, CosmosDB et HDInsight                                   | GA                   | YES                | YES         |
| **[Pipelines Machine Learning](concept-ml-pipelines.md)** |   |  | | 
| Créer, exécuter et publier des pipelines à l’aide du kit de développement logiciel (SDK) Azure Machine Learning                   | GA                   | YES                | YES         |
| Créer des points de terminaison de pipeline à l’aide du kit de développement logiciel (SDK) Azure Machine Learning                           | GA                   | YES                | YES         |
| Créer, modifier et supprimer des séries planifiées de pipelines à l’aide du kit de développement logiciel (SDK) Azure Machine Learning | GA                   | OUI*               | OUI*        |
| Afficher les détails de l’exécution du pipeline dans Studio                                        | GA                   | YES                | YES         |
| Créer, exécuter, visualiser et publier des pipelines dans le concepteur Azure Machine Learning          | GA      | YES                | YES         |
| Intégration avec Azure Databricks à l’aide d’un pipeline ML                             | GA                   | Non                 | Non          |
| Créer des points de terminaison de pipeline dans le concepteur Azure Machine Learning                             | GA      | YES                | YES         |
| **[Notebooks intégrés](how-to-run-jupyter-notebooks.md)** |   |  | | 
| Notebook et partage de fichiers de l’espace de travail                                        | GA                   | YES                | YES         |
| Prise en charge R et Python                                                       | GA                   | YES                | YES         |
| Prise en charge des réseaux virtuels                                                    | Version préliminaire publique       | Non                 | Non          |
| **[Instance de calcul](concept-compute-instance.md)** |   |  | | 
| Instances de calcul gérées pour les notebooks intégrés                         | GA                   | YES                | YES         |
| Intégration de Jupyter et JupyterLab                                            | GA                   | YES                | YES         |
| Prise en charge du réseau virtuel                                             | Version préliminaire publique       | YES                | YES         |
| **Prise en charge des Kits de développement logiciel (SDK)** |  |  | | 
| [Prise en charge du Kit de développement logiciel (SDK) R](https://azure.github.io/azureml-sdk-for-r/reference/index.html)                                                              | Version préliminaire publique       | YES                | YES         |
| [Prise en charge du Kit de développement logiciel (SDK) Python](/python/api/overview/azure/ml/)                                                         | GA                   | YES                | YES         |
| **[Sécurité](concept-enterprise-security.md)** |   | | | 
| Prise en charge du réseau virtuel (Vnet) pour la formation                                | GA                   | YES                | YES         |
| Prise en charge du réseau virtuel (Vnet) pour l’inférence                               | GA                   | YES                | YES         |
| Authentification du point de terminaison scoring                                            | Version préliminaire publique       | YES                | YES         |
| Point de terminaison privé d’espace de travail                                                 | Version préliminaire publique       | Non                 | Non          |
| ACI derrière réseau virtuel                                                            | Version préliminaire publique       | Non                 | Non          |
| ACR derrière réseau virtuel                                                            | Version préliminaire publique       | Non                 | Non          |
| Adresse IP privée du cluster AKS                                                  | Version préliminaire publique       | Non                 | Non          |
| **Calcul** |   | | |
| [Gestion de quota entre les espaces de travail](how-to-manage-quotas.md)                                         | GA                   | YES                | YES         |
| **[Données pour Machine Learning](concept-data.md)** |   | | |
| Créer, afficher ou modifier des jeux de données et des magasins de données à partir du kit de développement logiciel (SDK)                  | GA                   | YES                | YES         |
| Créer, afficher ou modifier des jeux de données et des magasins de données à partir de l’interface utilisateur                   | GA                   | YES                | YES         |
| Afficher, modifier ou supprimer des superviseurs de dérive de jeu de données à partir du kit de développement logiciel (SDK)                   | Version préliminaire publique       | YES                | YES         |
| Afficher, modifier ou supprimer des superviseurs de dérive de jeu de données à partir de l’interface utilisateur                    | Version préliminaire publique       | YES                | YES         |
| **Cycle de vie de Machine Learning** |   | | |
| [Profilage des modèles](how-to-deploy-profile-model.md)                                                            | GA                   | YES                | PARTIAL     |
| [Azure ML CLI 1.0](reference-azure-machine-learning-cli.md)     | GA                   | YES                | YES         |
| [Modèles accélérés matériellement basés sur les FPGA](how-to-deploy-fpga-web-service.md)                                     | GA                   | Non                 | Non          |
| [Intégration de Visual Studio Code](how-to-setup-vs-code.md)                                             | Version préliminaire publique       | Non                 | Non          |
| [Intégration à Event Grid](how-to-use-event-grid.md)                                                     | Version préliminaire publique       | Non                 | Non          |
| [Intégration d’Azure Stream Analytics à Azure Machine Learning](../stream-analytics/machine-learning-udf.md)               | Version préliminaire publique       | Non                 | Non          |
| **[Étiquetage](how-to-create-labeling-projects.md)** |   | | |
| Portail d’étiquetage de gestion de projet                                        | GA                   | YES                | YES         |
| Portail d’étiquetage                                                            | GA                   | YES                | YES         |
| Étiquetage à l’aide du personnel privé                                          | GA                   | YES                | YES         |
| Étiquetage assisté par ML (classification des images et détection des objets)           | Version préliminaire publique       | YES                | YES         |
| **[ML responsable](concept-responsible-ml.md)** |   | | |
| Explicabilité dans l’interface utilisateur                                                       | Version préliminaire publique       | Non                 | Non          |
| Boîte à outils SmartNoise de confidentialité différentielle                                    | OSS                  | Non                 | Non          |
| Balises personnalisées dans Azure Machine Learning pour implémenter des feuilles de données              | GA                   | Non                 | Non          |
| Intégration d’impartialité AzureML                                               | Version préliminaire publique       | Non                 | Non          |
| Kit de développement logiciel (SDK) d’interprétabilité                                                      | GA                   | YES                | YES         |
| **Entrainement** |   | | |
| [Streaming des journaux d’expérimentation](how-to-track-monitor-analyze-runs.md)                                              | GA                   | YES                | YES         |
| [Apprentissage par renforcement](how-to-use-reinforcement-learning.md)                                                     | Version préliminaire publique       | Non                 | Non          |
| [IU d’expérimentation](how-to-track-monitor-analyze-runs.md)                                                         | Version préliminaire publique                   | YES                | YES         |
| [Intégration .NET avec ML.NET 1.0](/dotnet/machine-learning/tutorials/object-detection-model-builder)                                                | GA                   | YES                | YES         |
| **Inférence** |   | | |
| [Inférence par lot](tutorial-pipeline-batch-scoring-classification.md)                                                          | GA                   | YES                | YES         |
| [Data Box Edge avec FPGA](how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)                                                    | Version préliminaire publique       | Non                 | Non          |
| **Autres** |   | | |
| [Open Datasets](/azure/open-datasets/samples)                                                              | Version préliminaire publique       | YES                | YES         |
| [Recherche cognitive personnalisée](how-to-deploy-model-cognitive-search.md)                                                    | Version préliminaire publique       | YES                | YES         |
| [Accélérateur de solution de nombreux modèles](https://github.com/microsoft/solution-accelerator-many-models)                                                                | Version préliminaire publique       | Non                 | Non          |


### <a name="azure-government-scenarios"></a>Scénarios Azure Government

| Scénario                                                    | US-Virginia | US-Arizona| Limites  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Configuration de la sécurité générale** |   | | |
| Communication réseau privé entre les services                                     | Non | Non | Aucune liaison privée actuellement | 
| Désactiver/Contrôler l’accès Internet (entrant et sortant) et un réseau virtuel spécifique | PARTIAL| PARTIAL   | ACR derrière réseau virtuel n’est pas disponible dans Azure Government - double vérification sur ACI | 
| Emplacement pour tous les services/ressources associés  | YES | YES |  |
| Chiffrement au repos et en transit.                                                 | YES | YES |  |
| Accès à la racine et au protocole SSH pour les ressources de calcul.                                          | YES | YES |  |
| Maintenir la sécurité des systèmes déployés (instances, points de terminaison, etc.), y compris la protection des points de terminaison, la mise à jour corrective et la journalisation |  PARTIAL|  PARTIAL |ACR derrière réseau virtuel et point de terminaison privé actuellement non disponibles |                                  
| Contrôler (désactiver/limiter/restreindre) l’utilisation de l’intégration ACI/AKS                    | PARTIAL| PARTIAL |ACR derrière réseau virtuel et point de terminaison privé actuellement non disponibles|
| Contrôle d’accès en fonction du rôle Azure (Azure RBAC) – Création de rôles personnalisés                           | YES | YES |  |
| Contrôler l’accès aux images ACR utilisées par le service Machine Learning (fourni par Azure et gérée ou personnalisée)  |PARTIAL|  PARTIAL | ACR derrière point de terminaison privé et réseau virtuel non pris en charge dans Azure Government |
| **Utilisation générale du service Machine Learning** |  | | |
| Possibilité de disposer d’un environnement de développement pour générer un modèle, effectuer l’apprentissage de ce modèle, l’héberger en tant que point de terminaison et l’utiliser via une application web     | YES | YES |  |
| Possibilité d’extraire des données depuis ADLS (Data Lake Storage)                                 |YES | YES |  |
| Possibilité d’extraire des données depuis Stockage Blob Azure                                       |YES | YES |  |



### <a name="other-azure-government-limitations"></a>Autres limitations d’Azure Government

* Pour les instances de calcul Azure Machine Learning, la possibilité d’actualiser un jeton au-delà de 24 heures n’est pas disponible dans Azure Government.
* Le profilage de modèle ne prend pas en charge 4 UC dans la région US-Arizona.   
* Les exemples de notebooks peuvent ne pas fonctionner dans Azure Government s’il leur faut accéder à des données publiques.
* Adresses IP : La commande CLI utilisée dans les instructions [VNet et tunneling forcé](how-to-secure-training-vnet.md#forced-tunneling) ne retourne pas de plages IP. Privilégiez les [plages d’adresses IP et étiquettes de service pour Azure Government](https://www.microsoft.com/download/details.aspx?id=57063).
* Pour les pipelines planifiés, nous fournissons également un mécanisme de déclenchement basé sur un objet blob. Ce mécanisme n’est pas pris en charge pour les espaces de travail CMK. Pour activer un déclencheur basé sur un objet blob pour les espaces de travail CMK, vous devez effectuer une configuration supplémentaire. Pour plus d’informations, consultez [Déclencher l’exécution d’un pipeline Machine Learning à partir d’une application logique](how-to-trigger-published-pipeline.md).
* Pare-feu : lorsque vous utilisez une région Azure Government, ajoutez les hôtes suivants à votre paramètre de pare-feu :

    * Pour l’Arizona, utilisez : `usgovarizona.api.ml.azure.us`
    * Pour la Virginie, utilisez : `usgovvirginia.api.ml.azure.us`
    * Pour les deux : `graph.windows.net` 


## <a name="azure-china-21vianet"></a>21Vianet Azure - Chine 

| Fonctionnalité                                       | État du cloud public | CH-East-2 | CH-North-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Machine learning automatisé** |    | | |
| Créer et exécuter des expériences dans des blocs-notes                                    | GA               | YES       | N/A        |
| Créer et exécuter des expériences dans l’expérience web Studio                        | Version préliminaire publique   | YES       | N/A        |
| Fonctionnalités de prévision du secteur                                  | GA               | YES       | N/A        |
| Prise en charge de l’apprentissage profond et d’autres apprenants avancés                      | GA               | YES       | N/A        |
| Prise en charge de données volumineuses (jusqu’à 100 Go)                                          | Version préliminaire publique   | YES       | N/A        |
| Intégration avec Azure Databricks                                              | GA               | Non        | N/A        |
| Intégrations avec SQL, CosmosDB et HDInsight                                   | GA               | YES       | N/A        |
| **Pipelines Machine Learning** |    | | |
| Créer, exécuter et publier des pipelines à l’aide du kit de développement logiciel (SDK) Azure Machine Learning                   | GA               | YES       | N/A        |
| Créer des points de terminaison de pipeline à l’aide du kit de développement logiciel (SDK) Azure Machine Learning                           | GA               | YES       | N/A        |
| Créer, modifier et supprimer des séries planifiées de pipelines à l’aide du kit de développement logiciel (SDK) Azure Machine Learning | GA               | YES       | N/A        |
| Afficher les détails de l’exécution du pipeline dans Studio                                        | GA               | YES       | N/A        |
| Créer, exécuter, visualiser et publier des pipelines dans le concepteur Azure Machine Learning          | GA  | YES       | N/A        |
| Intégration avec Azure Databricks à l’aide d’un pipeline ML                             | GA               | Non        | N/A        |
| Créer des points de terminaison de pipeline dans le concepteur Azure Machine Learning                             | GA   | YES       | N/A        |
| **Notebooks intégrés** |   | | |
| Notebook et partage de fichiers de l’espace de travail                                        | GA               | YES       | N/A        |
| Prise en charge R et Python                                                       | GA               | YES       | N/A        |
| Prise en charge des réseaux virtuels                                                    | Version préliminaire publique   | Non        | N/A        |
| **Instance de calcul** |    | | |
| Instances de calcul gérées pour les notebooks intégrés                         | GA               | Non        | N/A        |
| Intégration de Jupyter et JupyterLab                                            | GA               | YES       | N/A        |
| Prise en charge du réseau virtuel                                             | Version préliminaire publique   | YES       | N/A        |
| **Prise en charge des Kits de développement logiciel (SDK)** |    | | |
| Prise en charge du Kit de développement logiciel (SDK) R                                                              | Version préliminaire publique   | YES       | N/A        |
| Prise en charge du Kit de développement logiciel (SDK) Python                                                         | GA               | YES       | N/A        |
| **Sécurité** |   | | |
| Prise en charge du réseau virtuel (Vnet) pour la formation                                | GA               | YES       | N/A        |
| Prise en charge du réseau virtuel (Vnet) pour l’inférence                               | GA               | YES       | N/A        |
| Authentification du point de terminaison scoring                                            | Version préliminaire publique   | YES       | N/A        |
| Liaison privée de l’espace de travail                                                     | GA               | Non        | N/A        |
| ACI derrière réseau virtuel                                                            | Version préliminaire publique   | Non        | N/A        |
| ACR derrière réseau virtuel                                                            | Version préliminaire publique   | Non        | N/A        |
| Adresse IP privée du cluster AKS                                                  | Version préliminaire publique   | Non        | N/A        |
| **Calcul** |   | | |
| Gestion de quota entre les espaces de travail                                         | GA               | YES       | N/A        |
| **Données pour Machine Learning** | | | |
| Créer, afficher ou modifier des jeux de données et des magasins de données à partir du kit de développement logiciel (SDK)                  | GA               | YES       | N/A        |
| Créer, afficher ou modifier des jeux de données et des magasins de données à partir de l’interface utilisateur                   | GA               | YES       | N/A        |
| Afficher, modifier ou supprimer des superviseurs de dérive de jeu de données à partir du kit de développement logiciel (SDK)                   | Version préliminaire publique   | YES       | N/A        |
| Afficher, modifier ou supprimer des superviseurs de dérive de jeu de données à partir de l’interface utilisateur                    | Version préliminaire publique   | YES       | N/A        |
| **Cycle de vie de Machine Learning** |    | | |
| Profilage des modèles                                                            | GA               | PARTIAL   | N/A        |
| Extension Azure DevOps pour Machine Learning et Azure ML CLI         | GA               | YES       | N/A        |
| Modèles accélérés matériellement basés sur les FPGA                                     | GA               | Non        | N/A        |
| Intégration de Visual Studio Code                                             | Version préliminaire publique   | Non        | N/A        |
| Intégration à Event Grid                                                     | Version préliminaire publique   | YES       | N/A        |
| Intégration d’Azure Stream Analytics avec Azure Machine Learning               | Version préliminaire publique   | Non        | N/A        |
| **Étiquetage** |    | | |
| Portail d’étiquetage de gestion de projet                                        | GA               | YES       | N/A        |
| Portail d’étiquetage                                                            | GA               | YES       | N/A        |
| Étiquetage à l’aide du personnel privé                                          | GA               | YES       | N/A        |
| Étiquetage assisté par ML (classification des images et détection des objets)           | Version préliminaire publique   | YES       | N/A        |
| **ML responsable** |    | | |
| Explicabilité dans l’interface utilisateur                                                       | Version préliminaire publique   | Non        | N/A        |
| Boîte à outils SmartNoise de confidentialité différentielle                                    | OSS              | Non        | N/A        |
| Balises personnalisées dans Azure Machine Learning pour implémenter des feuilles de données              | GA               | Non        | N/A        |
| Intégration d’impartialité AzureML                                               | Version préliminaire publique   | Non        | N/A        |
| Kit de développement logiciel (SDK) d’interprétabilité                                                      | GA               | YES       | N/A        |
| **Entrainement** |    | | |
| Streaming des journaux d’expérimentation                                              | GA               | YES       | N/A        |
| Apprentissage par renforcement                                                     | Version préliminaire publique   | Non        | N/A        |
| IU d’expérimentation                                                         | GA               | YES       | N/A        |
| Intégration .NET avec ML.NET 1.0                                                | GA               | YES       | N/A        |
| **Inférence** |   | | |
| Inférence par lot                                                          | GA               | YES       | N/A        |
| Data Box Edge avec FPGA                                                    | Version préliminaire publique   | Non        | N/A        |
| **Autres** |    | | |
| Open Datasets                                                              | Version préliminaire publique   | YES       | N/A        |
| Recherche cognitive personnalisée                                                    | Version préliminaire publique   | YES       | N/A        |
| Nombreux modèles                                                                | Version préliminaire publique   | Non        | N/A        |



### <a name="other-azure-china-limitations"></a>Autres limitations d’Azure Chine

* Azure Chine dispose d’une référence SKU de machine virtuelle limitée, notamment pour la référence SKU GPU. Seule la famille NCv3 (V100) est disponible.
* Les points de terminaison de l’API REST sont différents d’Azure global. Utilisez le tableau suivant pour rechercher le point de terminaison de l’API REST pour les régions Azure Chine :

    | Point de terminaison REST                 | Azure global                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Plan de gestion | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Plan de données       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Les exemples de notebooks peuvent ne pas fonctionner s’il leur faut accéder à des données publiques.
* Plages d’adresse IP : La commande CLI utilisée dans les instructions [Tunneling forcé - VNet](how-to-secure-training-vnet.md#forced-tunneling) ne retourne pas de plages IP. Privilégiez les [plages d’adresses IP et étiquettes de service pour Azure Chine](https://www.microsoft.com//download/details.aspx?id=57062).
* La préversion des instances de calcul Azure Machine Learning n’est pas prise en charge dans un espace de travail où Liaison privée est activé, mais CI sera pris en charge lors du prochain déploiement de l’extension de service dans toutes les régions AML.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les régions dans lesquelles Azure Machine Learning est disponible, consultez [Produits par région](https://azure.microsoft.com/global-infrastructure/services/).
