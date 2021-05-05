---
title: Démarrage rapide de l’interface CLI Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Démarrez avec l’interface CLI Azure Speech. Vous pouvez interagir avec les services Speech que sont la reconnaissance vocale, la synthèse vocale et la traduction vocale sans écrire de code.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/28/2021
ms.author: trbye
ms.openlocfilehash: e0a2646dad0be024573c8d02bf4e2749eaeb20c2
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227977"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Bien démarrer avec l’interface CLI Azure Speech

Dans cet article, vous allez découvrir comment utiliser l’interface de ligne de commande CLI Speech pour accéder aux services Speech que sont la reconnaissance vocale, la synthèse vocale et la traduction vocale sans écrire de code. L’interface CLI Speech est pleinement utilisable en production et peut servir à automatiser des workflows simples dans le service Speech, à l’aide de scripts `.bat` ou shell.

Cet article suppose que vous avez une bonne connaissance pratique de l’invite de commandes, du terminal ou de PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Utilisation de base

Cette section présente quelques commandes SPX de base qui sont souvent utiles pour les premiers tests et les premières expérimentations. Commencez par afficher l'aide intégrée à l'outil en exécutant la commande suivante.

```console
spx
```

Vous pouvez rechercher des rubriques d’aide par mot clé. Par exemple, entrez la commande suivante pour afficher la liste des exemples d’utilisation de l’interface CLI Speech :

```console
spx help find --topics "examples"
```

Entrez la commande suivante pour afficher les options de la commande Recognize :

```console
spx help recognize
```

Commandes d’aide supplémentaires listées dans la colonne de droite. Vous pouvez entrer ces commandes pour obtenir une aide détaillée sur les sous-commandes.

## <a name="speech-to-text-speech-recognition"></a>Reconnaissance vocale

Servons-nous de l’interface CLI Speech pour effectuer une conversion de parole en texte (reconnaissance vocale) en utilisant le micro par défaut de votre système. Une fois la commande entrée, SPX commence à écouter l’audio sur l’appareil d’entrée actif et s’arrête dès que vous appuyez sur **ENTRÉE**. La parole enregistrée est ensuite reconnue et convertie en texte dans la sortie de la console.

>[!IMPORTANT]
> Si vous utilisez un conteneur Docker,`--microphone` ne fonctionne pas.

Exécutez cette commande :

```console
spx recognize --microphone
```

Avec l’interface CLI Speech, vous pouvez aussi effectuer une reconnaissance vocale à partir d’un fichier audio.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Si vous effectuez une reconnaissance vocale à partir d’un fichier audio situé dans un conteneur Docker, vérifiez que le fichier audio se trouve dans le répertoire que vous avez monté à l’étape précédente.

N’oubliez pas que si vous êtes bloqué ou que vous souhaitez en savoir plus sur les options de reconnaissance de l’interface CLI Speech, tapez simplement :

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Synthèse vocale

La commande suivante prend le texte en entrée et génère la synthèse vocale sur l’appareil de sortie actif (par exemple, les haut-parleurs de votre ordinateur).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Vous pouvez aussi enregistrer la sortie synthétisée dans un fichier. Dans cet exemple, nous allons créer un fichier nommé `my-sample.wav` dans le répertoire dans lequel la commande est exécutée.

```console
spx synthesize --text "Enjoy using the Speech CLI." --audio output my-sample.wav
```

Ces exemples supposent que vous testez en anglais. Cependant, la synthèse vocale est prise en charge dans de nombreuses langues. Vous pouvez extraire une liste complète de voix via cette commande ou dans la [page de prise en charge des langues](./language-support.md).

```console
spx synthesize --voices
```

Voici comment utiliser l’une des voix que vous avez découvertes.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

N’oubliez pas que si vous êtes bloqué ou que vous souhaitez en savoir plus sur les options de synthèse de l’interface CLI Speech, tapez simplement :

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Traduction par reconnaissance vocale

Avec l’interface CLI Speech, vous pouvez aussi effectuer une traduction par reconnaissance vocale. Exécutez cette commande pour capturer le son à partir de votre microphone par défaut et générer la traduction sous forme de texte. Gardez à l’esprit que vous devez indiquer les langues `source` et `target` avec la commande `translate`.

```console
spx translate --microphone --source en-US --target ru-RU
```

Quand vous effectuez une traduction en plusieurs langues, séparez les codes de langue avec `;`.

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Si vous souhaitez enregistrer la sortie de votre traduction, utilisez l’indicateur `--output`. Dans cet exemple, vous allez aussi lire à partir d’un fichier.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> Pour connaître la liste de toutes les langues prises en charge avec le code de paramètres régionaux correspondant, consultez l’article [Langues et paramètres régionaux](language-support.md).

N’oubliez pas que si vous êtes bloqué ou que vous souhaitez en savoir plus sur les options de traduction de l’interface CLI Speech, tapez simplement :

```console
spx help translate
```

## <a name="next-steps"></a>Étapes suivantes

* [Options de configuration de l’interface CLI Speech](./spx-data-store-configuration.md)
* [Opérations de traitement par lots avec l’interface CLI Speech](./spx-batch-operations.md)
