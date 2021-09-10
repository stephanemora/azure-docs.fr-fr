---
title: Module d’IA Azure Percept Vision
description: Une présentation d’azureeyemodule, qui est le module responsable de l’exécution de la charge de travail de la vision IA sur Azure Percept DK.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: overview
ms.date: 08/09/2021
ms.custom: template-overview
ms.openlocfilehash: b09c5eb0dc2fa977c544d7da8178408dadcd08a6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222369"
---
# <a name="azure-percept-vision-ai-module"></a>Module d’IA Azure Percept Vision

Azureeyemodule est le nom du module de périphérie responsable de l’exécution de la charge de travail de la vision IA sur Azure Percept DK. Il fait partie de la suite Azure IoT de modules de périphérie et est déployé sur Azure Percept DK pendant [l’expérience de configuration](./quickstart-percept-dk-set-up.md). Cet article propose une vue d’ensemble du module et de son architecture.

## <a name="architecture"></a>Architecture

:::image type="content" source="media/azureeyemodule-overview/azureeyemodule-architecture.png" alt-text="Diagramme montrant l’architecture d’azureeyemodule.":::

La charge de travail Azure Percept sur Azure Percept DK est une application C++ qui s’exécute dans le conteneur Docker azureeyemodule. Elle utilise OpenCV GAPI pour le traitement des images et l’exécution du modèle. Azureeyemodule s’exécute sur le système d’exploitation Mariner dans le cadre de la suite Azure IoT de modules qui s’exécutent sur Azure Percept DK.

La charge de travail Azure Percept est destinée à prendre des images et d’émettre des images et des messages en sortie. Les images de sortie peuvent être marquées avec des dessins tels que des zones englobantes, des masques de segmentation, des jointures, des étiquettes, etc. Les messages de sortie sont un flux JSON de résultats d’inférence qui peuvent être ingérés et utilisés par des tâches en aval.
Les résultats sont fournis en tant que flux RTSP disponible sur le port 8554 de l’appareil. Les résultats sont également envoyés à un autre module en cours d’exécution sur l’appareil, qui sert le flux RTSP encapsulé dans un serveur HTTP, s’exécutant sur le port 3000. Dans les deux cas, ils ne sont visibles que sur le réseau local.

> [!CAUTION]
> Il n’y a *pas* de chiffrement ou d’authentification pour les flux RTSP. Toute personne sur le réseau local peut voir exactement ce qu’Azure Percept Vision voit en tapant l’adresse correcte dans un navigateur web ou un lecteur multimédia RTSP.

La charge de travail Azure Percept offre plusieurs fonctionnalités dont les utilisateurs finaux peuvent tirer avantage :
- Une solution sans code pour les cas d’utilisation courants de vision par ordinateur, comme la classification d’objets et la détection d’objets communs.
- Une solution avancée, dans laquelle un développeur peut apporter son propre modèle (potentiellement en cascade) à l’appareil et l’exécuter, en passant éventuellement les résultats à un autre module IoT de sa propre création exécuté sur l’appareil.
- Une boucle de reformation pour récupérer régulièrement des images de l’appareil, reformer le modèle dans le cloud, puis renvoyer le modèle nouvellement formé vers l’appareil. Utilisation de la capacité de l’appareil à mettre à jour et échanger des modèles à la volée.

## <a name="ai-workload-details"></a>Détails de la charge de travail IA
L’application de charge de travail est disponible en libre accès dans le [référentiel github](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app) Azure Percept Advanced Development et se compose de nombreux petits modules C++, dont voici certains des plus importants :
- [main.cpp](https://github.com/microsoft/azure-percept-advanced-development/blob/main/azureeyemodule/app/main.cpp) : Configure tout et exécute la boucle principale.
- [iot](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/iot) : Ce dossier contient les modules qui gèrent les messages entrants et sortants du hub Azure IoT Edge, ainsi que la méthode de mise à jour des jumeaux.
- [model](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/model) : Ce dossier contient des modules pour une hiérarchie de classes de modèles de vision par ordinateur.
- [kernels](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/kernels) : Ce dossier contient des modules pour les noyaux G-API, les ops et les fonctions wrapper C++.

Les développeurs peuvent créer des modules personnalisés ou personnaliser l’azureeyemodule actuel à l’aide de cette application de charge de travail. 

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous en savez plus sur l’azureeyemodule et la charge de travail Azure Percept, essayez d’utiliser votre propre modèle ou pipeline en suivant l’un de [ces tutoriels](https://github.com/microsoft/azure-percept-advanced-development/blob/main/tutorials/README.md)
- Ou essayez l’**apprentissage de transfert** en utilisant l’un de nos [notebooks de Machine Learning](https://github.com/microsoft/azure-percept-advanced-development/tree/main/machine-learning-notebooks) prêts à l’emploi

