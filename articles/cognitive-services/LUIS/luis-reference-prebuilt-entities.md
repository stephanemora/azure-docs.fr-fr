---
title: Toutes les entités prédéfinies
titleSuffix: Azure Cognitive Services
description: Cet article contient des listes des entités prédéfinies qui sont incluses dans Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 4d5ce9775e7844fcc82aa993f5b01c7cc7ae4779
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213733"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entités par culture dans votre modèle LUIS

Language Understanding (LUIS) fournit des entités prédéfinies. Quand une entité prédéfinie est incluse dans votre application, LUIS inclut la prédiction d’entité correspondante dans la réponse du point de terminaison. Tous les exemples d’énoncés sont également étiquetés avec l’entité. Le comportement des entités prédéfinies **ne peut pas** être modifié. Sauf indication contraire, les entités prédéfinies sont disponibles dans tous les paramètres régionaux de l’application LUIS (cultures). Le tableau suivant montre les entités prédéfinies qui sont pris en charge pour chaque culture.

|Culture|Sous-cultures|
|--|--|
|Chinois|[zh-CN](#chinese-entity-support)|
|Néerlandais|[nl-NL](#dutch-entity-support)|
|Anglais|[en-US (États-Unis)](#english-american-entity-support)|
|Anglais|[fr-CA (Canada)](#french-canadian-entity-support), [fr-FR (France)](#french-france-entity-support), |
|Allemand|[de-DE](#german-entity-support)|
|Italien|[it-IT](#italian-entity-support)|
|Japonais|[ja-JP](#japanese-entity-support)|
|Coréen|[ko-KR](#korean-entity-support)|
|Portugais|[pt-BR (Brésil)](#portuguese-brazil-entity-support)|
|Espagnol|[es-ES (Espagne)](#spanish-spain-entity-support), [es-MX (Mexique)](#spanish-mexico-entity-support)|

## <a name="chinese-entity-support"></a>Prise en charge de l'entité Chinois

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```zh-CN``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Prise en charge de l’entité Néerlandais

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```nl-NL``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Prise en charge de l’entité Anglais (États-Unis)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```en-US``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Prise en charge de l'entité Français (France)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```fr-FR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Prise en charge de l'entité Français (Canada)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```fr-CA``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Prise en charge de l’entité Allemand

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```de-DE``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Prise en charge de l’entité Italien

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```it-IT``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Prise en charge de l’entité Japonais

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```ja-JP``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Prise en charge de l’entité Coréen

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```ko-KR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    -   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    -   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Prise en charge de l'entité Portugais (Brésil)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```pt-BR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Prise en charge de l'entité Espagnol (Espagne)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```es-ES``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Prise en charge de l'entité Espagnol (Mexique)

Les entités suivantes sont prises en charge :

|Entité prédéfinie|```es-MX``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    -   |
[Devise (monnaie)](luis-reference-prebuilt-currency.md):<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    -   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Nombre](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Voir les notes sur [entités prédéfinies désapprouvée](luis-reference-prebuilt-deprecated.md)

KeyPhrase n’est pas disponible dans toutes les sous-cultures du portugais (Brésil) – ```pt-BR```.

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuer aux cultures d’entités prédéfinies
Les entités prédéfinies sont développées dans le projet open source Recognizers-Text. [Contribuez](https://github.com/Microsoft/Recognizers-Text) au projet. Ce projet inclut des exemples de devise par culture. 

GeographyV2 et PersonName ne sont pas inclus dans le projet Texte de reconnaissances. En cas de problèmes avec ces entités prédéfinies, veuillez ouvrir un [demande de support](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) et [currency](luis-reference-prebuilt-currency.md). 
