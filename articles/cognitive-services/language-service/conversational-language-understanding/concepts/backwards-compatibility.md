---
title: Compatibilité descendante de Compréhension du langage courant (CLU)
titleSuffix: Azure Cognitive Services
description: Découvrir la compatibilité descendante entre LUIS et Compréhension du langage courant
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: f4c354780cd5d32c2c801b76bde2b8b6abd2d555
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097609"
---
# <a name="backwards-compatibility-with-luis-applications"></a>Compatibilité descendante avec les applications LUIS

Vous pouvez réutiliser une partie du contenu de vos applications LUIS existantes dans Compréhension du langage courant. Quand vous utilisez des projets de Compréhension du langage courant, vous pouvez :
* Créer des projets de langage courant CLU à partir de fichiers JSON d’application LUIS.
* Créez des applications LUIS que vous pouvez connecter à des projets de workflow d’orchestration.  
  
> [!NOTE]
> Ce guide suppose que vous avez créé une ressource de langue. Si vous démarrez avec le service, consultez le [guide de démarrage rapide](../quickstart.md). 

## <a name="import-a-luis-application-json-file-into-conversational-language-understanding"></a>Importer un fichier JSON LUIS dans Compréhension du langage courant

Pour importer un fichier JSON d’application LUIS, cliquez sur l’icône à côté de **Créer un projet** et sélectionnez **Importer**. Sélectionnez ensuite le fichier LUIS. Quand vous importez un nouveau projet dans Compréhension du langage courant, vous pouvez sélectionner un fichier JSON d’application LUIS exporté pour que le service crée automatiquement un projet avec les fonctionnalités actuellement disponibles.

:::image type="content" source="../media/import.png" alt-text="Capture d’écran montrant le bouton Importer pour les projets de langage courant." lightbox="../media/import.png":::

### <a name="supported-features"></a>Fonctionnalités prises en charge
Quand vous importez l’application JSON LUIS dans CLU, un projet **Conversations** est créé avec les fonctionnalités suivantes :

|**Fonctionnalité**|**Remarques**|
| :- | :- |
|Intentions|Toutes vos intentions sont transférées sous forme d’intentions CLU avec les mêmes noms.|
|Entités ML|Toutes vos entités ML sont transférées sous forme d’entités CLU avec les mêmes noms. Les étiquettes ML sont conservées et utilisées pour entraîner le composant appris de l’entité. Les entités ML structurées sont transférées uniquement sous forme d’entités de niveau supérieur. Les sous-entités individuelles sont ignorées.|
|Énoncés|Tous vos énoncés LUIS sont transférés sous forme d’énoncés CLU avec leur intention et les étiquettes d’entité. Les étiquettes d’entité ML structurées prennent en compte uniquement les étiquettes d’entité de niveau supérieur, les étiquettes de sous-entité individuelles sont ignorées.|
|Culture|La langue principale du projet Conversation est la culture de l’application LUIS. Si la culture n’est pas prise en charge, l’importation échoue. |

### <a name="unsupported-features"></a>Fonctionnalités non prises en charge

Quand vous importez l’application JSON LUIS dans CLU, certaines fonctionnalités sont ignorées, mais elles ne vous empêchent pas d’importer l’application. Les fonctionnalités suivantes sont ignorées :

|**Fonctionnalité**|**Remarques**|
| :- | :- |
|Paramètres de l’application|Les paramètres de type Normaliser la ponctuation, Normaliser les signes diacritiques et Utiliser toutes les données d’entraînement ont été conçus afin d’améliorer les prédictions pour les intentions et les entités. Les nouveaux modèles dans CLU ne sont pas sensibles aux petits changements, comme la ponctuation, et ne sont donc pas disponibles comme paramètres.|
|Fonctionnalités|Les caractéristiques de liste d’expressions et les caractéristiques pour les intentions sont toutes ignorées. Les caractéristiques ont été conçues pour introduire une compréhension sémantique dans LUIS que CLU peut fournir par défaut avec ses nouveaux modèles.|
|Modèles|Des modèles (schémas) étaient utilisés pour couvrir le manque de qualité dans la classification des intentions. Les nouveaux modèles dans CLU sont censés être plus performants sans ces schémas.|
|Entités Pattern.Any|Les entités Pattern.Any étaient utilisées pour couvrir le manque de qualité de l’extraction d’entités ML. Les nouveaux modèles dans CLU sont censés être plus performants sans les entités Pattern.Any.|
|Entités Regex| Non prise en charge pour le moment |
|Entités ML structurées| Non prise en charge pour le moment |
|Lister les entités | Non prise en charge pour le moment |
|Entités prédéfinies | Non prise en charge pour le moment |
|Caractéristiques d’entité obligatoires dans les entités ML | Non prise en charge pour le moment |
|Caractéristiques d’entité non obligatoires dans les entités ML | Non prise en charge pour le moment |
|Rôles | Non prise en charge pour le moment |

## <a name="use-a-published-luis-application-in-conversational-language-understanding-orchestration-projects"></a>Utiliser une application LUIS publiée dans des projets d’orchestration de Compréhension du langage courant

Vous pouvez vous connecter uniquement aux applications LUIS publiées qui appartiennent à la ressource de langue que vous utilisez pour Compréhension du langage courant. Vous pouvez remplacer la ressource de création par une ressource **S** de langue dans les applications **Europe Ouest**. Consultez la [documentation LUIS](../../../luis/luis-how-to-azure-subscription.md#assign-luis-resources) pour savoir comment attribuer une ressource différente à votre application LUIS. Vous pouvez également exporter les applications LUIS dans votre ressource de langue. Vous devez entraîner et publier des applications LUIS pour qu’elles apparaissent dans Compréhension du langage courant si vous voulez les connecter à des projets d’orchestration.


## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Compréhension du langage courant](../overview.md)
