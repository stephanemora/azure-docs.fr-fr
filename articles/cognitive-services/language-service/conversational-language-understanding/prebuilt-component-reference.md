---
title: Composants d’entité prédéfinis pris en charge
titleSuffix: Azure Cognitive Services
description: Découvrez quelles entités qui peuvent être détectées automatiquement dans Compréhension du langage courant
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: fc7188b647e6e299390179c68bcf9c9742a018f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097608"
---
# <a name="supported-prebuilt-entity-components"></a>Composants d’entité prédéfinis pris en charge

Compréhension du langage courant vous permet d’ajouter des composants prédéfinis à des entités. Les composants prédéfinis prédisent automatiquement les types courants d’énoncés. Pour plus d’informations sur les composants, consultez l’article [Composants d’entité](concepts/entity-components.md).

## <a name="reference"></a>Informations de référence

Les composants prédéfinis suivants sont disponibles dans Compréhension du langage courant.

| Type | Description | Langues prises en charge |
| --- | --- | --- |
| Quantity.Age | Âge d’une personne ou d’une chose. Par exemple : « 30 ans », « 9 mois » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Quantity.Number | Nombre cardinal sous forme numérique ou texte. Par exemple : « trente », « 23 », « 14,5 », « deux et demi » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Quantity.Percentage | Pourcentage utilisant le symbole % ou le mot « pour cent ». Par exemple : « 10 % », « 5,6 pour cent » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Quantity.Ordinal | Nombre ordinal sous forme numérique ou texte. Par exemple : « premier », « second », « dernier », « 10ème » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Quantity.Dimension | Dimensions spéciales, comme la longueur, la distance, le volume, la surface et la vitesse. Par exemple : « deux miles », « 650 kilomètres carrés », « 35 km/h » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Quantity.Temperature | Température en degrés Celsius ou Fahrenheit. Par exemple : « 32 F », « 34 degrés Celsius », « 2 deg C » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Quantity.Currency | Montants monétaires incluant une devise. Par exemple, « 1000,00 dollars US », « £20,00 », « 67,5 milliards de dollars US » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Datetime | Dates et heures. Par exemple : « 23 juin, 1976 », « 7h », « 18:49 », « demain à 7h00 », « semaine prochaine » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| E-mail | Adresses e-mail. Par exemple : « user@contoso.com », « user_name@contoso.com », « user.name@contoso.com » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| Numéro de téléphone | Numéro de téléphone aux États-Unis Par exemple : « 123-456-7890 », « +1 123 456 7890 », « (123)456-7890 » | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |
| URL | URL et liens de sites web. | Anglais, chinois, français, italien, allemand, portugais (Brésil), espagnol |

## <a name="prebuilt-components-in-multilingual-projects"></a>Composants prédéfinis dans les projets multilingues

Dans les projets de conversation multilingue, vous pouvez activer n’importe quel composant prédéfini. Le composant sera prédit seulement si la langue de la requête est prise en charge par le composant prédéfini. La langue est spécifiée dans la demande ou, si elle n’est pas fournie, elle est définie par défaut sur la langue principale de l’application.

## <a name="next-steps"></a>Étapes suivantes

[Composants de l’entité](concepts/entity-components.md) 
