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
ms.date: 09/12/2020
ms.author: aahi
ms.openlocfilehash: 185a6a26350f7803d109e26d8985510380b8cc16
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056589"
---
# <a name="text-analytics-api-v3-language-support"></a>Prise en charge linguistique de l’API Analyse de texte v3 

> [!IMPORTANT]
> La version 3.x de l’API Analyse de texte n’est pas disponible actuellement dans les régions suivantes : Inde Centre, Émirats arabes unis Nord, Chine Nord 2, Chine Est.


#### <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment-analysis)

| Langage              | Code langue | Prise en charge de v2 | Prise en charge de v3 | À partir de la version de modèle v3 : |              Notes |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinois simplifié    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` également accepté |
| Chinois traditionnel   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Danois               |     `da`      |     ✓      |            |                            |                    |
| Néerlandais                 |     `nl`      |     ✓      |            |                            |                    |
| Anglais               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finnois               |     `fi`      |     ✓      |            |                            |                    |
| Français                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Allemand                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Grec                 |     `el`      |     ✓      |            |                            |                    |
| Italien               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japonais              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coréen                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvégien (Bokmål)   |     `no`      |     ✓      |     ✓       |        2020-07-01         |                    |
| Polonais                |     `pl`      |     ✓      |            |                            |                    |
| Portugais (Portugal) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` également accepté |
| Russe               |     `ru`      |     ✓      |            |                            |                    |
| Espagnol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Suédois               |     `sv`      |     ✓      |            |                            |                    |
| Turc               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Exploration des opinions (v3.1-preview uniquement)

| Langage              | Code langue | À partir de la version de modèle v3 : |              Notes |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Anglais               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Reconnaissance d’entité nommée (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER v3 prend actuellement en charge seulement la langue anglaise et la langue espagnole. Si vous appelez NER v3 avec une autre langue, l’API retourne les résultats de la version 2.1 à condition que la langue soit prise en charge dans v2.1.
> * v2.1 retourne l’ensemble complet des entités disponibles uniquement en anglais, chinois simplifié, français, allemand et espagnol.  Les entités « Person », « Location » et « Organization » sont retournées pour les autres langues prises en charge.

| Langage               | Code langue | Prise en charge de v2.1 | Prise en charge de v3 | À partir de la version de modèle v3 : |       Notes        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabe                |     `ar`      |     ✓      |            |                                 |                    |
| Tchèque                 |     `cs`      |     ✓      |            |                                 |                    |
| Chinois simplifié     |   `zh-hans`   |     ✓      |            |                                 | `zh` également accepté |
| Chinois traditionnel   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Danois                |     `da`      |     ✓      |            |                                 |                    |
| Néerlandais                 |     `nl`      |     ✓      |            |                                 |                    |
| Anglais                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finnois               |     `fi`      |     ✓      |            |                                 |                    |
| Français                 |     `fr`      |     ✓      |            |                                 |                    |
| Allemand                 |     `de`      |     ✓      |            |                                 |                    |
| Hébreu                |     `he`      |     ✓      |            |                                 |                    |
| Hongrois             |     `hu`      |     ✓      |            |                                 |                    |
| Italien               |     `it`      |     ✓      |            |                                 |                    |
| Japonais              |     `ja`      |     ✓      |            |                                 |                    |
| Coréen                |     `ko`      |     ✓      |            |                                 |                    |
| Norvégien (Bokmål)   |     `no`      |     ✓      |            |                                 | `nb` également accepté |
| Polonais                |     `pl`      |     ✓      |            |                                 |                    |
| Portugais (Portugal) |    `pt-PT`    |     ✓      |            |                                 | `pt` également accepté |
| Portugais (Brésil)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Russe              |     `ru`      |     ✓      |            |                                 |                    |
| Espagnol               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Suédois               |     `sv`      |     ✓      |            |                                 |                    |
| Turc               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Extraction de phrases clés](#tab/key-phrase-extraction)

> [!NOTE]
> Les versions de modèle d’extraction de phrases clés antérieures à 2020-07-01 ont une limite de 64 caractères. Cette limite n’est pas présente dans les versions de modèle ultérieures.

| Langage              | Code langue | Prise en charge de v2 | Prise en charge de v3 | Disponible à partir de la version de modèle v3 : |       Notes        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Néerlandais                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Anglais               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finnois               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Français                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Allemand                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italien               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japonais              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coréen                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norvégien (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` également accepté |
| Polonais                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugais (Portugal) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` également accepté |
| Portugais (Brésil)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Russe               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Espagnol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Suédois               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Liaison d’entités](#tab/entity-linking)

| Langage | Code langue | Prise en charge de v2 | Prise en charge de v3 | Disponible à partir de la version de modèle v3 : | Notes |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Anglais  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Espagnol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Détection de la langue](#tab/language-detection)

L’API Analyse de texte peut détecter une grande variété de langues, variantes, dialectes et certaines langues régionales/culturelles.  La fonction de détection de la langue renvoie le « script » d’une langue. Par exemple, pour la phrase « I have a dog », elle renvoie `en` à la place de `en-US`. Le chinois est un cas spécial unique : la fonction de détection de la langue renvoie `zh_CHS` ou `zh_CHT` si elle parvient à déterminer le script dans le texte fourni. Dans les situations où un script spécifique n’est pas identifié dans un document chinois, elle renvoie simplement `zh`.

Nous ne publions pas la liste exacte des langues pour cette fonctionnalité, mais elle peut détecter une grande variété de langues, de variantes, de dialectes, et de certaines langues régionales/de culture. 

Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez essayer Détection de langue pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `unknown`.

---

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)   
