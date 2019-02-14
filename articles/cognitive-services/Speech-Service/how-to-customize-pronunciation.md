---
title: Personnaliser la prononciation - Services Speech
titlesuffix: Azure Cognitive Services
description: Découvrez comment personnaliser la prononciation avec le service Speech. La prononciation personnalisée vous permet de définir l’écriture et la forme phonétiques d’un mot ou d’un terme. Cela peut être utile pour traiter les termes personnalisés que sont notamment les noms et les acronymes. Pour commencer, vous n’avez besoin que d’un fichier de prononciation (un simple fichier .txt).
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 0a3dfce10fc8ea76bc8f99e2459295bc637017dc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878406"
---
# <a name="enable-custom-pronunciation"></a>Activer la prononciation personnalisée

La prononciation personnalisée vous permet de définir l’écriture et la forme phonétiques d’un mot ou d’un terme. Cela peut être utile pour traiter les termes personnalisés que sont notamment les noms et les acronymes. Pour commencer, vous n’avez besoin que d’un fichier de prononciation (un simple fichier .txt).

Voici comment cela fonctionne. Dans un simple fichier .txt, vous pouvez saisir plusieurs entrées de prononciation personnalisées. La structure est comme suit :

```
Display form <Tab> Spoken form <Newline>
```

Plusieurs exemples sont présentés dans le tableau suivant :

| Forme écrite (en anglais) | Forme orale (en anglais) |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK (Computational Network Toolkit de Microsoft Research) | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>Exigences pour la forme orale
La forme orale doit être en minuscules, ce que vous pouvez appliquer pendant l’importation. Vous devez également fournir des vérifications dans l’importateur de données. Aucune tabulation n’est autorisée dans la forme orale ou la forme écrite. Néanmoins, d’autres caractères interdits peuvent être présents dans la forme écrite (par exemple, ~ et ^).

Chaque fichier .txt peut avoir plusieurs entrées, comme le montre l’image suivante :

![Exemples de prononciation d’acronyme](media/stt/custom-speech-pronunciation-file.png)

La forme orale est la séquence phonétique de la forme écrite. Elle est composée de lettres, de mots ou de syllabes. Actuellement, il n’existe aucun guide ou ensemble de normes pour vous aider à formuler la forme orale.

## <a name="supported-pronunciation-characters"></a>Caractères de prononciation pris en charge
Pour l’heure, la prononciation personnalisée prend uniquement en charge l’anglais (en-US) et l’allemand (de-DE). Les jeux de caractères dont vous pouvez vous servir pour exprimer la forme orale d’un terme (dans le fichier de prononciation personnalisée) sont présentés dans le tableau suivant :

| Langage | Caractères |
|---------- |----------|
| Anglais (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Allemand (de-de) | ä, ö, ü, ?, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> La forme écrite d’un terme (dans un fichier de prononciation) doit être identique à celle utilisée dans un jeu de données d’adaptation de langue.

## <a name="requirements-for-the-display-form"></a>Exigences pour la forme écrite
Une forme écrite peut être constituée d’un mot, d’un terme, d’un acronyme personnalisé unique ou de mots composés combinant des mots existants. Vous pouvez également entrer des prononciations différentes pour les mots courants.

>[!NOTE]
>Nous vous déconseillons d’utiliser cette fonctionnalité pour reformuler des mots courants ou modifier la forme orale. Il est préférable d’exécuter le décodeur pour voir si certains mots inhabituels (tels que des abréviations, des mots techniques ou des mots étrangers) ne sont pas décodés de manière incorrecte. S’ils ne sont pas décodés correctement, ajoutez-les dans le fichier de prononciation personnalisée. Dans le modèle de langage, vous devez toujours utiliser uniquement la forme écrite d’un mot.

## <a name="requirements-for-the-file-size"></a>Exigences pour la taille de fichier
La taille du fichier .txt qui contient les entrées de prononciation est limitée à 1 mégaoctet (1 Ko pour les clés de niveau gratuit). En principe, vous n’avez pas besoin de charger de grandes quantités de données via ce fichier. La taille de la plupart des fichiers de prononciation personnalisée est normalement de quelques kilo-octets (Ko). L’encodage du fichier .txt pour tous les paramètres régionaux doit être la marque d'ordre d'octet UTF-8. Pour les paramètres régionaux anglais, ANSI est également acceptable.

## <a name="next-steps"></a>Étapes suivantes
* Améliorez la précision de la reconnaissance en créant un [modèle acoustique personnalisé](how-to-customize-acoustic-models.md).
* Améliorez la précision de la reconnaissance en créant un [modèle de langage personnalisé](how-to-customize-language-model.md).
