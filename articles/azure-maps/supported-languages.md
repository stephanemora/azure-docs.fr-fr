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
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686784"
---
# <a name="localization-support-in-azure-maps"></a>Prise en charge de la localisation dans Azure Maps

Azure Maps prend en charge divers langages et les vues basées sur le pays/région. Cet article fournit les langues prises en charge et les vues de guider votre implémentation d’Azure Maps.


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


## <a name="azure-maps-supported-views"></a>Azure Maps pris en charge les vues

Azure paramètre mappe une vue (également appelée « paramètre de région utilisateur ») est un Code de pays ISO 3166 2 lettres qui affichera les cartes correctes pour ce pays/région en spécifiant le jeu de géopolitique litige bordures et les étiquettes sont affichées sur la carte.  Par défaut le paramètre View est définie **« Unified »**.  Pays/régions qui ne sont pas dans la liste d’affichage par défaut à la vue « Unified ». Il vous incombe de déterminer l’emplacement de vos utilisateurs et définissez le paramètre de mode correctement pour cet emplacement. Le paramètre View dans Azure mappe doit être utilisé conformément à la législation applicable, y compris celles relative au mappage du pays où les cartes, des images et autre contenu tiers et les données que vous êtes autorisé à accéder via Azure Maps est mis à disposition.

Le tableau suivant fournit des vues prises en charge.

| Affichage         | Description                            |  Cartes | Recherche | Contrôle de carte JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Émirats arabes unis (vue arabe)    |   ✓   |        |     ✓          |
| AR           | Argentine (Argentine vue)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahreïn (vue arabe)                 |   ✓   |        |     ✓          |
| IN           | Inde (vue indien)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (vue arabe)                    |   ✓   |        |     ✓          |
| JO           | Jordanie (vue arabe)                  |   ✓   |        |     ✓          |
| KW           | Koweït (vue arabe)                  |   ✓   |        |     ✓          |
| LB           | Liban (vue arabe)                 |   ✓   |        |     ✓          |
| MA           | Maroc (vue marocain)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (vue arabe)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (vue pakistanais)              |   ✓   |    ✓    |     ✓          |
| PS           | Autorité palestinienne (vue arabe)    |   ✓   |        |     ✓          |
| QA           | Qatar (vue arabe)                   |   ✓   |        |     ✓          |
| SA           | Arabie Saoudite (vue arabe)            |   ✓   |        |     ✓          |
| SY           | Syrie (vue arabe)                   |   ✓   |        |     ✓          |
| YE           | Yémen (vue arabe)                   |   ✓   |        |     ✓          |
| Unifiée      | Vue unifiée (autres)                  |   ✓   |   ✓     |     ✓          |
