---
title: Prise en charge de la localisation dans Azure Maps | Microsoft Docs
description: En savoir plus sur les langues prises en charge pour les services dans Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a9446301cc4bb46c989223ad020c7a8e8b353ad3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446185"
---
# <a name="localization-support-in-azure-maps"></a>Prise en charge de la localisation dans Azure Maps

Azure Maps prend en charge différentes langues et différents affichages basés sur le pays ou la région. Cet article présente les langues et affichages pris en charge pour vous aider à implémenter Azure Maps.


## <a name="azure-maps-supported-languages"></a>Langues prises en charge dans Azure Maps

Les services Azure Maps ont été localisés en différentes langues. Le tableau suivant indique les codes des langues prises en charge pour chaque service.  
  

| id         | Nom                   |  Cartes | Recherche | Routage | Incidents de trafic | Commandes de la carte JS | Fuseau horaire |
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

> [!Note]
> Le 1er août 2019, nous lançons Azure Maps dans les pays/régions suivants :
>  * Argentine
>  * Inde
>  * Maroc
>  * Pakistan
>
> À compter du 1er août 2019, le paramètre **Vue** définira le contenu cartographique retourné pour les régions/pays supplémentaires ci-dessus. Assurez-vous d’avoir configuré le paramètre Vue de façon appropriée pour les API REST et les SDK utilisés par vos services.
>  
>
>  **API REST** :
>  
>  Vérifiez que vous avez configuré le paramètre Vue comme il convient. Le paramètre Vue spécifie quel contenu géopolitique sensible est retourné par les services Azure Maps. 
>
>  API REST Azure Maps concernées :
>    
>    * Get Map Tile
>    * Get Map Image 
>    * Get Search Fuzzy
>    * Get Search POI
>    * Get Search POI Category
>    * Get Search Nearby
>    * Get Search Address
>    * Get Search Address Structured
>    * Get Search Address Reverse
>    * Get Search Address Reverse Cross Street
>    * Post Search Inside Geometry
>    * Post Search Address Batch Preview
>    * Post Search Address Reverse Batch Preview
>    * Post Search Along Route
>    * Post Search Fuzzy Batch Preview
>
>    
>  **SDK** :
>
>  Assurez-vous d’avoir configuré le paramètre Vue comme il convient et d’utiliser la dernière version du SDK Web et du SDK Android. SDK concernés :
>
>    * Kit de développement logiciel (SDK) web Azure Maps
>    * Android SDK Azure Maps


Le paramètre **Vue** d’Azure Maps (ou « paramètre régional de l'utilisateur ») est un code de pays ISO-3166 à deux lettres qui est utilisé pour afficher les cartes de ce pays ou cette région, spécifiant quel contenu géopolitique sensible est retourné par les services Azure Maps, y compris les frontières et les étiquettes affichées sur les cartes. 

Par défaut, le paramètre Vue est défini sur **Unifié**, même si vous n’avez pas défini ce paramètre dans la requête. Il vous incombe de déterminer l'emplacement de vos utilisateurs, puis de définir correctement le paramètre Affichage correspondant à cet emplacement. Vous avez aussi la possibilité de définir le paramètre d’affichage « Vue=Auto », qui retourne les données cartographiques correspondant à l’adresse IP de la requête.  Le paramètre Affichage d'Azure Maps doit être utilisé dans le respect des lois en vigueur, y compris celles relatives à la cartographie du pays où les cartes, images et autres données et contenus tiers auxquels vous êtes autorisé à accéder via Azure Maps sont mis à disposition.


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
| Auto         | Retourne les données cartographiques correspondant à l’adresse IP de la requête.|   ✓   |    ✓   |     ✓          |
| Unifié      | Affichage unifié (Autres)                  |   ✓   |   ✓     |     ✓          |
