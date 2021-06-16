---
title: Prise en charge linguistique – Form Recognizer
titleSuffix: Azure Applied AI Services
description: Apprenez-en davantage sur les langues disponibles avec Form Recognizer.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 05/10/2021
ms.author: lajanuar
ms.openlocfilehash: cdf67dcaa3a2bb8a21b380756332a85e00967d06
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111887003"
---
# <a name="language-support-for-form-recognizer"></a>Prise en charge linguistique pour Form Recognizer

 Ce tableau répertorie les langues écrites que chaque service Form Recognizer prend en charge.

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->

## <a name="layout-and-custom-model"></a>Disposition et modèle personnalisé

### <a name="v21"></a>[v2.1](#tab/v2-1)

|Langage| Code langue |
|:-----|:----:|
|Afrikaans|`af`|
|Albanais |`sq`|
|Asturien |`ast`|
|Basque  |`eu`|
|Bichelamar   |`bi`|
|Breton    |`br`|
|Catalan    |`ca`|
|Cebuano    |`ceb`|
|Chamorro  |`ch`|
|Chinois (simplifié) | `zh-Hans`|
|Chinois (traditionnel) | `zh-Hant`|
|Cornique     |`kw`|
|Corse      |`co`|
|Tatar de Crimée (latin)  |`crh`|
|Tchèque | `cs` |
|Danois | `da` |
|Néerlandais | `nl` |
|Anglais (imprimé et manuscrit) | `en` |
|Estonien  |`et`|
|Fidjien |`fj`|
|Filipino  |`fil`|
|Finnois | `fi` |
|Français | `fr` |
|Frioulan  | `fur` |
|Galicien   | `gl` |
|Allemand | `de` |
|Gilbertin    | `gil` |
|Groenlandais   | `kl` |
|Créole haïtien  | `ht` |
|Hani  | `hni` |
|Hmong daw (latin) | `mww` |
|Hongrois | `hu` |
|Indonésien   | `id` |
|Interlingua  | `ia` |
|Inuktitut (Latin)  | `iu`  |
|Irlandais    | `ga` |
|Italien | `it` |
|Japonais | `ja` |
|Javanais | `jv` |
|K’iche’  | `quc` |
|Créole du Cap-Vert | `kea` |
|Kachin (latin) | `kac` |
|Karakalpak | `kaa` |
|Cachoube | `csb` |
|Khasi  | `kha` |
|Coréen | `ko` |
|Kurde (latin) | `kur` |
|Luxembourgeois  | `lb` |
|Malais (latin)  | `ms` |
|Mannois  | `gv` |
|Napolitain   | `nap` |
|Norvégien | `no` |
|Occitan | `oc` |
|Polonais | `pl` |
|Portugais | `pt` |
|Romanche  | `rm` |
|Scots  | `sco` |
|Gaélique écossais  | `gd` |
|Slovène  | `slv` |
|Espagnol | `es` |
|Swahili (latin)  | `sw` |
|Suédois | `sv` | 
|Tatar (latin)  | `tat` |
|Tétoum    | `tet` |
|Turc | `tr` |
|Haut sorabe  | `hsb` |
|Ouzbek (latin)     | `uz` |
|Volapük   | `vo` |
|Haut-valaisan    | `wae` |
|Frison occidental | `fy` |
|Yucatec Maya | `yua` |
|Zhuang | `za` |
|Zoulou  | `zu` |

### <a name="v20"></a>[v2.0](#tab/v2-0)

|Langage| Code langue |
|:-----|:----:|
Chinois (simplifié) | `zh-Hans`|
|Néerlandais | `nl` |
|Anglais (imprimé et manuscrit) | `en` |
|Français | `fr` |
|Allemand | `de` |
|Italien | `it` |
|Japonais | `ja` |
|Portugais | `pt` |
|Espagnol | `es` |

-----

## <a name="prebuilt-receipt-and-business-card"></a>Reçu et carte de visite prédéfinis

>[!NOTE]
 > Vous n’avez pas besoin de spécifier de paramètres régionaux. Il s’agit d’un paramètre facultatif et la technologie d’apprentissage profond de Form Recognizer détecte automatiquement la langue du texte figurant dans votre image.

### <a name="v21"></a>[v2.1](#tab/v2-1)

Les reçus et cartes de visite prédéfinis prennent en charge l’ensemble des reçus et cartes de visite anglais avec les paramètres régionaux suivants :

|Langage| Code des paramètres régionaux |
|:-----|:----:|
|Anglais (Australie)|`en-au`|
|Anglais (Canada)|`en-ca`|
|Anglais (Royaume-Uni)|`en-gb`|
|Anglais (Inde)|`en-in`|
|Anglais (États-Unis)| `en-us`|

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Cette fonctionnalité n’est pas disponible dans la version de l’API sélectionnée.

-----

## <a name="prebuilt-invoice"></a>Facture prédéfinie

### <a name="v21"></a>[v2.1](#tab/v2-1)

Langage| Code des paramètres régionaux |
|:-----|:----:|
|Anglais (États-Unis)|fr-FR|

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Cette fonctionnalité n’est pas disponible dans la version de l’API sélectionnée.

-----

## <a name="prebuilt-identity-documents"></a>Documents d’identité prédéfinis

### <a name="v21"></a>[v2.1](#tab/v2-1)

> [!NOTE]
> Cette technologie est actuellement disponible pour les permis de conduire délivrés aux États-Unis et la page biographique des passeports internationaux (à l’exclusion des visas et autres documents de voyage).

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Cette fonctionnalité n’est pas disponible dans la version de l’API sélectionnée.

-----

> [!div class="nextstepaction"]
> [Essayer Form Recognizer](https://aka.ms/fott-2.1-ga)
