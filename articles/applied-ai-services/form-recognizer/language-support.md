---
title: Prise en charge linguistique – Form Recognizer
titleSuffix: Azure Applied AI Services
description: Apprenez-en davantage sur les langues disponibles avec Form Recognizer.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 10/07/2021
ms.author: lajanuar
ms.openlocfilehash: 5c9df61e7f4430a623f354294863b2a1bc06b7c8
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164276"
---
# <a name="language-support-for-form-recognizer"></a>Prise en charge linguistique pour Form Recognizer

 Ce tableau répertorie les langues écrites que chaque service Form Recognizer prend en charge.

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

## <a name="layout-and-custom-model"></a>Disposition et modèle personnalisé

Les listes suivantes couvrent les langues en GA (disponibilité générale) dans la version 2.1 ainsi que les nouvelles préversions de la préversion 3.0 de Form Recognizer. Ces langues sont prises en charge par les modèles de disposition et les modèles personnalisés. La préversion peut inclure des améliorations apportées aux langues en GA.

> [!NOTE]
> **Code de langue facultatif**
>
> Les modèles universels basés sur le deep learning de Form Recognizer extraient tout le texte multilingue de vos documents, notamment les lignes de texte contenant plusieurs langues, et ne nécessitent pas la spécification d’un code de langue. N’indiquez pas de code de langue en tant que paramètre, sauf si vous êtes sûr de la langue et si vous souhaitez forcer le service à appliquer uniquement le modèle approprié. Sinon, le service pourrait retourner du texte incomplet et incorrect.

Pour utiliser les langues en préversion dans les modèles de disposition et les modèles personnalisés, consultez le [guide de migration de l’API REST v3.0](/v3-migration-guide.md#changes-to-the-rest-api-endpoints) afin de comprendre les différences par rapport à l’API v2.1 en GA, puis accédez aux [guides de démarrage rapide du kit SDK v3.0 (préversion)](quickstarts/try-v3-python-sdk.md) et au [guide de démarrage rapide de l’API REST (préversion)](quickstarts/try-v3-rest-api.md).

### <a name="handwritten-languages"></a>Langues manuscrites
Le tableau suivant liste les langues manuscrites prises en charge par les fonctionnalités des modèles personnalisés et des modèles de disposition de Form Recognizer.

|Langage| Code langue (facultatif) | Préversion ?  |
|:-----|:----:|:----:|
|Anglais|`en`||
|Chinois (simplifié) |`zh-Hans`| preview
|Français |`fr`| preview
|Allemand |`de`| preview
|Italien|`it`| preview
|Portugais |`pt`| preview
|Espagnol |`es`| preview

### <a name="print-languages"></a>Langues d’impression
Le tableau suivant liste les langues d’impression prises en charge par les fonctionnalités des modèles personnalisés et des modèles de disposition de Form Recognizer.

|Langage| Code langue (facultatif) | Préversion ? |
|:-----|:----:|:----:|
|Afrikaans|`af`||
|Albanais |`sq`||
|Asturien |`ast`| |
|Azerbaïdjanais (Latin) | `az` | preview |
|Basque  |`eu`| |
|Biélorusse (cyrillique) | `be` | preview |
|Biélorusse (latin) | `be` | preview |
|Bichelamar   |`bi`| |
|Bosniaque (latin)   |`bs`| preview |
|Breton    |`br`| |
|Bulgare |`bg`| preview |
|Buryat (cyrillique)|`bua`| preview |
|Catalan    |`ca`| |
|Cebuano    |`ceb`| |
|Chamorro  |`ch`| |
|Chinois (simplifié) | `zh-Hans`| |
|Chinois traditionnel | `zh-Hant`| |
|Cornique     |`kw`| |
|Corse      |`co`| |
|Tatar de Crimée (latin)|`crh`| |
|Croate |`hr`| preview |
|Tchèque | `cs` | |
|Danois | `da` | |
|Néerlandais | `nl` | |
|Anglais | `en` | |
|Erzya (Cyrillique) |`myv`| preview |
|Estonien  |`et`|  |
|Féroïen |`fo`| preview |
|Fidjien |`fj`| |
|Filipino  |`fil`| |
|Finnois | `fi` | |
|Français | `fr` | |
|Frioulan  | `fur` | |
|Gagauz (Latin) |`gag`| preview |
|Galicien   | `gl` | |
|Allemand | `de` | |
|Gilbertin    | `gil` | |
|Groenlandais   | `kl` | |
|Créole haïtien  | `ht` | |
|Hani  | `hni` | |
|Hawaïen |`haw`| preview |
|Hmong daw (latin)| `mww` | |
|Hongrois | `hu` | |
|Islandais |`is`| preview |
|Inari Sami |`smn`| preview |
|Indonésien   | `id` | |
|Interlingua  | `ia` | |
|Inuktitut (Latin) | `iu` | |
|Irlandais    | `ga` | |
|Italien | `it` | |
|Japonais | `ja` | |
|Javanais | `jv` | |
|Quiché  | `quc` | |
|Créole du Cap-Vert | `kea` | |
|Kachin (latin) | `kac` | |
|Karachay-Balkar |`krc`| preview |
|Kara-Kalpak (Latin) | `kaa` | |
|Kara-Kalpak (Cyrillique) | `kaa-cyrl` | preview |
|Cachoube | `csb` | |
|Kazakh (Cyrillique) |`kk-cyrl`| preview |
|Kazakh (Latin) |`kk-latn`| preview |
|Khasi  | `kha` |  |
|Coréen | `ko` | |
|Koryak |`kpy`| preview |
|Kosrae |`kos`| preview |
|Kumyk (Cyrillique) |`kum`| preview |
|Kurde (latin)| `kur` | |
|Kirghiz (cyrillique) |`ky`| preview |
|Lakota |`lkt`| preview |
|Latin|`la`| preview |
|Lituanien|`lt`| preview |
|Bas sorabe|`dsb`| preview |
|Lule Sami|`smj`| preview |
|Luxembourgeois  | `lb` |  |
|Malais (latin) | `ms` |  |
|Maltais|`mt`| preview |
|Mannois  | `gv` |  |
|Maori|`mi`| preview |
|Mongole (cyrillique)|`mn`| preview |
|Monténégrin (cyrillique)|`cnr-cyrl`| preview |
|Montenegrin (latin)|`cnr-latn`| preview |
|Napolitain   | `nap` | |
|Niué|`niu`| preview |
|Nogay|`nog`| preview |
|Same du Nord (latin)|`sme`| preview |
|Norvégien | `no` |  |
|Occitan | `oc` | |
|Ossète|`os`| preview |
|Polonais | `pl` | |
|Portugais | `pt` | |
|Francique ripuaire|`ksh`| preview |
|Roumain | `ro` | preview |
|Romanche  | `rm` | |
|Russe | `ru` | preview |
|Samoan (latin)|`sm`| preview |
|Scots  | `sco` | |
|Gaélique écossais  | `gd` | |
|Serbe (latin) | `sr-latn` | preview |
|Same de Skolt|`sms`| preview |
|Slovaque | `sk` | preview |
|Slovène  | `slv` | |
|Same du Sud|`sma`| preview |
|Espagnol | `es` | |
|Swahili (latin)  | `sw` | |
|Suédois | `sv` | |
|Tadjik (cyrillique)|`tg`| preview |
|Tatar (latin)  | `tat` | |
|Tétoum    | `tet` |  |
|Tonga|`to`|(préversion) |
|Turc | `tr` | |
|Turkmène (latin)|`tk`| preview |
|Touvain|`tyv`| preview |
|Haut sorabe  | `hsb` | |
|Ouzbek (cyrillique)  | `uz-cyrl` |  |
|Ouzbek (latin)     | `uz` |  |
|Volapük   | `vo` | |
|Haut-valaisan    | `wae` |  |
|Gallois     | `cy` | preview |
|Frison occidental | `fy` |  |
|Yucatec Maya | `yua` |  |
|Zhuang | `za` | |
|Zoulou  | `zu` |  |

## <a name="receipt-and-business-card-models"></a>Modèles de reçu et de carte de visite

>[!NOTE]
 > Il n’est pas nécessaire de spécifier de paramètres régionaux. Il s'agit d'un paramètre facultatif. La technologie d’apprentissage profond de Form Recognizer détecte automatiquement la langue du texte figurant dans votre image.

Les reçus et cartes de visite prédéfinis prennent en charge l’ensemble des reçus et cartes de visite anglais avec les paramètres régionaux suivants :

|Langage| Code des paramètres régionaux |
|:-----|:----:|
|Anglais (Australie)|`en-au`|
|Anglais (Canada)|`en-ca`|
|Anglais (Royaume-Uni)|`en-gb`|
|Anglais (Inde)|`en-in`|
|Anglais (États-Unis)| `en-us`|

## <a name="invoice-model"></a>Modèle de facture

Langage| Code des paramètres régionaux |
|:-----|:----:|
|Anglais (États-Unis)|fr-FR|

## <a name="id-documents"></a>Pièces d’identité

Cette technologie est actuellement disponible pour les permis de conduire délivrés aux États-Unis et la page biographique des passeports internationaux (à l’exclusion des visas et autres documents de voyage).

> [!div class="nextstepaction"]
> [Essayer Form Recognizer](https://aka.ms/fott-2.1-ga)

## <a name="general-document"></a>Document général

Langage| Code des paramètres régionaux |
|:-----|:----:|
|Anglais (États-Unis)|fr-FR|

