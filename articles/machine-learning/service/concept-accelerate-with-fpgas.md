---
title: Présentation des FPGA et de Project Brainwave - Azure Machine Learning
description: Découvrez comment accélérer des modèles et des réseaux neuronaux profonds avec des FPGA sur Azure. Cet article présente les FGPA (Field-Programmable Gate Array) et explique comment Azure Machine Learning fournit une de l’intelligence artificielle (IA) en temps réel quand vous déployez votre modèle sur un FPGA Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: adcd812bc63f98e4cbde949946bf32a037c6d704
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158683"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Que sont FPGA et Project Brainwave ?

Cet article présente les FGPA (Field-Programmable Gate Array) et explique comment Azure Machine Learning fournit une de l’intelligence artificielle (IA) en temps réel quand vous déployez votre modèle sur un FPGA Azure.

Les FPGA contiennent un tableau de blocs logiques programmables et une hiérarchie d’interconnexions reconfigurables. Les interconnexions permettent la configuration de ces blocs de différentes façons après leur fabrication. Les FPGA offrent une combinaison de programmabilité et de performances à comparer à d’autres circuits intégrés.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs et CPU, GPU, et ASIC

![Comparaison FPGA Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processeur||Description|
|---|:-------:|------|
|Application Specific Integrated Circuit (Circuits intégrés spécifiques à une application)|ASIC|Les circuits personnalisés, comme les TPU (TensorFlow Processor Units) de Google, offrent la meilleure efficacité. Ils ne peuvent pas être reconfigurés au fil de vos besoins.|
|Field-Programmable Gate Array|FPGA|Les FPGA, comme ceux disponibles sur Azure, offrent des performances proches de celles des ASIC, mais ils sont flexibles et reconfigurables au fil du temps pour implémenter une nouvelle logique.|
|GPU (Graphics Processing Unit, Unité de traitement graphique)|GPU|Un choix courant pour les calculs en intelligence artificielle offrant des fonctionnalités de traitement parallèle, qui les rendent plus rapides que les UC pour le rendu des images.|
|UC (Unité centrale)|UC|Processeurs à usage général dont les performances ne sont pas idéales pour le traitement des graphiques et de la vidéo.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave sur Azure

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) est l’architecture matérielle économique de Microsoft, basée sur des appareils FPGA d’Intel, que les scientifiques des données et les développeurs utilisent pour accélérer les calculs d’intelligence artificielle en temps réel.  Cette architecture compatible avec les FPGA offre **performances**, **flexibilité** et **scalabilité**, et est disponible sur Azure.

**Les FPGA permettent d’obtenir une latence faible pour les requêtes d’inférence en temps réel.** Le traitement par lots signifie collecter une grande quantité de données et l’envoyer à un processeur afin d’améliorer l’utilisation du matériel. Le traitement par lots peut entraîner une latence, car davantage de données doivent être traitées, mais il peut améliorer le débit. Les implémentations par Projet Brainwave des unités de traitement neuronal ne nécessitent pas de traitement par lots ; ainsi, la latence peut être plusieurs fois inférieure à celle des UC et des GPU.

### <a name="reconfigurable-power"></a>Puissance reconfigurable
**Les FPGA peuvent être reconfigurés pour différents types de modèles de machine learning.** Cette souplesse permet d’accélérer les applications basées sur la précision numérique la plus optimale et le modèle de mémoire utilisé.

De nouvelles techniques Machine Learning sont développées de manière régulière et la conception matérielle de Project Brainwave évolue rapidement également. Étant donné que les FPGA sont reconfigurables, il est possible rester à jour avec la configuration requise des algorithmes IA en constante évolution.

### <a name="whats-supported-on-azure"></a>Ce qui est pris en charge sur Azure
**Microsoft Azure constitue le plus important investissement cloud au monde en matière de FPGA.** Vous pouvez exécuter Project Brainwave sur l’infrastructure de mise à l’échelle d’Azure.

Aujourd’hui, Project Brainwave prend en charge :
+ Les scénarios de classification et de reconnaissance d’images
+ Le déploiement de TensorFlow
+ DNN (réseaux neuronaux profonds) : ResNet 50, ResNet 152, VGG-16, SSD-VGG et DenseNet-121
+ Le matériel FPGA d’Intel 

En utilisant cette architecture matérielle compatible avec les FPGA, les réseaux neuronaux entraînés s’exécutent rapidement et avec une latence plus faible. Project Brainwave peut mettre en parallèle des réseaux neuronaux profonds préentraînés sur des FPGA pour faire monter en charge votre service. Les réseaux neuronaux profonds peuvent être préentraînés, en tant que caractériseurs profonds pour l’apprentissage du transfert, ou optimisés avec des pondérations mises à jour.

### <a name="scenarios-and-applications"></a>Scénarios et applications

Project Brainwave n’est pas intégré à Azure Machine Learning. Microsoft utilise des FPGA pour l’évaluation des réseaux neuronaux profonds, le classement des recherches Bing et l’accélération réseau définie par logiciel pour réduire la latence, tout en libérant les UC pour d’autres tâches.

Les scénarios suivants utilisent FPGA sur l’architecture Project Brainwave :
+ [Système d’inspection optique automatisé](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Cartographie de couverture terrestre](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Déployer sur des FPGA sur Azure

Voici le workflow pour créer un service de reconnaissance d’images dans Azure en utilisant des réseaux neuronaux profonds pris en charge comme caractériseur pour le déploiement sur des FPGA Azure :

1. Utilisez le SDK Azure Machine Learning pour Python pour créer une définition de service, qui est un fichier décrivant un pipeline de graphes (entrée, caractériseur et classifieur) basé sur TensorFlow. La commande de déploiement compresse automatiquement la définition et les graphes dans un fichier ZIP, puis charge ce fichier dans Stockage Blob Azure.  Le réseau neuronal profond est déjà déployé sur Project Brainwave pour s’exécuter sur le FPGA.

1. Inscrivez le modèle en utilisant le SDK avec le fichier ZIP dans Stockage Blob Azure.

1. Déployez le service avec le modèle inscrit en utilisant le SDK.

Vous pouvez commencer à déployer des modèles de réseaux neuronaux profonds entraînés sur des FPGA dans le cloud Azure en suivant cet article, **« [Déployer un modèle en tant que service web sur un FPGA](how-to-deploy-fpga-web-service.md) »**.


## <a name="next-steps"></a>Étapes suivantes

Consultez ces vidéos et ces blogs :

+ [Matériel hyperscale : ML à l’échelle en plus d’Azure + FPGA : Build 2018 (vidéo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Immersion dans le cloud configurable basé sur le FPGA Microsoft (vidéo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave pour l’IA en temps réel : page d’accueil du projet](https://www.microsoft.com/research/project/project-brainwave/)
