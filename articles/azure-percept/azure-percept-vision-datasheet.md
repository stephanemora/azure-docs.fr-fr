---
title: Fiche technique d’Azure Percept Vision
description: Consulter la fiche technique d’Azure Percept Vision pour le détail des spécifications
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 7bbb3a88bbc3011ec5dd917cdb0c1e49f7556aab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177162"
---
# <a name="azure-percept-vision-datasheet"></a>Fiche technique d’Azure Percept Vision

Les spécifications listées ci-dessous s’appliquent à Azure Percept Vision, inclus dans le [DK Azure Percept](./azure-percept-dk-datasheet.md).

|Spécification du produit           |Valeur     |
|--------------------------------|---------------------|
|Secteurs d’activité cibles               |Industrie <br> Bâtiments intelligents <br> Automobile <br> Commerce |
|Scénarios de bannière                  |Analytique des acheteurs <br> Disponibilité en rayon <br> Réduction des pertes <br> Surveillance de l’espace de travail|
|Dimensions                      |42 mm x 42 mm x 40 mm (assemblage SoM Azure Percept Vision avec boîtier) <br> 42 mm x 42 mm x 6 mm (puce SoM Vision)|
|Plan de contrôle de gestion        |Azure Device Update (ADU)          |
|Logiciels et services pris en charge |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure Device Update |
|Accélération IA                 |Unité de traitement de la vision Movidius Myriad X (MA2085) avec Intel Camera ISP intégré, 0,7 TOPS |
|Capteurs et indicateurs visuels   |Capteur d’appareil photo Sony IMX219 avec objectif 6P<br>Résolution : 8 MP à 30 FPS, distance : 50 cm à l’infini<br>Champ de vue : 120 degrés en diagonale, Couleur : Plage dynamique large, Rolling shutter à foyer fixe|
|Prise en charge de la caméra                  |RGB <br> 2 caméras pouvant fonctionner simultanément |
|Contrôleur de chiffrement de sécurité      |ST-Micro STM32L462CE      |
|Composant de versioning/ID       |EEPROM 64 Ko |
|Mémoire                          |LPDDR4 2 Go     |
|Power                           |3,5 W     |
|Ports                           |1 port USB 3.0 type C <br> 2 ports MIPI 4 voies (jusqu’à 1,5 Gbits/s par voie)     |
|Interfaces de contrôle              |2 I2C <br> 2 SPI <br> 6 PWM (GPIO : horloge [2], synchronisation d’images [2], non utilisé [2]) <br> 2 GPIO de rechange |
|Certification                   |FCC <br> IC <br> RoHS <br> REACH <br> UL   |
|Température de fonctionnement           |0 à 27 degrés C (assemblage SoM Azure Percept Vision avec boîtier) <br> -10 à 70 degrés C (puce SoM Vision) |
|Température au toucher               |<= 48 degrés C |
|Humidité relative               |8 % à 90 %    |
