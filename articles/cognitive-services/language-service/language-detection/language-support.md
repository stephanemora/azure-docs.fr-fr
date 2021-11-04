---
title: Prise en charge linguistique de Détection de langue
titleSuffix: Azure Cognitive Services
description: Cet article décrit les langages naturels pris en charge par l’API Détection de langue.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 1c71e3d04ff770aefc50732c18fe49bd684e854a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097967"
---
# <a name="language-support-for-language-detection"></a>Prise en charge linguistique de Détection de langue

Utilisez cet article pour connaître les langages naturels pris en charge par Détection de langue.


> [!NOTE]
> Les langues sont ajoutées à mesure que de nouvelles [versions de modèle](how-to/call-api.md#specify-the-language-detection-model) sont publiées. La version de modèle actuelle pour la Détection de la langue est `2021-01-05`.

La fonctionnalité Détection de langue peut détecter une grande variété de langues, de variantes, de dialectes ainsi que certaines langues régionales/culturelles, et peut retourner les langues détectées avec leur nom et leur code. Les paramètres de code de langue retournés sont conformes à la norme [BCP-47](https://tools.ietf.org/html/bcp47) et la plupart d’entre eux sont conformes aux identificateurs [ISO-639-1](https://www.iso.org/iso-639-language-codes.html). 

Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez essayer Détection de langue pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `unknown`.

## <a name="languages-supported-by-language-detection"></a>Langues prises en charge par Détection de langue

| Langage            | Code de langue | À partir de la version de modèle : |
|---------------------|---------------|------------------------------|
| Afrikaans           | `af`          |                              |
| Albanais            | `sq`          |                              |
| Amharique             | `am`          | 05-01-2021                   |
| Arabe              | `ar`          |                              |
| Arménien            | `hy`          |                              |
| Assamais            | `as`          | 05-01-2021                   |
| Azéri         | `az`          | 05-01-2021                   |
| Basque              | `eu`          |                              |
| Biélorusse          | `be`          |                              |
| Bengali             | `bn`          |                              |
| Bosniaque             | `bs`          | 2020-09-01                   |
| Bulgare           | `bg`          |                              |
| Birman             | `my`          |                              |
| Catalan             | `ca`          |                              |
| Khmer central       | `km`          |                              |
| Chinois             | `zh`          |                              |
| Chinois (simplifié)  | `zh_chs`      |                              |
| Chinois traditionnel | `zh_cht`      |                              |
| Corse            | `co`          | 05-01-2021                   |
| Croate            | `hr`          |                              |
| Tchèque               | `cs`          |                              |
| Danois              | `da`          |                              |
| Dari                | `prs`         | 2020-09-01                   |
| Maldivien              | `dv`          |                              |
| Néerlandais               | `nl`          |                              |
| Anglais             | `en`          |                              |
| Espéranto           | `eo`          |                              |
| Estonien            | `et`          |                              |
| Fidjien              | `fj`          | 2020-09-01                   |
| Finnois             | `fi`          |                              |
| Français              | `fr`          |                              |
| Galicien            | `gl`          |                              |
| Géorgien            | `ka`          |                              |
| Allemand              | `de`          |                              |
| Grec               | `el`          |                              |
| Goudjrati            | `gu`          |                              |
| Haïtien             | `ht`          |                              |
| Hausa               | `ha`          | 05-01-2021                   |
| Hébreu              | `he`          |                              |
| Hindi               | `hi`          |                              |
| Hmong blanc           | `mww`         | 2020-09-01                   |
| Hongrois           | `hu`          |                              |
| Islandais           | `is`          |                              |
| Igbo                | `ig`          | 05-01-2021                   |
| Indonésien          | `id`          |                              |
| Inuktitut           | `iu`          |                              |
| Irlandais               | `ga`          |                              |
| Italien             | `it`          |                              |
| Japonais            | `ja`          |                              |
| Javanais            | `jv`          | 05-01-2021                   |
| Kannada             | `kn`          |                              |
| Kazakh              | `kk`          | 2020-09-01                   |
| Kinyarwanda         | `rw`          | 05-01-2021                   |
| Kirghiz             | `ky`          | 05-01-2021                   |
| Coréen              | `ko`          |                              |
| Kurde             | `ku`          |                              |
| Lao                 | `lo`          |                              |
| Latin               | `la`          |                              |
| Letton             | `lv`          |                              |
| Lituanien          | `lt`          |                              |
| Luxembourgeois       | `lb`          | 05-01-2021                   |
| Macédonien          | `mk`          |                              |
| Malgache            | `mg`          | 2020-09-01                   |
| Malais               | `ms`          |                              |
| Malayalam           | `ml`          |                              |
| Maltais             | `mt`          |                              |
| Maori               | `mi`          | 2020-09-01                   |
| Marathi             | `mr`          | 2020-09-01                   |
| Mongol           | `mn`          | 05-01-2021                   |
| Népalais              | `ne`          | 05-01-2021                   |
| Norvégien           | `no`          |                              |
| Norvégien (Nynorsk)   | `nn`          |                              |
| Odia               | `or`          |                              |
| Pashto               | `ps`          |                              |
| Persan             | `fa`          |                              |
| Polonais              | `pl`          |                              |
| Portugais          | `pt`          |                              |
| Pendjabi             | `pa`          |                              |
| Queretaro Otomi     | `otq`         | 2020-09-01                   |
| Roumain            | `ro`          |                              |
| Russe             | `ru`          |                              |
| Samoan              | `sm`          | 2020-09-01                   |
| Serbe             | `sr`          |                              |
| Shona               | `sn`          | 05-01-2021                   |
| Sindhi              | `sd`          | 05-01-2021                   |
| Cingalais             | `si`          |                              |
| Slovaque              | `sk`          |                              |
| Slovène           | `sl`          |                              |
| Somali              | `so`          |                              |
| Espagnol             | `es`          |                              |
| Soundanais           | `su`          | 05-01-2021                   |
| Swahili             | `sw`          |                              |
| Suédois             | `sv`          |                              |
| Tagalog             | `tl`          |                              |
| Tahitien            | `ty`          | 2020-09-01                   |
| Tadjik               | `tg`          | 05-01-2021                   |
| Tamoul               | `ta`          |                              |
| Tatar               | `tt`          | 05-01-2021                   |
| Télougou              | `te`          |                              |
| Thaï                | `th`          |                              |
| Tibétain             | `bo`          | 05-01-2021                   |
| Tigrigna            | `ti`          | 05-01-2021                   |
| Tonga              | `to`          | 2020-09-01                   |
| Turc             | `tr`          | 05-01-2021                   |
| Turkmène             | `tk`          | 05-01-2021                   |
| Ukrainien           | `uk`          |                              |
| Ourdou                | `ur`          |                              |
| Ouzbek               | `uz`          |                              |
| Vietnamien          | `vi`          |                              |
| Gallois               | `cy`          |                              |  
| Xhosa               | `xh`          | 05-01-2021                   |
| Yiddish             | `yi`          |                              |
| Yoruba              | `yo`          | 05-01-2021                   |
| Yucatec Maya        | `yua`         |                              |  
| Zoulou                | `zu`          | 05-01-2021                   |

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la détection de langue](overview.md)
