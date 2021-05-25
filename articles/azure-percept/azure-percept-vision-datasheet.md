---
title: Fiche technique d’Azure Percept Vision
description: Consulter la fiche technique d’Azure Percept Vision pour le détail des spécifications
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: d60c7f72c6b32f01fcf93b45ed6507e57ad97af3
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108742388"
---
# <a name="azure-percept-vision-datasheet"></a>Fiche technique d’Azure Percept Vision

Les spécifications listées ci-dessous s’appliquent à Azure Percept Vision, inclus dans le [DK Azure Percept](./azure-percept-dk-datasheet.md).

|Spécification du produit           |Valeur     |
|--------------------------------|---------------------|
|Secteurs d’activité cibles               |Industrie <br> Bâtiments intelligents <br> Automobile <br> Commerce |
|Scénarios de bannière                  |Analytique des acheteurs <br> Disponibilité en rayon <br> Réduction des pertes <br> Surveillance de l’espace de travail|
|Dimensions                      |42 mm x 42 mm x 40 mm (assemblage SoM Azure Percept Vision avec boîtier) <br> 42 mm x 42 mm x 6 mm (puce SoM Vision)|
|Plan de contrôle de gestion        |Azure Device Update (ADU)          |
|Logiciels et services pris en charge |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure Device Update |
|Accélération IA                 |Unité de traitement de la vision Movidius Myriad X (MA2085) avec Intel Camera ISP intégré, 0,7 TOPS |
|Capteurs et indicateurs visuels   |Capteur d’appareil photo Sony IMX219 avec objectif 6P<br>Résolution : 8 MP à 30 FPS, distance : 50 cm à l’infini<br>Champ de vue : 120 degrés en diagonale, Couleur : Plage dynamique large, Rolling shutter à foyer fixe|
|Prise en charge de la caméra                  |RGB <br> 2 caméras pouvant fonctionner simultanément |
|Contrôleur de chiffrement de sécurité      |ST-Micro STM32L462CE      |
|Composant de versioning/ID       |EEPROM 64 ko |
|Mémoire                          |LPDDR4 2 Go     |
|Power                           |3,5 W     |
|Ports                           |1 port USB 3.0 type C <br> 2 ports MIPI 4 voies (jusqu’à 1,5 Gbits/s par voie)     |
|Interfaces de contrôle              |2 I2C <br> 2 SPI <br> 6 PWM (GPIO : horloge [2], synchronisation d’images [2], non utilisé [2]) <br> 2 GPIO de rechange |
|Certification                   |FCC <br> IC <br> RoHS <br> REACH <br> UL   |
|Température de fonctionnement           |0 à 27 degrés C (assemblage SoM Azure Percept Vision avec boîtier) <br> -10 à 70 degrés C (puce SoM Vision) |
|Température au toucher               |<= 48 degrés C |
|Humidité relative               |8 % à 90 %    |
