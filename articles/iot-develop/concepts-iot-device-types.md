---
title: Vue d’ensemble des types d’appareil Azure IoT
description: Découvrez les différents types d’appareil pris en charge par Azure IoT et les outils disponibles.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100656317"
---
# <a name="overview-of-azure-iot-device-types"></a>Vue d’ensemble des types d’appareil Azure IoT
Les appareils IoT existent sur une large sélection de plateformes matérielles. Cela va des petits MCU 8 bits aux processeurs x86 les plus récents, comme il est possible d’en trouver sur un ordinateur de bureau. Comme de nombreuses variables dictent le choix du matériel pour un appareil IoT, cet article décrit certaines des principales différences.

## <a name="key-hardware-differentiators"></a>Principaux facteurs de différence pour le matériel
Certains facteurs importants lors du choix de votre matériel sont le coût, la consommation d’énergie, le réseau et les entrées et sorties disponibles.

* **Coût :** Les appareils plus petits et moins onéreux sont généralement utilisés lors de la production en masse du produit final. Toutefois, le compromis est que le développement de l’appareil peut être plus cher en raison des contraintes auxquelles il est soumis. Le coût de développement pouvant être réparti sur tous les appareils produits, le coût de développement par unité est faible.

* **Énergie :** La quantité d’énergie consommée par un appareil est importante si celui-ci utilise des batteries et qu’il n’est pas connecté à la grille d’alimentation. Les MCU, qui sont souvent conçus pour des scénarios à faible consommation énergétique, peuvent être un meilleur choix pour prolonger la durée de vie de la batterie.

* **Accès réseau :** Il existe de nombreuses façons de connecter un appareil à un service cloud. Citons les connexions Ethernet, Wi-Fi et cellulaire parmi les options disponibles. Le type de connexion que vous choisissez dépend de l’endroit où l’appareil est déployé et de la façon dont il est utilisé. Par exemple, une connexion cellulaire peut être une option intéressante en raison de la couverture élevée qu’elle offre, mais elle peut s’avérer onéreuse pour les appareils à trafic élevé. Une connexion Ethernet câblée offre des coûts de données moins élevés, mais avec l’inconvénient d’être moins portable.

* **Entrée et sorties :** Les entrées et les sorties disponibles sur l’appareil affectent directement ses capacités d’exploitation. Un microcontrôleur dispose généralement de nombreuses fonctions d’E/S intégrées directement à la puce et fournit un large choix de capteurs pour effectuer une connexion directe.

## <a name="microcontrollers-vs-microprocessors"></a>Microcontrôleurs ou microprocesseurs
Les appareils IoT peuvent être répartis en deux catégories principales : microcontrôleurs (MCU) et microprocesseurs (MPU).

Les **MCU** sont moins chers et plus simples à utiliser que les MPU. Un MCU héberge un grand nombre des fonctions, telles que la mémoire, les interfaces et les E/S, au sein de la puce elle-même. Un MPU tire ces fonctionnalités de composants dans des puces de prise en charge. Un MCU utilise souvent un système d’exploitation en temps réel (RTOS) ou s’exécute nu (sans système d’exploitation) et fournit une réponse en temps réel et des réactions très déterministes aux événements externes.

Les **MPU** exécutent généralement un système d’exploitation à usage général, tel que Windows, Linux ou MacOSX, qui fournit une réponse en temps réel non déterministe. Il n’y a généralement aucune garantie quant au moment auquel une tâche est effectuée. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU ou MPU":::

Le tableau ci-dessous présente certaines des différences qui existent entre un système basé sur un MCU et un système basé sur un MPU :

||Microcontrôleur (MCU)|Microprocesseur (MPU)|
|-|-|-|
|**UC**| Less | Plus |
|**RAM**| Less | Plus |
|**Clignote**| Less | Plus |
|**SE**| Non ou RTOS | Usage général |
|**Difficulté de développement**| Plus difficile |  Plus facile |
|**Consommation énergétique**| Moins grand | Plus grand |
|**Coût**| Moins grand | Plus grand |
|**Déterministe**| Oui | Non, avec des exceptions|
|**Taille de l’appareil**| Plus petite | Plus grand |
