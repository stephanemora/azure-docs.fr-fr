---
title: Couverture de trafic dans Azure Maps | Microsoft Docs
description: En savoir plus sur la couverture de trafic dans Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 49e91287d23c35d8cfea858d41c47ba15efd820c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-maps-traffic-coverage"></a>Couverture du trafic Azure Maps

Azure Maps fournit de riches données sur le trafic, sous la forme de **flux** de trafic et d’**incidents**. Ces données peuvent être visualisées sur les cartes ou utilisées pour générer des itinéraires plus pertinents qui prennent en compte les réelles conditions de conduite. 

Toutefois, Maps n’a pas le même niveau d’informations et de précision pour toutes les régions. Le tableau suivant fournit des informations sur les types de données de trafic que vous pouvez solliciter pour chaque région : 

|Région  |Incidents  |Flux  |
|---------|:---------:|:---------:|
|Argentine      |         |✓         |
|Australie     |✓         |✓        |
|Autriche     |✓         |✓         |
|Bahreïn     |         |✓         |
|Belgique     |✓         |✓         |
|Brésil     |✓         |✓         |
|Bulgarie     |✓         |✓         |
|Canada     |✓         |✓         |
|Chili     |✓         |✓         |
|Colombie      |         |✓         |
|Croatie     |         |✓         |
|République tchèque     |✓         |✓         |
|Danemark     |✓         |✓         |
|Égypte     |         |✓         |
|Estonie     |         | ✓        |
|Finlande     |✓         |✓         |
|+Îles Åland      |✓         |✓         |
|France     |✓         |✓         |
|+Monaco     |✓         |✓         |
|Allemagne     |✓         |✓         |
|Grèce     |✓         |✓         |
|Hong Kong (R.A.S.)     |✓         |✓         |
|Hongrie     |✓         |✓         |
|Islande     |         |✓         |
|Indonésie     |✓         |✓         |
|Irlande     |✓         |✓         |
|Israël     |         |✓         |
|Italie     |✓         |✓        |
|+Saint-Marin     |✓         |✓         |
|Koweït     |✓         |✓         |
|Lettonie     |         |✓         |
|Lesotho     |✓         |✓         |
|Lituanie     |         |✓         |
|Luxembourg     |✓         |✓         |
|Macao R.A.S.     |         |✓         |
|Malaisie     |✓         |✓         |
|Malte     |✓         |✓         |
|Mexique     |✓         |✓         |
|Maroc     |         |✓         |
|Pays-bas     |✓         |✓         |
|Nouvelle-Zélande     |✓         |✓         |
|Norvège     |✓         |✓         |
|Oman     |         |✓         |
|Pologne     |✓         |✓         |
|Portugal     |✓         |✓         |
|+Açores et Madère     |         |✓         |
|Qatar     |         |✓         |
|Roumanie     |         |✓         |
|Fédération de Russie     |✓         |✓         |
|Arabie Saoudite     |✓         |✓         |
|Singapour     |✓         |✓         |
|Slovaquie     |✓         |✓         |
|Slovénie     |✓         |✓         |
|Afrique du Sud     |✓         |✓         |
|Espagne     |✓         |✓         |
|+Andorre     |✓         |✓         |
|+Îles Baléares     |✓         |✓         |
|+Îles Canaries     |✓         |✓         |
|+Gibraltar     |✓         |✓         |
|Suède     |✓         |✓         |
|Suisse     |✓         |✓        |
|+Liechtenstein      |✓         |✓         |
|Taïwan     |✓         |✓        |
|Thaïlande     |✓         |✓        |
|Turquie     |✓         |✓         |
|Ukraine     |✓         |✓         |
|Émirats Arabes Unis     |✓         |✓         |
|Royaume-Uni     |✓         |✓         |
|(Guernesey & Jersey)     |✓         |✓         |
|Île de Man     |✓         |✓         |
|États-Unis     |✓         |✓        |
|+Porto Rico     |✓         |✓         |

Pour plus d’informations sur les données de trafic Azure Maps, consultez les pages de référence sur le [trafic](https://docs.microsoft.com/rest/api/maps/traffic).