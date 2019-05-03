---
title: Quelles sont les tableaux de portes à champ programmable (FPGA)
titleSuffix: Azure Machine Learning service
description: Découvrez comment accélérer des modèles et des réseaux neuronaux profonds avec des FPGA sur Azure. Cet article présente les FGPA (Field-Programmable Gate Array) et explique comment le service Azure Machine Learning fournit l’intelligence artificielle (IA) en temps réel quand vous déployez votre modèle sur un FPGA Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 648dc462477570a692eff588c558a18a3121e4e7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025254"
---
# <a name="what-are-field-programmable-gate-arrays-fpga"></a>Quelles sont les tableaux de portes à champ programmable (FPGA)

Cet article présente les FGPA (Field-Programmable Gate Array) et explique comment le service Azure Machine Learning fournit l’intelligence artificielle (IA) en temps réel quand vous déployez votre modèle sur un FPGA Azure.

Les FPGA contiennent un tableau de blocs logiques programmables et une hiérarchie d’interconnexions reconfigurables. Les interconnexions permettent la configuration de ces blocs de différentes façons après leur fabrication. Par rapport aux autres circuits intégrés, les FPGA offrent une combinaison de programmabilité et de performances.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs et CPU, GPU, et ASIC

Le diagramme et le tableau qui suivent comparent les FPGA et les autres processeurs.

![Diagramme de comparaison des FPGA du service Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processeur||Description|
|---|:-------:|------|
|Application Specific Integrated Circuit (Circuits intégrés spécifiques à une application)|ASIC|Les circuits personnalisés, comme les TPU (TensorFlow Processor Units) de Google, offrent la meilleure efficacité. Ils ne peuvent pas être reconfigurés selon vos besoins.|
|Field-Programmable Gate Array|FPGA|Les FPGA, comme ceux disponibles sur Azure, offrent des performances approchant celles des ASIC. Ils sont flexibles et reconfigurables, afin de pouvoir implémenter de nouvelles logiques.|
|GPU (Graphics Processing Unit, Unité de traitement graphique)|GPU|Un choix courant pour les calculs en intelligence artificielle. Les GPU offrent des fonctionnalités de traitement parallèle qui les rendent plus rapides que les UC pour le rendu des images.|
|UC (Unité centrale)|UC|Processeurs à usage général dont les performances ne sont pas idéales pour le traitement des images et de la vidéo.|

Des FPGA sur Azure sont basées sur les appareils FPGA d’Intel, les scientifiques des données et les développeurs utilisent pour accélérer les calculs d’intelligence artificielle en temps réel. Cette architecture compatible avec les FPGA offre performances, flexibilité et scalabilité, et est disponible sur Azure.

Les FPGA permettent d’obtenir une latence faible pour les requêtes d’inférence en temps réel. Les requêtes asynchrones (traitement par lot) ne sont pas nécessaires. Le traitement par lots peut entraîner une latence, car davantage de données doivent être traitées. Les implémentations des unités de traitement neuronaux ne nécessitent pas de traitement par lot ; la latence peut donc être autant de fois inférieure, par rapport aux processeurs GPU et UC.

### <a name="reconfigurable-power"></a>Puissance reconfigurable
Vous pouvez reconfigurer les FGPA pour différents types de modèles Machine Learning. Cette souplesse permet d’accélérer les applications basées sur la précision numérique la plus optimale et le modèle de mémoire utilisé. Étant donné que les FPGA sont reconfigurables, vous pouvez suivre l’évolution des besoins des algorithmes d’intelligence artificielle.

### <a name="whats-supported-on-azure"></a>Ce qui est pris en charge sur Azure
Microsoft Azure constitue le plus important investissement cloud au monde en matière de FPGA. FPGA sur Azure prend en charge :

+ Les scénarios de classification et de reconnaissance d’images
+ Le déploiement de TensorFlow
+ Réseaux neuronaux profonds : ResNet 50, ResNet 152, VGG-16, SSD-VGG et DenseNet-121
+ Le matériel FPGA d’Intel 

En utilisant cette architecture matérielle compatible avec les FPGA, les réseaux neuronaux entraînés s’exécutent rapidement et avec une latence plus faible. Azure peut paralléliser préformé les réseaux neuronaux profonds (DNN) sur des FPGA faire évoluer votre service. Les réseaux neuronaux profonds peuvent être préentraînés, en tant que caractériseurs profonds pour l’apprentissage du transfert, ou optimisés avec des pondérations mises à jour.

### <a name="scenarios-and-applications"></a>Scénarios et applications

Des FPGA Azure sont intégrés à Azure Machine Learning. Microsoft utilise des FPGA pour l’évaluation des réseaux neuronaux profonds, le classement des recherches Bing et l’accélération réseau définie par logiciel pour réduire la latence, tout en libérant les UC pour d’autres tâches.

Les scénarios suivants utilisent des FPGA :
+ [Système d’inspection optique automatisé](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Cartographie de couverture terrestre](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Déployer sur des FPGA sur Azure

Pour créer un service de reconnaissance d’images dans Azure, vous pouvez utiliser les réseaux neuronaux profonds pris en charge comme caractériseurs pour le déploiement sur les FPGA Azure :

1. Utilisez le [SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) pour créer une définition de service. Une définition de service est un fichier décrivant un pipeline de graphes (entrée, caractériseur et classifieur) basé sur TensorFlow. La commande de déploiement compresse automatiquement la définition et les graphes dans un fichier ZIP, puis charge ce fichier dans Stockage Blob Azure. Le réseau de neurones profond est déjà déployé pour s’exécuter sur le FPGA.

1. Inscrivez le modèle en utilisant le SDK avec le fichier ZIP dans Stockage Blob Azure.

1. Déployez le service avec le modèle inscrit avec le SDK.

Pour commencer à déployer des modèles de réseaux neuronaux profonds entraînés sur des FPGA dans le cloud Azure, consultez cet article, [Déployer un modèle en tant que service web sur un FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Étapes suivantes

Consultez ces vidéos et ces blogs :

+ [Matériel Hyperscale : ML à grande échelle sur Azure + FPGA : Build 2018 (vidéo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Immersion dans le cloud configurable basé sur le FPGA Microsoft (vidéo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave pour l’IA en temps réel : page d’accueil du projet](https://www.microsoft.com/research/project/project-brainwave/)
