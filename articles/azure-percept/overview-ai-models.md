---
title: Modèles IA Azure Percept
description: Apprenez-en davantage sur les modèles d’intelligence artificielle disponibles pour le prototypage et le déploiement.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041584"
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

## <a name="pre-built-solutions"></a>Solutions prédéfinies

Une [solution d’analytique spatiale prédéfinie pour la détection de personnes](https://github.com/george-moore/Santa-Cruz-AI-App) est également disponible. La solution prédéfinie est une application IA open source qui fournit le comptage des personnes en fonction de la périphérie, avec des événements d’entrée/sortie de zone définis par l’utilisateur. La sortie vidéo et IA de l’appareil Edge local est envoyée à [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/), tandis que l’interface utilisateur s’exécute en tant que site web Azure. L’inférence IA est fournie par un modèle d’intelligence artificielle open source pour la détection de personnes.

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

Pour les développeurs avancés, le [notebook Jupyter](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) disponible effectue l’apprentissage de transfert à l’aide d’un modèle TensorFlow (MobileNetSSDV2Lite) préentraîné en Python avec un jeu de données personnalisé pour la détection d’objets. Le notebook utilise des instances de calcul distantes par le biais d’[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview), et peut être exécuté dans le cloud à l’aide du portail AzureML ou localement dans [Visual Studio Code](https://code.visualstudio.com/).

Il contient également des [scripts](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts) Python utiles pour la gestion des jeux de données et le [programme d’installation du pack d’outils de développement](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md), qui installe et configure tous les outils nécessaires au développement d’une solution IA avancée.
