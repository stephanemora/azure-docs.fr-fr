---
title: Guide de référence de l’API REST de traduction de texte
titleSuffix: Azure Cognitive Services
description: Affichez une liste avec des liens vers les API REST de traduction de texte.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
ms.openlocfilehash: 2b0c4676ca7d085e5fbe042dd2a23fe0ae42db9c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524114"
---
# <a name="text-translation-rest-api"></a>API REST de traduction de texte

La traduction de texte est une fonctionnalité basée sur le cloud du service Azure Translator qui fait partie de la famille du Service cognitif Azure des API REST. L’API de traduction de texte traduit le texte entre paires de langues dans toutes les [langues et dialectes pris en charge](../../language-support.md). Les méthodes disponibles sont répertoriées dans le tableau ci-dessous :

| Requête| Méthode| Description|
|---------|--------------|---------|
| [**langues**](v3-0-languages.md) | **GET** | Retourne l’ensemble de langues actuellement prises en charge par les méthodes de **traduction**, de **translittération** et de **dictionnaire**. Cette requête ne nécessite pas d’en-tête d’authentification et vous n’avez besoin d’aucune ressource Traducteur pour visualiser le jeu de langues pris en charge.|
|[**traduire**](v3-0-translate.md) | **POST**| Traduire le texte d’une langue source spécifiée dans le texte d’une langue cible.|
|[**translittérer**](v3-0-transliterate.md) |  **POST** | Mapper le script ou l’alphabet de langue source vers un script ou un alphabet de langue cible.
|[**détecter**](v3-0-detect.md) | **POST** | Identifiez la langue source. |
|[**breakSentence**](v3-0-break-sentence.md) | **POST** | Retourne un tableau d’entiers représentant la longueur des phrases d’un texte source. |
| [**dictionnaire/recherche**](v3-0-dictionary-lookup.md) | **POST** | Retourne des alternatives pour les traductions de mots uniques. |
| [**dictionnaire/exemples**](v3-0-dictionary-lookup.md) | **POST** | Retourne la manière dont un terme est utilisé en contexte. |

> [!div class="nextstepaction"]
> [Créer une ressource Traducteur dans le Portail Azure.](/translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [Démarrage rapide : API REST et votre langue de programmation](../quickstart-translator.md)
