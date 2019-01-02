---
title: Personnalisation de la traduction - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Utilisez Microsoft Translator Hub pour générer votre propre système de traduction automatique à l’aide de la terminologie et du style de votre choix.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: f02c495c7448334529de2d5b42cda02206daea0d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877970"
---
# <a name="customize-your-text-translations"></a>Personnaliser vos traductions de texte

Microsoft Custom Translator est une fonctionnalité du service Microsoft Translator, qui permet aux utilisateurs de personnaliser la traduction automatique neurale avancée de Microsoft Translator lors de la traduction de texte à l’aide de l’API de traduction de texte Translator Text (version 3 uniquement).

Cette fonctionnalité peut également être utilisée pour personnaliser la traduction vocale quand elle est utilisée avec [Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Avec Custom Translator, vous pouvez générer des systèmes de traduction neuronaux qui comprennent la terminologie utilisée dans votre entreprise et secteur. Le système de traduction personnalisé s’intègre aux applications, sites web et flux de travail existants.

### <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Utilisez vos documents traduits précédemment (dépliants, pages web, documentation, etc.) pour générer un système de traduction qui reflète la terminologie et le style propres à un domaine, plutôt qu’un système de traduction générique. Les utilisateurs peuvent charger des documents TMX, XLIFF, TXT, DOCX et XLSX.  

Le système accepte également des données parallèles au niveau du document, mais pas encore alignées au niveau de la phrase. Si les utilisateurs ont accès à des versions du même contenu dans plusieurs langues, mais dans des documents distincts, Custom Translator est capable de faire correspondre automatiquement les phrases dans les documents.  Le système peut utiliser également des données unilingues dans une ou les deux langages pour compléter les données d’apprentissage parallèles et améliorer les traductions.

Le système personnalisé est alors disponible via un appel normal à l’API de traduction de texte Translator Text via le paramètre de catégorie.

Étant donné le type approprié et la quantité de données d’apprentissage, il n’est pas rare d’attendre des gains compris entre 5 et 10, ou encore plus de points BLEU sur la qualité de la traduction grâce à Custom Translator.

Vous trouverez plus de détails sur les différents niveaux de personnalisation en fonction des données disponibles dans le [guide de l’utilisateur de Custom Translator](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub Microsoft Translator

L’ancien hub Microsoft Translator peut être utilisé pour obtenir une traduction automatique basée sur des statistiques. [En savoir plus](https://www.microsoft.com/en-us/translator/hub.aspx)

## <a name="custom-translator-versus-hub"></a>Custom Translator ou hub

|   | **Concentrateur** | **Custom Translator**|
|:-----|:----:|:----:|
|État de la fonctionnalité de personnalisation   | Disponibilité générale  | Disponibilité générale |
| Version de l'API de texte  | V2 uniquement   | V3 uniquement |
| Personnalisation TA statistique | Oui   | Non  |
| Personnalisation TA neuronale | Non     | Oui |
| Nouvelle personnalisation unifiée des services vocaux | Non     | Oui |
| [Sans trace](https://www.aka.ms/notrace) | Oui  | Oui |

## <a name="collaborative-translations-framework"></a>Infrastructure de traductions collaboratives

> [!NOTE]
> Depuis le 1 février 2018, AddTranslation() et AddTranslationArray() ne sont plus disponibles pour l’API de traduction de texte Translator Text v2.0. Ces méthodes échouent, et rien n’est écrit. L’API de traduction de texte Translator Text v3.0 ne prend pas en charge ces méthodes.

>Une fonctionnalité similaire est disponible dans l’API du hub Translator. Voir [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer un système de langue personnalisé à l’aide de Custom Translator](https://aka.ms/CustomTranslatorDocs)
