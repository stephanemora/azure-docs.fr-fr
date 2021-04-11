---
title: Terminologie - Custom Translator
titleSuffix: Azure Cognitive Services
description: Liste des termes utilisés dans les articles de Custom Translator.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658477"
---
# <a name="custom-translator-terminology"></a>Terminologie de Custom Translator

Le tableau suivant présente une liste de termes que vous pouvez trouver lorsque vous travaillez avec [Custom Translator](https://portal.customtranslator.azure.ai).

| Mot ou expression|Définition|
|------------------|-----------|
| Langue source | La langue source est la langue de démarrage que vous souhaitez convertir dans une autre langue (la « cible »).|
| Langue cible| La langue cible est la langue que la traduction automatique doit vous fournir après avoir reçu la langue source. |
| Fichier unilingue | Un fichier monolingue ne dispose que d’une seule langue, non associée à un autre fichier dans une autre langue. |
| Fichiers parallèles | Un fichier parallèle est la combinaison de deux fichiers contenant un texte correspondant. Un fichier contient la langue source, et l’autre fichier la langue cible.|
| Alignement de phrases| Un jeu de données parallèle doit avoir des phrases alignées avec les phrases qui représentent le même texte dans les deux langues. Par exemple, dans un fichier parallèle source, la première phrase doit, en théorie, correspondre à la première phrase dans le fichier parallèle cible.|
| Texte aligné | Une des étapes les plus importantes de la validation d’un fichier consiste à aligner les phrases dans les documents parallèles. Les choses sont exprimées différemment dans diverses langues. L’ordre des mots est également différent selon les langues. Cette étape aligne les phrases ayant le même contenu afin qu’elles puissent être utilisées pour l’apprentissage. Un mauvais alignement de phrases peut indiquer un problème avec un ou les deux fichiers. |
| Césure/non-césure des mots | La césure est l’opération consistant à définir les limites entre les mots. De nombreux systèmes d’écriture utilisent un espace comme limite entre les mots. La non-césure des mots désigne la suppression d’un marqueur visible qui a peut-être été inséré entre les mots lors d’une étape précédente. |
| Délimiteurs   | Les délimiteurs permettent de diviser une phrase en segments ou délimitent la marge entre les phrases. En anglais, par exemple, les espaces délimitent les mots, les deux-points, les points-virgules délimitent les clauses, et les points délimitent des phrases. |
| Fichiers d’apprentissage | Un fichier d’apprentissage apprend au système de traduction automatique comment mapper une langue (la source) à une langue cible (la cible). Plus vous fournissez de données, plus le système est performant. |
| Paramétrage des fichiers | Ces fichiers sont souvent dérivés au hasard à partir du jeu d’apprentissage (si vous n’avez sélectionné aucun jeu de paramétrage). Les phrases automatiquement sélectionnées et utilisées pour paramétrer le système et vérifier son bon fonctionnement. Si vous souhaitez créer un modèle de traduction universel et vos propres fichiers de paramétrage, assurez-vous qu’ils représentent une série aléatoire de phrases de différents domaines |
| Fichiers de test| Ces fichiers sont souvent choisis au hasard dans le jeu d’apprentissage (si vous n’avez sélectionné aucun jeu de test). Ces phrases visent à évaluer la précision du modèle de traduction. Puisque ces phrases sont utilisées pour vous assurer une traduction précise par le système, vous pouvez souhaiter créer un ensemble de tests et le charger dans le traducteur. Cela permet de s’assurer que ces phrases sont utilisées dans l’évaluation du système (génération d’un score BLEU).   |
| Fichier combiné   | Type de fichier dans lequel la source et les phrases traduites sont contenues dans le même fichier. Formats de fichiers pris en charge (.tmx, .xliff, .xlf, .ici, .xlsx). |
| Archiver un fichier | Fichier contenant d’autres fichiers. Formats de fichiers pris en charge (zip, gz, tgz).  |
| Score BLEU   | La méthode [BLEU](what-is-bleu-score.md) est la méthode standard pour l’évaluation de la précision du modèle de traduction. Bien qu’il existe d’autres méthodes d’évaluation, Microsoft Translator s’appuie sur la méthode BLEU pour garantir un niveau de précision aux propriétaires de projets.
