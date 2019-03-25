---
title: Microsoft Azure données boîte Edge les caractéristiques techniques et conformité | Microsoft Docs
description: En savoir plus sur les spécifications techniques et de la conformité pour votre zone de données Azure Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/12/2019
ms.author: alkohli
ms.openlocfilehash: ab3d3a13f8bfa69bd9e13dae344cfbcc3731cc77
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402590"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Spécifications techniques Edge de zone de données Azure

Les composants matériels de votre appareil Microsoft Azure données boîte Edge respectent les spécifications techniques et les normes réglementaires présentées dans cet article. Les spécifications techniques décrivent les unités d’alimentation (blocs d’alimentation), la capacité de stockage, boîtiers et des normes environnementales. 

## <a name="power-supply-unit-specifications"></a>Spécifications unité d’alimentation

Le périphérique périmétrique de zone de données a deux 100-240 V unités d’alimentation (blocs d’alimentation) avec les fans de hautes performances. Les deux blocs d’alimentation fournissent une configuration de l’alimentation redondante. Si un bloc d’alimentation échoue, l’appareil continue à fonctionner normalement sur l’autres PSU jusqu'à ce que le module défaillant est remplacé. Le tableau suivant répertorie les spécifications techniques des blocs d’alimentation.

| Caractéristique           | LE BLOC D’ALIMENTATION DE 750 W                  |
|-------------------------|----------------------------|
| Puissance de sortie maximale    | 750 W                     |
| Fréquence               | 50/60 Hz                   |
| Sélection de la plage de tension | Plage automatique : 100-240 V CA |
| Enfichable à chaud           | Oui                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Spécifications de stockage

Les appareils de périphérie de zone de données ont 10 X 2,5" SSD NVMe, chacun avec une capacité de 1,6 To. Ces disques SSD, 2 sont des disques de système d’exploitation, et les 8 sont des disques de données. La capacité totale utilisable pour l’appareil est à peu près 12,5 To. Le tableau suivant présente les détails de la capacité de stockage de l’appareil.

|     Caractéristique                          |     Valeur             |
|--------------------------------------------|-----------------------|
|    Nombre de disques SSD     |    8                  |
|    Capacité de disque SSD unique                     |    1,6 To             |
|    Capacité totale                          |    12,8 TO            |
|    Capacité utilisable totale*                  |    ~ 12,5 TO            |

**Partie de l’espace est réservé à un usage interne.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensions et poids des boîtiers

Les tableaux suivants répertorient les dimensions et les caractéristiques de poids des différents boîtiers.

### <a name="enclosure-dimensions"></a>Dimensions de boîtier

Le tableau suivant répertorie les dimensions du boîtier en millimètres et en pouces.

|     Boîtier     |     Millimètres     |     Pouces     |
|-------------------|---------------------|----------------|
|    Hauteur         |    304.8            |    12          |
|    Largeur          |    660.4            |    26          |
|    profondeur          |    1041.4           |    41          |

### <a name="enclosure-weight"></a>Poids du boîtier

Le package de l’appareil pèse livres 66. et deux personnes afin de le gérer. Le poids de l’appareil dépend de la configuration du boîtier.

|     Boîtier                                 |     Poids          |
|-----------------------------------------------|---------------------|
|    Poids total, notamment l’empaquetage       |    équilibreurs de charge 66.          |
|    Poids de l’appareil                       |    équilibreurs de charge 48,3.        |

## <a name="enclosure-environment-specifications"></a>Caractéristiques ambiantes pour le boîtier

Cette section répertorie les spécifications liées à l’environnement du boîtier telles que la température, humidité et altitude.

### <a name="temperature-and-humidity"></a>Température et humidité

|     Boîtier         |     Plage de températures ambiantes     |     Humidité relative ambiante     |     Point de rosée maximale     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    En fonctionnement        |    10° C À 35° C (50° F - 86° F)         |    10-80 % sans condensation.         |    29 °C (84 °F)            |
|    Hors fonctionnement    |    -40 ° C à 65 ° C (40 ° F - 149 ° F)     |    5 à 95 % sans condensation.          |    33° C (91° F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Ventilation, altitude, chocs, vibrations, orientation, sécurité et CEM

|     Boîtier                           |     Caractéristiques en fonctionnement                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Ventilation                              |    La ventilation du système s’effectue de l’avant vers l’arrière. Le système doit être utilisé avec une installation basse pression à échappement vers l’arrière. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitude maximale, en fonctionnement        |    3048 mètres (10 000 pieds) avec la valeur maximale de la température nominale de fonctionnement est déterminé par [température rating retirer les spécifications de fonctionnement](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude maximale, non opérationnelle    |    12 000 mètres (pieds 39,370)                                                                                                                                                                                         |
|    Chocs, en fonctionnement                   |    6 G pour 11 millisecondes dans les 6 orientations                                                                                                                                                                         |
|    Chocs, hors fonctionnement               |    71 G 2 millisecondes dans les 6 orientations                                                                                                                                                                           |
|    Vibrations, en fonctionnement               |    0,26 G<sub>RMS</sub> 5 Hz à 350 Hz aléatoire                                                                                                                                                                                     |
|    Vibrations, hors fonctionnement           |    1,88 G<sub>RMS</sub> 10 Hz à 500 Hz pendant 15 minutes (tous les six faces testés.)                                                                                                                                                  |
|    Orientation et montage             |    montage en rack de 19"                                                                                                                                                                                        |
|    Sécurité et homologations                 |    EN 60950-1:2006 + A1:2010 + A2:2013 + A11:2009 + A12:2011 / IEC 60950-1:2005 ed2 + A1:2009 + A2:2013 EN 62311:2008                                                                                                                                                                       |
|    CEM                                  |    FCC A, ICES-003 <br>EN 55032:2012 / CISPR 32:2012  <br>EN 55032:2015 / CISPR 32:2015  <br>FR 55024:2010 + A1:2015 / CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014 / CEI 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013 / CEI 61000-3-3:2013                                                                                                                                                                                         |
|    Énergie             |    Règlement (UE) non. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Évaluation des spécifications de température de fonctionnement

|     Température fonctionnement déduplication évaluation     |     Plage de températures ambiantes                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Jusqu'à 35 ° C (95° F)                       |    Température maximale est réduite par 1° C/300 mètres (1° F/547 ft) m 950 (3,117 ft).    |
|    35° C à 40° C (95° F à 104° F)            |    Température maximale est réduite par 1° C/175 m (1° F/319 ft) ci-dessus m 950 (3,117 ft).    |
|    40° C à 45° C (104° F à 113° F)           |    Température maximale est réduite par 1° C/125 m (1° F/228 ft) ci-dessus m 950 (3,117 ft).    |


## <a name="next-steps"></a>Étapes suivantes

- [Déployer votre solution Azure Data Box Edge](data-box-edge-deploy-prep.md)
