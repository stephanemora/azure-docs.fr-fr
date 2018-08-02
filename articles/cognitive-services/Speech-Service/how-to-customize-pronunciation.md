---
title: Service Speech d’Azure Cognitive Services | Microsoft Docs
description: Découvrez comment personnaliser la prononciation avec le service Speech de Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: f926bf8686a279c93d8f1ed03e8a0ecf3f4a9632
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347289"
---
# <a name="enable-custom-pronunciation"></a>Activer la prononciation personnalisée
La prononciation personnalisée permet aux utilisateurs de définir l’écriture et la forme phonétiques d’un mot ou d’un terme. Cela peut être utile pour traiter les termes personnalisés tels que les noms ou les acronymes. Vous avez uniquement besoin d’un fichier de prononciation (un simple fichier .txt).

Voici comment cela fonctionne. Dans un simple fichier .txt, vous pouvez saisir plusieurs entrées de prononciation personnalisées. La structure est comme suit :

```
Display form <Tab> Spoken form <Newline>
```

*Exemples :*

| Forme écrite (en anglais) | Forme orale (en anglais) |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK (Computational Network Toolkit de Microsoft Research) | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>Exigences pour la forme orale
La forme orale doit être en minuscules, ce qui peut être appliqué pendant l’importation. En outre, vous devez fournir des vérifications dans l’importateur de données. Aucune tabulation n’est autorisée dans la forme orale ou la forme écrite. Néanmoins, d’autres caractères interdits peuvent être présents dans la forme écrite (par exemple, ~ et ^).

Chaque fichier .txt peut avoir plusieurs entrées. Observez la capture d’écran suivante pour avoir un exemple :

![Capture d’écran de bloc-notes avec plusieurs entrées pour la prononciation d’acronymes](media/stt/custom-speech-pronunciation-file.png)

La forme orale est la séquence phonétique de la forme écrite. Elle est composée de lettres, de mots ou de syllabes. Actuellement, il n’existe aucun guide ou ensemble de normes pour vous aider à formuler la forme orale. 

## <a name="supported-pronunciation-characters"></a>Caractères de prononciation pris en charge
Pour l’instant, la prononciation personnalisée prend uniquement en charge l’anglais (en-US) et l’allemand (de-DE). Le jeu de caractères qui peut servir à exprimer la forme orale d’un terme (dans le fichier de prononciation personnalisée) est indiqué dans le tableau suivant : 

| Langage | Caractères |
|---------- |----------|
| Anglais (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Allemand (de-de) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> La forme écrite d’un terme (dans un fichier de prononciation) doit être identique à celle utilisée dans un jeu de données d’adaptation du langage.

## <a name="requirements-for-the-display-form"></a>Exigences pour la forme écrite
Une forme écrite peut uniquement être un mot, un terme, un acronyme personnalisé ou des mots composés combinant des mots existants. Vous pouvez également entrer des prononciations différentes pour les mots courants. 

>[!NOTE]
>Nous vous déconseillons d’utiliser cette fonctionnalité pour reformuler des mots courants ou modifier la forme orale. Il est préférable d’exécuter le décodeur pour voir si certains mots inhabituels (tels que des abréviations, des mots techniques et les mots étrangers) ne sont pas décodés correctement. S’ils ne sont pas décodés correctement, ajoutez-les dans le fichier de prononciation personnalisée. Dans le modèle de langage, vous devez uniquement et toujours utiliser la forme écrite d’un mot. 

## <a name="requirements-for-the-file-size"></a>Exigences pour la taille de fichier
La taille du fichier .txt qui contient les entrées de prononciation est limitée à 1 Mo. En règle générale, vous n’avez pas besoin de charger de grandes quantités de données via ce fichier. La taille de la plupart des fichiers de prononciation personnalisée est susceptible d’être de l’ordre de quelques Ko. L’encodage du fichier .txt pour tous les paramètres régionaux doit être la marque d'ordre d'octet UTF-8. Pour les paramètres régionaux anglais, ANSI est également acceptable.

## <a name="next-steps"></a>Étapes suivantes
* Améliorez la précision de la reconnaissance en créant un [modèle acoustique personnalisé](how-to-customize-acoustic-models.md)
* Améliorez la précision de la reconnaissance en créant un [modèle de langage personnalisé](how-to-customize-language-model.md)
