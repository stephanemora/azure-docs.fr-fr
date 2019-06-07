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
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753103"
---
| Cible de calcul | Usage | Prise en charge GPU | Prise en charge FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Service web local](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Test/debug | Peut-être | &nbsp; | Convient pour les tests et la résolution des problèmes limitée. L’accélération matérielle dépend de l’utilisation de bibliothèques dans le système local.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Inférence en temps réel |  [yes](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [yes](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Convient aux déploiements de production à grande échelle. Fournit une mise à l’échelle et les temps de réponse rapides.  AKS est la seule option disponible pour l’interface visuelle. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Développement ou test | &nbsp;  | &nbsp; | Adapté à petite échelle, les charges de travail en fonction du processeur nécessitant < 48 Go de RAM |
| [Capacité de calcul Azure Machine Learning](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Version préliminaire) Inférence de lot | Oui | &nbsp;  | Exécuter le calcul sans serveur de notation par lots. Prend en charge des machines virtuelles normale et basse priorité. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Version préliminaire) Module IoT |  &nbsp; | &nbsp; | Déployer et traiter les modèles ML sur les appareils IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | Oui | Déployer et traiter les modèles ML sur les appareils IoT. |