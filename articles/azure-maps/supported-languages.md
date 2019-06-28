---
title: Prise en charge de la localisation dans Azure Maps | Microsoft Docs
description: En savoir plus sur les langues prises en charge pour les services dans Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1928185521419006a487a933e2ecba79894a09d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686784"
---
# <a name="localization-support-in-azure-maps"></a>Prise en charge de la localisation dans Azure Maps

Azure Maps prend en charge différentes langues et différents affichages basés sur le pays ou la région. Cet article présente les langues et affichages pris en charge pour vous aider à implémenter Azure Maps.


## <a name="azure-maps-supported-languages"></a>Langues prises en charge dans Azure Maps

Les services Azure Maps ont été localisés en différentes langues. Le tableau suivant indique les codes des langues prises en charge pour chaque service.  
  

| ID         | Nom                   |  Cartes | Recherche | Routage | Incidents de trafic | Commandes de la carte JS | Fuseau horaire |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Afrikaans              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Arabe                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Basque                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Bulgare              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | Catalan                |       |    ✓   |         |                   |                |     ✓     |
| zh-hans    | Chinois (simplifié)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-hant    | Chinois (traditionnel)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | Croate               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Tchèque                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Danois                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | Néerlandais                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Néerlandais (Belgique)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | Anglais (Australie)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | Anglais (Nouvelle-Zélande)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Anglais (Grande-Bretagne) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Anglais (États-Unis)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Et-EE      | Estonien               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finnois                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Anglais                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Français (Canada)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galicien               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Allemand                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | Grec                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | Hébreu                 |       |    ✓   |         |         ✓         |                |     ✓     |
| hi-IN      | Hindi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Hongrois              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonésien             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | Italien                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonais               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Kazakh                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Coréen                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Espagnol (Amérique latine) |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Letton                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Lituanien             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | Malais (latin)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norwegian Bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutral Ground Truth : langues officielles pour toutes les régions dans les scripts locaux s’ils sont disponibles |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutral Ground Truth : exonymes du Latin. Le script Latin sera utilisé s’il est disponible |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polonais                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-br      | Portugais (Brésil)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Portugais (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ro-RO      | Roumain               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Russe                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Serbe (cyrillique)     |       |    Serbe (cyrillique) (sr-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | Serbe (latin)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Slovaque              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Slovène              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Espagnol                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Espagnol (Mexique)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Suédois                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Thaï                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turc                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ukrainien               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnamien             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Affichages pris en charge dans Azure Maps

Le paramètre Affichage d'Azure Maps (ou « paramètre régional de l'utilisateur ») est un code de pays ISO-3166 à deux lettres qui affiche les cartes correspondant à ce pays ou à cette région, en spécifiant l'ensemble de frontières géopolitiquement contestées et d'étiquettes affichées sur la carte.  Par défaut le paramètre Affichage est défini sur **« Unifié »** .  Les pays/régions qui ne figurent pas dans la liste des affichages sont placés par défaut dans l'affichage « Unifié ». Il vous incombe de déterminer l'emplacement de vos utilisateurs, puis de définir correctement le paramètre Affichage correspondant à cet emplacement. Le paramètre Affichage d'Azure Maps doit être utilisé dans le respect des lois en vigueur, y compris celles relatives à la cartographie du pays où les cartes, images et autres données et contenus tiers auxquels vous êtes autorisé à accéder via Azure Maps sont mis à disposition.

Le tableau suivant présente les affichages pris en charge.

| Affichage         | Description                            |  Cartes | Recherche | Contrôle de carte JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Émirats Arabes Unis (affichage arabe)    |   ✓   |        |     ✓          |
| AR           | Argentine (affichage argentin)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahreïn (affichage arabe)                 |   ✓   |        |     ✓          |
| IN           | Inde (affichage indien)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (affichage arabe)                    |   ✓   |        |     ✓          |
| JO           | Jordanie (affichage arabe)                  |   ✓   |        |     ✓          |
| KW           | Koweït (affichage arabe)                  |   ✓   |        |     ✓          |
| LB           | Liban (affichage arabe)                 |   ✓   |        |     ✓          |
| MA           | Maroc (affichage marocain)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (affichage arabe)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (affichage pakistanais)              |   ✓   |    ✓    |     ✓          |
| PS           | Autorité palestinienne (affichage arabe)    |   ✓   |        |     ✓          |
| QA           | Qatar (affichage arabe)                   |   ✓   |        |     ✓          |
| SA           | Arabie Saoudite (affichage arabe)            |   ✓   |        |     ✓          |
| SY           | Syrie (affichage arabe)                   |   ✓   |        |     ✓          |
| YE           | Yémen (affichage arabe)                   |   ✓   |        |     ✓          |
| Unifié      | Affichage unifié (Autres)                  |   ✓   |   ✓     |     ✓          |
