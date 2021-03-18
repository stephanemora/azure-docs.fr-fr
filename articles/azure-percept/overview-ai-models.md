---
title: Modèles IA Azure Percept
description: Apprenez-en davantage sur les modèles d’intelligence artificielle disponibles pour le prototypage et le déploiement.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: d0cdabb3b22d642a7903810181106b09c549e1a2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218160"
---
# <a name="azure-percept-ai-models"></a>Modèles IA Azure Percept

Azure Percept vous permet de développer et de déployer des modèles IA directement dans votre Azure Percept DK à partir d’[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Le déploiement de modèle utilise [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) et [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>Exemples de modèles IA

Azure Percept Studio contient des exemples de modèles pour les applications suivantes :

- détection de personnes
- détection de véhicules
- détection d’objet générale
- détection de produits en rayon

Avec les modèles préentraînés, aucune programmation ou collecte de données d’entraînement n’est nécessaire. Il vous suffit de déployer le modèle souhaité sur votre Azure Percept DK à partir du portail et d’ouvrir le flux vidéo de votre devkit pour voir l’inférence de modèle en action. La télémétrie d’inférence de modèle est également accessible par le biais de l’outil [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="reference-solutions"></a>Solutions de référence

Une [solution de référence pour le comptage des personnes](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) est également disponible. Cette solution de référence est une application d’IA open source qui permet le comptage des personnes en fonction de la périphérie, avec des événements d’entrée et de sortie de zone définis par l’utilisateur. La sortie vidéo et IA de l’appareil de périphérie local est envoyée à [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/), tandis que l’interface utilisateur s’exécute en tant que site web Azure. L’inférence IA est fournie par un modèle d’intelligence artificielle open source pour la détection de personnes.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="Image gif de solution prédéfinie d’analytique spatiale.":::

## <a name="custom-no-code-solutions"></a>Solutions sans code personnalisées

Grâce à Azure Percept Studio, vous pouvez développer des solutions de [vision](./tutorial-nocode-vision.md) et de synthèse vocale personnalisées. Aucune programmation n’est nécessaire.

Pour les solutions de vision personnalisées, des modèles IA de détection d’objets et de classification sont disponibles. Il vous suffit de charger et d’étiqueter vos images d’entraînement, qui peuvent être prises directement avec le SoM Azure Percept Vision d’Azure Percept DK, si vous le souhaitez. L’entraînement et l’évaluation du modèle sont faciles à effectuer dans [Custom vision](https://www.customvision.ai/), qui fait partie d’[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview).

Pour les solutions de synthèse vocale personnalisées, des modèles d’assistant vocal sont actuellement disponibles pour les applications suivantes :

- Hôtellerie : chambre d’hôtel équipée d’appareils intelligents à commande vocale.
- Santé : établissement de soins équipé d’appareils intelligents à commande vocale.
- Inventaire : hub d’inventaire équipé d’appareils intelligents à commande vocale.
- Automobile : hub automobile équipé d’appareils intelligents à commande vocale.

Des commandes et des mots clés d’assistant vocal prédéfinis sont disponibles directement par le biais du portail. Des commandes et des mots clés personnalisés peuvent être créés et entraînés dans [Speech Studio](https://speech.microsoft.com/), qui fait également partie d’Azure Cognitive Services.

## <a name="advanced-development"></a>Développement avancé

Pour des conseils, des tutoriels et des exemples à jour, consultez le [GitHub de développement avancé Azure Percept DK](https://github.com/microsoft/azure-percept-advanced-development) :

* Intégration d’un modèle d’intelligence artificielle personnalisé à l’appareil
* Mise à jour d’un modèle déjà pris en charge avec l’apprentissage de transfert
* Et plus encore
