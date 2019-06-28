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
ms.openlocfilehash: a463ac9f9584cb13cadbcf79674d56b2f8e47c2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753103"
---
| Cible de calcul | Usage | Prise en charge GPU | Prise en charge FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Service web local](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/débogage | Peut-être | &nbsp; | Convient pour les tests et la résolution des problèmes limités. L’accélération matérielle dépend de l’utilisation de bibliothèques dans le système local.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inférence en temps réel |  [Oui](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Oui](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Convient aux déploiements de production à grande échelle. Fournit la mise à l’échelle automatique et des temps de réponse rapides.  AKS est la seule option disponible pour l’interface visuelle. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Développement ou test | &nbsp;  | &nbsp; | Adaptées à petite échelle, charges de travail en fonction du processeur nécessitant < 48 Go de RAM |
| [Capacité de calcul Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Préversion) Inférence par lots | Oui | &nbsp;  | Exécutez le scoring par lots sur un calcul sans serveur. Prend en charge des machines virtuelles normales et basse priorité. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Préversion) Module IoT |  &nbsp; | &nbsp; | Déployez et servez des modèles sur des appareils IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | Oui | Déployez et servez des modèles sur des appareils IoT. |