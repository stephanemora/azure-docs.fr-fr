---
title: Prise en charge linguistique - API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Liste des langages naturels pris en charge par l’API Analyse de texte. Cet article décrit les langues prises en charge pour chaque opération.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/14/2021
ms.author: aahi
ms.openlocfilehash: 0e897b71f1f5f99ce2834768a72bf99b9773301f
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112078255"
---
# <a name="text-analytics-api-v3-language-support"></a>Prise en charge linguistique de l’API Analyse de texte v3 

> [!NOTE]
> Des langues sont ajoutées à mesure que de nouvelles versions de modèle sont publiées pour des fonctionnalités Analyse de texte spécifiques. Consultez [Gestion des versions des modèles](concepts/model-versioning.md) pour connaître la version de modèle la plus récente pour les fonctionnalités que vous utilisez, et obtenir plus d’informations.   

#### <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment-analysis)

| Langage              | Code langue | Prise en charge de v3 | À partir de la version de modèle v3 : |              Notes |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinois simplifié    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` également accepté |
| Chinois traditionnel   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Néerlandais                 |     `nl`      |     ✓      |         2019-10-01        |                    |
| Anglais               |     `en`      |     ✓      |         2019-10-01         |                    |
| Français                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Allemand                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hindi                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| Italien               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japonais              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Coréen                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norvégien (Bokmål)   |     `no`      |     ✓      |         2020-04-01         |                    |
| Portugais (Brésil)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugais (Portugal) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` également accepté |
| Espagnol               |     `es`      |     ✓      |         2019-10-01         |                    |
| Turc               |     `tr`      |     ✓       |         2020-04-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Exploration des opinions (v3.1-preview uniquement)

| Langage              | Code langue | À partir de la version de modèle v3 : |              Notes |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Anglais               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconnaissance d’entité nommée (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Les entités « Person », « Location » et « Organization » sont retournées pour les autres langues marquées d’un astérisque *.

| Langage               | Code langue | Prise en charge de v3 | À partir de la version de modèle v3 : |       Notes        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arabe                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinois simplifié     |   `zh-hans`   |     ✓      |               15-01-2021        | `zh` également accepté |
| Chinois traditionnel   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Tchèque                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Danois                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Néerlandais                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Anglais                |     `en`      |     ✓      |               2019-10-01        |                    |
| Finnois               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Français                 |     `fr`      |     ✓      |               15-01-2021        |                    |
| Allemand                 |     `de`      |     ✓      |               15-01-2021        |                    |
| Hébreu                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Hongrois             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Italien               |     `it`      |     ✓       |               15-01-2021        |                    |
| Japonais              |     `ja`      |     ✓       |               15-01-2021        |                    |
| Coréen                |     `ko`      |     ✓       |               15-01-2021        |                    |
| Norvégien (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` également accepté |
| Polonais                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugais (Brésil)   |    `pt-BR`    |     ✓       |               15-01-2021        |                    |
| Portugais (Portugal) |    `pt-PT`    |     ✓       |               15-01-2021        | `pt` également accepté |
| Russe              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Espagnol               |     `es`      |     ✓       |               2020-04-01        |                    |
| Suédois               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Turc               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Extraction de phrases clés](#tab/key-phrase-extraction)

| Langage              | Code langue |  Prise en charge de v3 | À partir de la version de modèle v3 : |       Notes        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Afrikaans             |     `af`      |     ✓      |                2020-07-01                 |                    |
| Bulgare             |     `bg`      |     ✓      |                2020-07-01                 |                    |
| Catalan               |     `ca`      |     ✓      |                2020-07-01                 |                    |
| Chinois simplifié    |     `zh-hans` |     ✓      |                2021-06-01                 |                    |
| Croate              |     `hr`      |     ✓      |                2020-07-01                 |                    |
| Danois                |     `da`      |     ✓      |                2019-10-01                 |                    |
| Néerlandais                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Anglais               |     `en`      |     ✓      |                2019-10-01                 |                    |
| Estonien              |     `et`      |     ✓      |                2020-07-01                 |                    |
| Finnois               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Français                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Allemand                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Grec                 |     `el`      |     ✓      |                2020-07-01                 |                    |
| Hongrois             |     `hu`      |     ✓      |                2020-07-01                 |                    |
| Italien               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Indonésien            |     `id`      |     ✓      |                2020-07-01                 |                    |
| Japonais              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Coréen                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Letton               |     `lv`      |     ✓      |                2020-07-01                 |                    |
| Norvégien (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` également accepté |
| Polonais                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugais (Brésil)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portugais (Portugal) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` également accepté |
| Roumain              |     `ro`      |     ✓      |                2020-07-01                 |                    |
| Russe               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Espagnol               |     `es`      |     ✓      |                2019-10-01                 |                    |
| Slovaque                |     `sk`      |     ✓      |                2020-07-01                 |                    |
| Slovène             |     `sl`      |     ✓      |                2020-07-01                 |                    |
| Suédois               |     `sv`      |     ✓      |                2019-10-01                 |                    |
| Turc               |     `tr`      |     ✓      |                2020-07-01                 |                    |

#### <a name="entity-linking"></a>[Liaison d’entités](#tab/entity-linking)

| Langage | Code langue |  Prise en charge de v3 | À partir de la version de modèle v3 : | Notes |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Anglais  |     `en`      |     ✓      |                2019-10-01                 |       |
| Espagnol  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Informations d’identification personnelle (PII)](#tab/pii)

| Langage               | Code langue | Prise en charge de v3 | À partir de la version de modèle v3 : |       Notes        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinois simplifié     |   `zh-hans`   |     ✓      |               15-01-2021        | `zh` également accepté |
| Anglais                |     `en`      |     ✓      |               2020-07-01        |                    |
| Français                 |     `fr`      |     ✓      |               15-01-2021        |                    |
| Allemand                 |     `de`      |     ✓      |               15-01-2021        |                    |
| Italien               |     `it`      |     ✓       |               15-01-2021        |                    |
| Japonais              |     `ja`      |     ✓       |               15-01-2021        |                    |
| Coréen                |     `ko`      |     ✓       |               15-01-2021        |                    |
| Portugais (Brésil)   |    `pt-BR`    |     ✓       |               15-01-2021        |                    |
| Portugais (Portugal) |    `pt-PT`    |     ✓       |               15-01-2021        | `pt` également accepté |
| Espagnol               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Détection de la langue](#tab/language-detection)

L’API Analyse de texte peut détecter une grande variété de langues, de variantes, de dialectes ainsi que certaines langues régionales/culturelles et peut retourner les langues détectées avec leur nom et leur code. Les paramètres du code de langue de la fonctionnalité Détection de langue de cette API sont conformes à la norme [BCP-47](https://tools.ietf.org/html/bcp47), avec la plupart d’entre eux conformes aux identificateurs [ISO-639-1](https://www.iso.org/iso-639-language-codes.html). 

Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez essayer Détection de langue pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `unknown`.

| Langage | Code de langue | Prise en charge de v3 | À partir de la version de modèle v3 : |
|:-|:-:|:-:|:-:|
|Afrikaans|`af`|✓|    |
|Albanais|`sq`|✓|    |
|Amharique|`am`|✓|05-01-2021|
|Arabe|`ar`|✓|    |
|Arménien|`hy`|✓|    |
|Assamais|`as`|✓|05-01-2021|
|Azéri|`az`|✓|05-01-2021|
|Basque|`eu`|✓|    |
|Biélorusse|`be`|✓|    |
|Bengali|`bn`|✓|    |
|Bosniaque|`bs`|✓|2020-09-01|
|Bulgare|`bg`|✓|    |
|Birman|`my`|✓|    |
|Catalan|`ca`|✓|    |
|Khmer central|`km`|✓|    |
|Chinois|`zh`|✓|    |
|Chinois (simplifié)|`zh_chs`|✓|    |
|Chinois traditionnel|`zh_cht`|✓|    |
|Corse|`co`|✓|05-01-2021|
|Croate|`hr`|✓|    |
|Tchèque|`cs`|✓|    |
|Danois|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Maldivien|`dv`|✓|    |
|Néerlandais|`nl`|✓|    |
|Anglais|`en`|✓|    |
|Espéranto|`eo`|✓|    |
|Estonien|`et`|✓|    |
|Fidjien|`fj`|✓|2020-09-01|
|Finnois|`fi`|✓|    |
|Français|`fr`|✓|    |
|Galicien|`gl`|✓|    |
|Géorgien|`ka`|✓|    |
|Allemand|`de`|✓|    |
|Grec|`el`|✓|    |
|Goudjrati|`gu`|✓|    |
|Haïtien|`ht`|✓|    |
|Hausa|`ha`|✓|05-01-2021|
|Hébreu|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong blanc|`mww`|✓|2020-09-01|
|Hongrois|`hu`|✓|    |
|Islandais|`is`|✓|    |
|Igbo|`ig`|✓|05-01-2021|
|Indonésien|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandais|`ga`|✓|    |
|Italien|`it`|✓|    |
|Japonais|`ja`|✓|    |
|Javanais|`jv`|✓|05-01-2021|
|Kannada|`kn`|✓|    |
|Kazakh|`kk`|✓|2020-09-01|
|Kinyarwanda|`rw`|✓|05-01-2021|
|Kirghiz|`ky`|✓|05-01-2021|
|Coréen|`ko`|✓|    |
|Kurde|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latin|`la`|✓|    |
|Letton|`lv`|✓|    |
|Lituanien|`lt`|✓|    |
|Luxembourgeois|`lb`|✓|05-01-2021|
|Macédonien|`mk`|✓|    |
|Malgache|`mg`|✓|2020-09-01|
|Malais|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltais|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Mongol|`mn`|✓|05-01-2021|
|Népalais|`ne`|✓|05-01-2021|
|Norvégien|`no`|✓|    |
|Norvégien (Nynorsk)|`nn`|✓|    |
|Odia|`or`|✓|    |
|Pashto|`ps`|✓|    |
|Persan|`fa`|✓|    |
|Polonais|`pl`|✓|    |
|Portugais|`pt`|✓|    |
|Pendjabi|`pa`|✓|    |
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Roumain|`ro`|✓|    |
|Russe|`ru`|✓|    |
|Samoan|`sm`|✓|2020-09-01|
|Serbe|`sr`|✓|    |
|Shona|`sn`|✓|05-01-2021|
|Sindhi|`sd`|✓|05-01-2021|
|Cingalais|`si`|✓|    |
|Slovaque|`sk`|✓|    |
|Slovène|`sl`|✓|    |
|Somali|`so`|✓|    |
|Espagnol|`es`|✓|    |
|Soundanais|`su`|✓|05-01-2021|
|Swahili|`sw`|✓|    |
|Suédois|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitien|`ty`|✓|2020-09-01|
|Tadjik|`tg`|✓|05-01-2021|
|Tamoul|`ta`|✓|    |
|Tatar|`tt`|✓|05-01-2021|
|Télougou|`te`|✓|    |
|Thaï|`th`|✓|    |
|Tibétain|`bo`|✓|05-01-2021|
|Tigrigna|`ti`|✓|05-01-2021|
|Tonga|`to`|✓|2020-09-01|
|Turc|`tr`|✓|05-01-2021|
|Turkmène|`tk`|✓|05-01-2021|
|Ukrainien|`uk`|✓||
|Ourdou|`ur`|✓||
|Ouzbek|`uz`|✓||
|Vietnamien|`vi`|✓||
|Gallois|`cy`|✓|| 
|Xhosa|`xh`|✓|05-01-2021|
|Yiddish|`yi`|✓||
|Yoruba|`yo`|✓|05-01-2021|
|Yucatec Maya| `yua` | ✓| |
|Zoulou|`zu`|✓|05-01-2021|


---

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)   
* [Versions des modèles](concepts/model-versioning.md)
