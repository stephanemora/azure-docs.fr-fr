---
title: Spécifications techniques et conformité de l’appareil Microsoft Azure Stack Edge Mini R | Microsoft Docs
description: Découvrez les spécifications techniques et la conformité de votre appareil Azure Stack Edge Mini R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 05/04/2021
ms.author: alkohli
ms.openlocfilehash: 8f48a007069c243442888f47ed75deb75b271df6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759830"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Spécifications techniques de l’appareil Azure Stack Edge Mini R

Les composants matériels de l’appareil Microsoft Azure Stack Edge Mini R sont conformes aux caractéristiques techniques et normes réglementaires présentées dans cet article. Les spécifications techniques décrivent le processeur, la mémoire, les blocs d’alimentation (PSU), la capacité de stockage, les dimensions du boîtier et le poids.


## <a name="compute-memory"></a>Calcul, mémoire

L’appareil Azure Stack Edge Mini R présente les spécifications suivantes en matière de calcul et de mémoire :

| Caractéristique           | Valeur                           |
|-------------------------|---------------------------------|
| Type de processeur                | Intel Xeon D-1577               |
| Processeur (brut)                | 16 cœurs au total, 32 processeurs virtuels au total  |
| Processeur (utilisable)             | 24 processeurs virtuels                        |
| Type de mémoire             | 16 Go 2400 MT/s SODIMM          |
| Mémoire (brute)             | 48 Go de RAM (3 x 16 Go)           |
| Mémoire (utilisable)          | 32 Go de RAM                       |


## <a name="compute-acceleration"></a>Accélération du calcul

Une unité VPU (Vision Processing Unit) est incluse sur chaque appareil Azure Stack Edge Mini R qui active les applications Kubernetes, de réseau neuronal profond et de vision par ordinateur.

| Caractéristique             | Valeur                  |
|---------------------------|------------------------|
| Carte d’accélération du calcul | Intel Movidius Myriad X VPU <br> Pour plus d’informations, consultez [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX). |


## <a name="storage"></a>Stockage

L’appareil Azure Stack Edge Mini R a 1 disque de données et 1 disque de démarrage (qui sert de stockage du système d’exploitation). Le tableau suivant présente les détails de la capacité de stockage de l’appareil.

|     Caractéristique                          |     Valeur                                              |
|--------------------------------------------|--------------------------------------------------------|
|    Nombre de disques SSD     |    2 disques de 1 To <br> 1 disque de données et 1 disque de démarrage |
|    Capacité de disque SSD unique                     |    1 To                                                |
|    Capacité totale (données uniquement)              |    1 To                                                |
|    Capacité utilisable totale*                  |    ~ 750 Go                                            |

**Une partie de l’espace est réservé à un usage interne.*

## <a name="network"></a>Réseau

L’appareil Azure Stack Edge Mini R présente les caractéristiques suivantes pour le réseau :

|Caractéristique         |Valeur                                                               |
|----------------------|--------------------------------------------------------------------|
|Interfaces réseau    |2 SFP+ de 10 Gbits/s <br> Affichés sous le nom PORT 3 et PORT 4 dans l’interface utilisateur locale     |
|Interfaces réseau    |2 RJ45 de 1 Gbits/s <br> Affichés sous le nom PORT 1 et PORT 2 dans l’interface utilisateur locale      |
|Wi-Fi                 |802.11ac                                                            |

|Caractéristique  |Valeur  |
|---------|---------|
|Interfaces réseau    |2 x 10 GbE SFP+ <br> Affichés sous le nom PORT 3 et PORT 4 dans l’interface utilisateur locale           |
|Interfaces réseau    |2 x 1 GbE RJ45 <br> Affichés sous le nom PORT 1 et PORT 2 dans l’interface utilisateur locale          |
|Wi-Fi   |802.11ac         |

Les routeurs et commutateurs suivants sont compatibles avec les interfaces réseau SPF+ de 10 Gbits/s (ports 3 et 4) sur vos appareils Azure Stack Edge Mini R :

|Routeur/commutateur     |Notes                         |
|------------------|------------------------------|
|[VoyagerESR 2.0](https://klastelecom.com/products/voyageresr2-0/)    |Composant du commutateur Cisco ESS3300   |
|[VoyagerSW26G](https://klastelecom.com/products/voyagersw26g/)       |                                 |
|[VoyagerVM 3.0](https://klastelecom.com/products/voyager-vm-3-0/)    |                                 |
|[Commutateur TDC](https://klastelecom.com/voyager-tdc/)                   |                                 |
|[TRX R2](https://klastelecom.com/products/trx-r2/) (8 cœurs)  <!--Better link: https://www.klasgroup.com/products/voyagersw12gg/? On current link target, an "R6" link opens this page.-->        |                              |
|[SW12GG](https://www.klasgroup.com/products/voyagersw12gg/)          |                                 |

## <a name="transceivers-cables"></a>Transmetteurs, câbles

Les transmetteurs et câbles SFP+ (10 Gbits/s) cuivre suivants sont vivement recommandés pour les utiliser avec des appareils Azure Stack Edge Mini R. Les câbles à fibre optique compatibles peuvent être utilisés avec les interfaces réseau SFP+ (ports 3 et 4), mais ils n’ont pas été testés. 

|Type de transmetteur SFP+ |Câbles pris en charge    | Notes |
|----------------------|--------------------|-------|
|Cuivre à connexion directe SFP+ (10GSFP+Cu)| <ul><li>[FS SFP-10g-DAC](https://www.fs.com/c/fs-10g-sfp-dac-1115) (disponible en température industrielle, de -40 ºC à +85 ºC à la demande)</li><br><li>[10Gtek CAB-10GSFP-P0.5M](http://www.10gtek.com/10G-SFP+-182)</li><br><li>[Cisco SFP-H10GB-CU1M](https://www.cisco.com/c/en/us/products/collateral/interfaces-modules/transceiver-modules/data_sheet_c78-455693.html)</li></ul> |<ul><li>Également connus sous le nom de câbles SFP+ Twinax.</li><br><li>Option recommandée parce que sa consommation électrique est faible et qu’elle est la plus simple.</li><br><li>L’autonégociation n’est pas prise en charge.</li><br><li>La connexion d’un appareil SFP à un appareil SFP+ n’est pas prise en charge.</li></ul>|

## <a name="power-supply-unit"></a>Bloc d’alimentation

L’appareil Azure Stack Edge Mini R inclut un adaptateur secteur de 85 W externe pour alimenter et charger la batterie intégrée.

Le tableau suivant présente les spécifications du bloc d’alimentation :

| Caractéristique           | Valeur                      |
|-------------------------|----------------------------|
| Puissance de sortie maximale    | 85 W                       |
| Fréquence               | 50/60 Hz                   |
| Sélection de la plage de tension | Détermination automatique : 100-240 V AC |

## <a name="included-battery"></a>Batterie incluse

L’appareil Azure Stack Edge Mini R inclut également une batterie intégrée qui est chargée par le bloc d’alimentation.

Une [batterie de type 2590](https://www.bren-tronics.com/bt-70791ck.html) supplémentaire peut être utilisée avec la batterie intégrée pour prolonger le temps d’utilisation de l’appareil entre les charges. Cette batterie doit être conforme à toutes les réglementations relatives à la sécurité, au transport et à l’environnement applicables dans le pays d’utilisation.


| Caractéristique            | Valeur      |
|--------------------------|------------|
| Capacité de batterie intégrée | 73 Wh      |

## <a name="enclosure-dimensions-and-weight"></a>Dimensions et poids du boîtier

Les tableaux suivants répertorient les dimensions et les caractéristiques de poids des différents boîtiers.

### <a name="enclosure-dimensions"></a>Dimensions de boîtier

Le tableau suivant indique les dimensions de l’appareil et de l’onduleur dans le boîtier renforcé, en millimètres et en pouces.

|     Boîtier     |     Millimètres     |     Pouces     |
|-------------------|---------------------|----------------|
|    Hauteur         |    68               |    2.68        |
|    Largeur          |    208              |    8.19        |
|    Longueur         |    259              |    10.20       |


### <a name="enclosure-weight"></a>Poids du boîtier

Le tableau suivant répertorie le poids de l’appareil, batterie incluse.

|     Boîtier                     |     Poids          |
|-----------------------------------|---------------------|
|    Poids total de l’appareil     |     3,18 kg (7 lb)           |

## <a name="enclosure-environment"></a>Environnement du boîtier

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
