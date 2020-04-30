---
title: Localisation du Lecteur multimédia Azure
description: Prise en charge de plusieurs langues pour les utilisateurs dont la langue n’est pas l’anglais.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81725878"
---
# <a name="localization"></a>Localisation #

La prise en charge de plusieurs langues permet aux utilisateurs ayant des langues autres que l’anglais d’interagir en mode natif avec le lecteur. Le Lecteur multimédia Azure instanciera avec un dictionnaire global de langues, ce qui permettra de localiser les messages d’erreur en fonction de la langue de la page. Un développeur peut également créer une instance de lecteur avec une langue définie forcée. La langue par défaut est l’anglais (en).

> [!NOTE]
> Cette fonctionnalité est encore soumise à des tests et, par conséquent, peut faire l’objet de bogues.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Le Lecteur multimédia Azure prend actuellement en charge les langues suivantes avec les codes de langue correspondants :

| Langage            | Code | Langage                | Code   | Langage                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Anglais {par défaut}   | en   | Croate                | hr     | Roumain                | ro           |
| Arabe              | ar   | Hongrois               | hu     | Slovaque                  | sk           |
| Bulgare           | bg   | Indonésien              | id     | Slovene                 | sl           |
| Catalan             | ca   | Islandais               | is     | Serbe - Cyrillique      | sr-cyrl-cs   |
| Tchèque               | cs   | Italien                 | it     | Serbe - Latin         | sr-latn-rs   |
| Danois              | da   | Japonais                | ja     | Russe                 | ru           |
| Allemand              | de   | Kazakh                  | kk     | Suédois                 | sv           |
| Grec               | el   | Coréen                  | ko     | Thaï                    | th           |
| Espagnol             | es   | Lituanien              | lt     | Tagalog                 | tl           |
| Estonien            | et   | Letton                 | lv     | Turc                 | tr           |
| Basque              | eu   | Malais               | ms     | Ukrainien               | uk           |
| Farsi               | fa   | Norvégien - BokmÃ¥l     | nb     | Ourdou                    | ur           |
| Finnois             | fi   | Néerlandais                   | nl     | Vietnamien              | vi           |
| Français              | fr   | Norvégien - Nynorsk     | nn     | Chinois - simplifié    | zh-hans      |
| Galicien            | gl   | Polonais                  | pl     | Chinois - traditionnel   | zh-hant      |
| Hébreu              | he   | Portugais - Brésil     | pt-br  |                         |              |
| Hindi               | hu   | Portugais - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Si vous ne voulez pas de localisation, vous devez forcer la langue en anglais

## <a name="next-steps"></a>Étapes suivantes ##

- [Démarrage rapide du Lecteur multimédia Azure](azure-media-player-quickstart.md)