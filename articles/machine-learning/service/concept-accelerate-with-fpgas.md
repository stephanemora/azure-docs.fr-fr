---
title: Qu’est-ce qu’un FPGA ? – Project Brainwave – Azure Machine Learning
description: Découvrez comment accélérer des modèles et des réseaux neuronaux profonds à l’aide de FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: service
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5ed94c3b750c927ec48959c12388bd22de3d3df4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261948"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Que sont FPGA et Project Brainwave ?

Cet article fournit une présentation de FPGA (field-programmable gate arrays) et la manière dont FPGA est intégré à Azure Machine Learning pour fournir une IA en temps réel.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs et CPU, GPU, et ASIC

Les FPGA contiennent un tableau de blocs de logique programmable, et une hiérarchie d’interconnexions reconfigurables qui permettent de configurer les blocs différemment après la fabrication.

Les FPGA fournissent une combinaison de programmabilité et de performances par rapport aux autres processeurs :

![Comparaison FPGA Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- Les **unités centrales (UC)** sont des processeurs à usage général. Les performances de l’UC ne sont pas idéales pour le traitement des graphiques et de la vidéo.
- Les **unités de traitement graphique (GPU)** offrent un traitement parallèle et sont un choix très répandu pour effectuer des calculs IA. Le traitement parallèle avec les GPU permet d’obtenir un rendu d’image plus rapide qu’avec les unités centrales.
- Les **circuits intégrés spécifiques à l’application (ASIC)**, comme les unités de processeur TensorFlow de Google, sont des circuits personnalisés. Bien que ces circuits intégrés offrent la plus grande efficacité, les ASIC sont inflexibles.
- Les **FPGA**, comme ceux disponibles sur Azure, offrent des performances proches de celles d’ASIC, mais offrent la possibilité d’une reconfiguration ultérieure.

Les FPGA se trouvent maintenant dans chaque nouveau serveur Azure. Microsoft utilise déjà des FPGA pour le classement de la recherche Bing, l’évaluation de réseau neuronal en profondeur (DNN) et l’accélération de mise en réseau définie par logiciel (SDN). Ces implémentations FPGA réduisent la latence tout en laissant les unités centrales libres d’exécuter d’autres tâches.

## <a name="project-brainwave-on-azure"></a>Project Brainwave sur Azure

Project Brainwave est une architecture matérielle qui est basée sur les appareils FPGA d’Intel et utilisée pour accélérer les calculs IA en temps réel. Avec cette architecture FPGA économique, un réseau neuronal formé peut être exécuté aussi rapidement que possible et avec une latence plus faible. Project Brainwave peut mettre en parallèle des réseaux neuronaux profonds préformés sur les FPGA pour une montée en charge de votre service.

- Performances

    Les FPGA permettent d’obtenir une latence faible pour les requêtes d’inférence en temps réel. Le traitement par lot signifie de diviser une requête en éléments plus petits et de les transmettre à un processeur pour améliorer l’utilisation du matériel. Le traitement par lot n’est pas efficace et peut provoquer une latence. Brainwave ne nécessite pas le traitement par lot. Par conséquent, la latence est 10 fois plus faible par rapport à une UC et une GPU.

- Flexibilité

    Les FPGA peuvent être reconfigurés pour différents types de modèles Machine Learning. Cette souplesse permet d’accélérer les applications basées sur la précision numérique la plus optimale et le modèle de mémoire utilisé.

    De nouvelles techniques Machine Learning sont développées de manière régulière et la conception matérielle de Project Brainwave évolue rapidement également. Étant donné que les FPGA sont reconfigurables, il est possible rester à jour avec la configuration requise des algorithmes IA en constante évolution.

- Scale

    Microsoft Azure constitue le plus important investissement cloud au monde en matière de FPGA. Vous pouvez exécuter Brainwave sur l’infrastructure d’échelle d’Azure.

Une préversion de Project Brainwave intégré à Azure Machine Learning est actuellement disponible. Et une préversion limitée est également disponible pour déployer Project Brainwave en périphérie afin que vous puissiez tirer parti de cette vitesse informatique dans vos propres entreprises et installations.

Dans la préversion actuelle, Brainwave est limité au déploiement TensorFlow et aux réseaux neuronaux ResNet50 sur du matériel Intel FPGA pour le classement et la reconnaissance d’images. La prise en charge de davantage de modèles de galerie et d’autres frameworks est prévue.

Les scénarios suivants utilisent FPGA sur l’architecture Project Brainwave :

- Système d’inspection optique automatisé. Consultez [IA en temps réel : Microsoft annonce une préversion de Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Mappage de couverture terrestre. Consultez [Utilisation de FPGA pour l’inférence de formation approfondie afin d’effectuer le mappage de couverture terrestre sur des téraoctets d’images aériennes](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Comment déployer un service web sur un FPGA ?

Le flux de haut niveau pour la création d’un service de reconnaissance d’image dans Azure à l’aide de ResNet50 comme générateur de fonctionnalités est le suivant :

1. ResNet50 est déjà déployé dans Brainwave. Vous pouvez créer d’autres graphes (entrée de date, classification, etc.) avec TensorFlow et définir un pipeline (entrée -> générer des fonctionnalités -> classer) à l’aide d’un fichier json de définition de service. Compressez la définition et les graphes dans un fichier zip et chargez le fichier zip vers le Stockage Blob Azure.
2. Enregistrez le modèle à l’aide de l’API Gestion des modèles Machine Learning Azure avec le fichier zip dans le Stockage Blob.
3. Déployez le service avec le modèle inscrit à l’aide de l’API Gestion des modèles Machine Learning Azure.

Pour en savoir plus sur ce processus, consultez l’article [Déployer un modèle en tant que service web sur un FPGA avec Azure Machine Learning](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Commencer à utiliser FPGA

Les modèles d’accélération matérielle pour Azure Machine Learning permettent de déployer des modèles DNN formés sur des FPGA dans le cloud Azure. Pour commencer, consultez :

- [Déployer un modèle en tant que service web sur un FPGA](how-to-deploy-fpga-web-service.md)
- [Modèles d’accélération matérielle pour Microsoft Azure Machine Learning basés sur Project Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Étapes suivantes

[Déployer un modèle en tant que service web sur un FPGA](how-to-deploy-fpga-web-service.md)

[Découvrez comment installer le kit SDK FPGA](reference-fpga-package-overview.md)

[Matériel hyperscale : ML à l’échelle en plus d’Azure + FPGA : Build 2018 (vidéo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[Immersion dans le cloud configurable basé sur le FPGA Microsoft (vidéo)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft dévoile Project Brainwave pour IA en temps réel](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)