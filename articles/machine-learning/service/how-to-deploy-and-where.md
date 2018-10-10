---
title: Où déployer les modèles avec le service Azure Machine Learning | Microsoft Docs
description: Découvrez les différentes façons de déployer vos modèles en production à l’aide du service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961006"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Déployer des modèles avec le service Azure Machine Learning

Le service Azure Machine Learning propose plusieurs façons de déployer votre modèle entraîné. Dans ce document, découvrez comment déployer votre modèle en tant que service web dans le cloud Azure ou sur des appareils IoT Edge.

Vous pouvez déployer des modèles sur les cibles de calcul suivantes :

- ACI (Azure Container Instances)
- Azure Kubernetes Service (AKS)
- Azure IoT Edge
- FPGA (Field-Programmable Gate Array)

Le reste de ce document présente chacune de ces options en détail.

## <a name="azure-container-instances"></a>Azure Container Instances

Utilisez Azure Container Instances pour déployer vos modèles en tant que point de terminaison API REST si une ou plusieurs des conditions suivantes sont remplies :
- Vous recherchez un déploiement rapide pour noter et valider votre modèle. Le déploiement ACI est généralement terminé en moins de 5 minutes.
- Vous cherchez à déployer votre modèle dans un environnement de développement ou de test. ACI vous permet de déployer 20 groupes de conteneurs par abonnement. Pour plus d’informations, consultez le document [Disponibilité des régions et quotas pour Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Pour plus d’informations, consultez le document [Déployer un modèle sur Azure Container Instances](how-to-deploy-to-aci.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pour les scénarios de production à grande échelle, vous pouvez déployer votre modèle sur AKS (Azure Kubernetes Service). Vous pouvez utiliser un cluster AKS existant ou en créer un en utilisant le kit SDK Azure Machine Learning, l’interface CLI ou le portail Azure.

La création du cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Une fois créé, vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez créer un cluster lors du prochain déploiement.

Avec un déploiement sur AKS, vos services web bénéficient d’une mise à l’échelle automatique, d’une journalisation, d’une collecte des données de modèles et de temps de réponse rapides. 

Le processus de création d’un cluster AKS prend environ 20 minutes.

Pour plus d’informations, consultez le document [Déployer un modèle sur Azure Kubernetes Service](how-to-deploy-to-aks.md).

## <a name="azure-iot-edge"></a>Azure IoT Edge

Avec les appareils IoT, il est plus rapide d’effectuer un scoring sur l’appareil au lieu d’envoyer des données au cloud et d’attendre qu’un modèle hébergé dans le cloud retourne des données. Avec Azure IoT Edge, vous pouvez héberger votre modèle sur des appareils de périphérie. Déployez votre modèle sur IoT Edge si vous avez besoin d’une ou de plusieurs des fonctionnalités suivantes :
- Gérer les tâches prioritaires localement, même sans connexion cloud
- Utiliser des données générées trop volumineuses pour être extraites rapidement du cloud
- Bénéficier du traitement en temps réel via l’intelligence dans ou près des appareils locaux
- Prendre en compte les exigences liées à la confidentialité des données 

Pour plus d’informations, consultez le document [Déployer sur Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

Pour plus d’informations sur le service IoT Edge, consultez la [documentation Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a name="field-programmable-gate-arrays-fpga"></a>FPGA (Field-Programmable Gate Array)

Les modèles accélérés matériellement avec Project Brainwave permettent d’obtenir une très faible latence pour les demandes d’inférence en temps réel. Project Brainwave accélère les réseaux neuronaux profonds déployés sur des tableaux FPGA dans le cloud Azure. Les réseaux neuronaux profonds fréquemment utilisés sont disponibles en tant que caractériseurs pour l’apprentissage par transfert ou personnalisables avec des pondérations entraînées à partir de vos propres données.

Pour plus d’informations, consultez le document [Déployer sur FPGA](how-to-deploy-fpga-web-service.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de déployer un modèle sur une cible de calcul spécifique, consultez les documents suivants :

* [Déployer un modèle sur Azure Container Instances](how-to-deploy-to-aci.md)
* [Déployer un modèle sur Azure Kubernetes Service](how-to-deploy-to-aks.md)
* [Déployer un modèle sur Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Déployer un modèle sur FPGA](how-to-deploy-fpga-web-service.md)
