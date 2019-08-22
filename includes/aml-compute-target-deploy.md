---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: c5a6f957cd3f799f9d7eff1df22df2200779ac30
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946034"
---
| Cible de calcul | Usage | Prise en charge GPU | Prise en charge FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Service&nbsp;web&nbsp;local](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/débogage | Peut-être | &nbsp; | Convient pour les tests et la résolution des problèmes limités. L’accélération matérielle dépend de l’utilisation de bibliothèques dans le système local.
| [Service&nbsp;web&nbsp;de machine virtuelle Notebook](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Test/débogage | Peut-être | &nbsp; | Convient pour les tests et la résolution des problèmes limités. 
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inférence en temps réel |  [Oui](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Oui](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Convient aux déploiements de production à grande échelle. Fournit un temps de réponse et une mise à l’échelle automatique rapides du service déployé. La mise à l’échelle automatique du cluster n’est pas pris en charge via le Kit de développement logiciel (SDK) Azure Machine Learning. Pour modifier les nœuds du cluster AKS, utilisez l’interface utilisateur de votre cluster AKS dans le portail Azure. AKS est la seule option disponible pour l’interface visuelle. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Développement ou test | &nbsp;  | &nbsp; | Adaptées à petite échelle, charges de travail en fonction du processeur nécessitant < 48 Go de RAM |
| [Capacité de calcul Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Préversion) Inférence&nbsp;Batch | Oui | &nbsp;  | Exécutez le scoring par lots sur un calcul sans serveur. Prend en charge des machines virtuelles normales et basse priorité. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Préversion) Module&nbsp;IoT |  &nbsp; | &nbsp; | Déployez et servez des modèles sur des appareils IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | Oui | Déployez et servez des modèles sur des appareils IoT. |
