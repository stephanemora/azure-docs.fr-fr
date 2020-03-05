---
title: Ensemble des entités prédéfinies – LUIS
titleSuffix: Azure Cognitive Services
description: Cet article contient des listes des entités prédéfinies qui sont incluses dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270583"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entités par culture dans votre modèle LUIS

Language Understanding (LUIS) fournit des entités prédéfinies. Quand une entité prédéfinie est incluse dans votre application, LUIS inclut la prédiction d’entité correspondante dans la réponse du point de terminaison. Tous les exemples d’énoncés sont également étiquetés avec l’entité. Le comportement des entités prédéfinies **ne peut pas** être modifié. Sauf indication contraire, les entités prédéfinies sont disponibles dans tous les paramètres régionaux de l’application LUIS (cultures). Le tableau suivant montre les entités prédéfinies qui sont pris en charge pour chaque culture.

|Culture|Sous-cultures|Notes|
|--|--|--|
|Chinois|[zh-CN](#chinese-entity-support)||
|Néerlandais|[nl-NL](#dutch-entity-support)||
|Anglais|[en-US (États-Unis)](#english-american-entity-support)||
|Français|[fr-CA (Canada)](#french-canadian-entity-support), [fr-FR (France)](#french-france-entity-support), ||
|Allemand|[de-DE](#german-entity-support)||
|Italien|[it-IT](#italian-entity-support)||
|Japonais|[ja-JP](#japanese-entity-support)||
|Coréen|[ko-KR](#korean-entity-support)||
|Portugais|[pt-BR (Brésil)](#portuguese-brazil-entity-support)||
|Espagnol|[es-ES (Espagne)](#spanish-spain-entity-support), [es-MX (Mexique)](#spanish-mexico-entity-support)||
|Turc|[turc](#turkish-entity-support)|Aucune prise en charge des entités prédéfinies en turc|

## <a name="prediction-endpoint-runtime"></a>Runtime du point de terminaison de prédiction

La disponibilité d’une entité prégénérée dans un langage spécifique est déterminée par la version du runtime de point de terminaison de prédiction.

## <a name="chinese-entity-support"></a>Prise en charge de l'entité Chinois

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```zh-CN``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Prise en charge de l’entité Néerlandais

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```nl-NL``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Prise en charge de l’entité Anglais (États-Unis)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```en-US``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Prise en charge de l'entité Français (France)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```fr-FR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Prise en charge de l'entité Français (Canada)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```fr-CA``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Prise en charge de l’entité Allemand

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```de-DE``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Prise en charge de l’entité Italien

_Résolution_ de l’âge prégénéré, de la devise, de la dimension, du nombre, du pourcentage italiens modifiée de la préversion v2 et v3.

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```it-IT``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Prise en charge de l’entité Japonais

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```ja-JP``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, -   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Prise en charge de l’entité Coréen

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```ko-KR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    -   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Prise en charge de l'entité Portugais (Brésil)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```pt-BR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Prise en charge de l'entité Espagnol (Espagne)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```es-ES``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    V2, V3   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    V2, V3   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    V2, V3   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Prise en charge de l'entité Espagnol (Mexique)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```es-MX``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    -   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    -   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>Date<br>daterange<br>time<br>plage horaire   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nombre](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Voir les notes sur [entités prédéfinies désapprouvée](luis-reference-prebuilt-deprecated.md)

KeyPhrase n’est pas disponible dans toutes les sous-cultures du portugais (Brésil) – ```pt-BR```.

## <a name="turkish-entity-support"></a>Prise en charge de l’entité Turc

**Aucune entité prédéfinie n’est prise en charge en turc.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuer aux cultures d’entités prédéfinies
Les entités prédéfinies sont développées dans le projet open source Recognizers-Text. [Contribuez](https://github.com/Microsoft/Recognizers-Text) au projet. Ce projet inclut des exemples de devise par culture.

GeographyV2 et PersonName ne sont pas inclus dans le projet Texte de reconnaissances. En cas de problèmes avec ces entités prédéfinies, veuillez ouvrir un [demande de support](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) et [currency](luis-reference-prebuilt-currency.md).
