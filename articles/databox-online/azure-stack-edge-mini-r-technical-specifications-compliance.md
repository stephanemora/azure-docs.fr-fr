---
title: Spécifications techniques et conformité de l’appareil Microsoft Azure Stack Edge Mini R | Microsoft Docs
description: Découvrez les spécifications techniques et la conformité de votre appareil Azure Stack Edge Mini R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: b0f3b81ac12d0ae96dde0397bc01a4570d992ea9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310170"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Spécifications techniques de l’appareil Azure Stack Edge Mini R

Les composants matériels de l’appareil Microsoft Azure Stack Edge Mini R sont conformes aux caractéristiques techniques et normes réglementaires présentées dans cet article. Les spécifications techniques décrivent le processeur, la mémoire, les blocs d’alimentation (PSU), la capacité de stockage, les dimensions du boîtier et le poids.


## <a name="compute-memory-specifications"></a>Spécifications de calcul et de mémoire

L’appareil Azure Stack Edge Mini R présente les spécifications suivantes en matière de calcul et de mémoire :

| Caractéristique           | Valeur                           |
|-------------------------|---------------------------------|
| Type de processeur                | Intel Xeon D-1577               |
| Processeur (brut)                | 16 cœurs au total, 32 processeurs virtuels au total  |
| Processeur (utilisable)             | 24 processeurs virtuels                        |
| Type de mémoire             | 16 Go 2400 MT/s SODIMM          |
| Mémoire (brute)             | 48 Go de RAM (3 x 16 Go)           |
| Mémoire (utilisable)          | 32 Go de RAM                       |


## <a name="compute-acceleration-specifications"></a>Spécifications de l’accélération de calcul

Une unité VPU (Vision Processing Unit) est incluse sur chaque appareil Azure Stack Edge Mini R qui active les applications Kubernetes, de réseau neuronal profond et de vision par ordinateur.

| Caractéristique             | Valeur                  |
|---------------------------|------------------------|
| Carte d’accélération du calcul | Intel Movidius Myriad X VPU <br> Pour plus d’informations, consultez [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX). |


## <a name="storage-specifications"></a>Spécifications de stockage

L’appareil Azure Stack Edge Mini R a 1 disque de données et 1 disque de démarrage (qui sert de stockage du système d’exploitation). Le tableau suivant présente les détails de la capacité de stockage de l’appareil.

|     Caractéristique                          |     Valeur                                              |
|--------------------------------------------|--------------------------------------------------------|
|    Nombre de disques SSD     |    2 disques de 1 To <br> 1 disque de données et 1 disque de démarrage |
|    Capacité de disque SSD unique                     |    1 To                                                |
|    Capacité totale (données uniquement)              |    1 To                                                |
|    Capacité utilisable totale*                  |    ~ 750 Go                                            |

**Une partie de l’espace est réservé à un usage interne.*

## <a name="network-specifications"></a>Spécifications réseau

L’appareil Azure Stack Edge Mini R présente les spécifications suivantes en matière de réseau :


|Caractéristique  |Valeur  |
|---------|---------|
|Interfaces réseau    |2 x 10 GbE SFP+ <br> Affichés sous le nom PORT 3 et PORT 4 dans l’interface utilisateur locale           |
|Interfaces réseau    |2 x 1 GbE RJ45 <br> Affichés sous le nom PORT 1 et PORT 2 dans l’interface utilisateur locale          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Spécifications des blocs d’alimentation

L’appareil Azure Stack Edge Mini R inclut un adaptateur secteur de 85 W externe pour alimenter et charger la batterie intégrée.

Le tableau suivant présente les spécifications du bloc d’alimentation :

| Caractéristique           | Valeur                      |
|-------------------------|----------------------------|
| Puissance de sortie maximale    | 85 W                       |
| Fréquence               | 50/60 Hz                   |
| Sélection de la plage de tension | Détermination automatique : 100-240 V AC |



## <a name="included-battery"></a>Batterie incluse

L’appareil Azure Stack Edge Mini R inclut également une batterie intégrée qui est chargée par le bloc d’alimentation.

Une [batterie de type 2590](https://www.bren-tronics.com/bt-70791ck.html) supplémentaire peut être utilisée conjointement avec la batterie intégrée pour étendre l’utilisation de l’appareil entre les charges. Cette batterie doit être conforme à toutes les réglementations relatives à la sécurité, au transport et à l’environnement applicables dans le pays d’utilisation.


| Caractéristique            | Valeur                      |
|--------------------------|----------------------------|
| Capacité de batterie intégrée | 73 Wh                     |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensions et poids des boîtiers

Les tableaux suivants répertorient les dimensions et les caractéristiques de poids des différents boîtiers.

### <a name="enclosure-dimensions"></a>Dimensions de boîtier

Le tableau suivant indique les dimensions de l’appareil et de l’onduleur dans le boîtier renforcé, en millimètres et en pouces.

|     Boîtier     |     Millimètres     |     Pouces     |
|-------------------|---------------------|----------------|
|    Hauteur         |    68               |    2.68        |
|    Largeur          |    208              |      8.19      |
|    Longueur          |   259              |    10.20       |


### <a name="enclosure-weight"></a>Poids du boîtier

Le tableau suivant répertorie le poids de l’appareil, batterie incluse.

|     Boîtier                     |     Poids          |
|-----------------------------------|---------------------|
|    Poids total de l’appareil     |     3,18 kg (7 lb)           |

## <a name="enclosure-environment-specifications"></a>Caractéristiques ambiantes pour le boîtier


Cette section présente les caractéristiques liées à l’environnement du boîtier, telles que la température, l’humidité et l’altitude.


|     Spécifications             |     Description                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Plage de températures          |     De 0 à 43 °C (en fonctionnement)                                              |
|     Vibration                  |     Méthode MIL-STD-810 514.7*<br> Procédure I CAT 4, 20                  |
|     Choc                      |     Méthode MIL-STD-810 516.7*<br> Procédure IV, logistique                 |
|     Altitude                   |     En fonctionnement :   3 000 mètres<br> Hors fonctionnement : 12 000 mètres          |

**Toutes les références ont trait à la norme MIL-STD-810G Change 1 (2014)*


## <a name="next-steps"></a>Étapes suivantes

- [Déployer votre Azure Stack Edge Mini R](azure-stack-edge-placeholder.md)
