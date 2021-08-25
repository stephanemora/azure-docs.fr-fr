---
title: Opérations de traitement par lots de l’interface CLI Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer des opérations de traitement par lots de reconnaissance vocale et de synthèse vocale avec l’interface CLI Speech.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: lajanuar
ms.openlocfilehash: c3481edc4b66afc4556ae6d7e957f2dfee9b2e69
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122072331"
---
# <a name="speech-cli-batch-operations"></a>Opérations de traitement par lots de l’interface CLI Speech

Les opérations de traitement par lots sont des tâches courantes quand vous utilisez les services Azure Speech. Dans cet article, vous allez découvrir comment effectuer des opérations de traitement par lots de reconnaissance vocale et de synthèse vocale avec l’interface CLI Speech. Plus précisément, vous allez découvrir comment :

* Exécuter une reconnaissance vocale par lots sur un répertoire de fichiers audio
* Exécuter une synthèse vocale par lots en itérant au sein d’un fichier `.tsv`

## <a name="batch-speech-to-text-speech-recognition"></a>Reconnaissance vocale par lots

Le service Speech est souvent utilisé pour effectuer une reconnaissance vocale à partir de fichiers audio. Dans cet exemple, vous allez apprendre à itérer au sein d’un répertoire avec l’interface CLI Speech pour capturer la sortie de reconnaissance pour chaque fichier `.wav`. L’indicateur `--files` est utilisé pour pointer vers le répertoire où sont stockés les fichiers audio, et le caractère générique `*.wav` est utilisé pour indiquer à l’interface CLI Speech d’exécuter une reconnaissance sur chaque fichier portant l’extension `.wav`. La sortie de chaque fichier de reconnaissance est écrite sous la forme d’une valeur séparée par des tabulations dans `speech_output.tsv`.

> [!NOTE]
> L’argument `--threads` peut également être utilisé dans la section suivante pour les commandes `spx synthesize`, et les threads disponibles dépendent du processeur et du pourcentage de charge actuel.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Voici un exemple de structure de fichier de sortie.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Synthèse vocale par lots

Le moyen le plus simple d’exécuter une synthèse vocale par lots consiste à créer un fichier `.tsv` (valeurs séparées par des tabulations) et à utiliser la commande `--foreach` de l’interface CLI Speech. Vous pouvez créer un fichier `.tsv` avec votre éditeur de texte préféré. Pour les besoins de cet exemple, nous allons le nommer `text_synthesis.tsv` :

>[!IMPORTANT]
> Quand vous copiez le contenu de ce fichier texte, vérifiez que votre fichier contient une **tabulation** plutôt que des espaces entre l’emplacement du fichier et le texte. Parfois, lors de la copie du contenu de cet exemple, les tabulations sont converties en espaces, ce qui entraîne l’échec de la commande `spx` lors de son exécution.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Ensuite, exécutez une commande pour pointer vers `text_synthesis.tsv`, effectuer une synthèse sur chaque champ `text` et écrire le résultat sur le chemin `audio.output` correspondant dans un fichier `.wav`.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Cette commande équivaut à exécuter `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **pour chaque** enregistrement du fichier `.tsv`.

Points à prendre en compte :

* Les en-têtes de colonne, `audio.output` et `text`, correspondent respectivement aux arguments de ligne de commande `--audio output` et `--text`. Les arguments de ligne de commande en plusieurs parties, comme `--audio output`, doivent être mis en forme dans le fichier sans espaces, ni tirets de début, ni points de séparation entre les chaînes, par exemple `audio.output`. Tous les autres arguments de ligne de commande existants peuvent être ajoutés au fichier sous forme de colonnes supplémentaires suivant ce modèle.
* Quand le fichier est mis en forme de cette manière, aucun argument supplémentaire n’est obligatoire pour `--foreach`.
* Veillez à séparer les valeurs du `.tsv` par une **tabulation**.

Toutefois, si votre fichier `.tsv` comporte des en-têtes de colonnes qui **ne correspondent pas** aux arguments de ligne de commande, comme dans l’exemple suivant :

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Vous pouvez remplacer ces noms de champs par les bons arguments selon la syntaxe suivante dans l’appel `--foreach`. Il s’agit du même appel que ci-dessus.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de l’interface CLI Speech](./spx-overview.md)
* [Démarrage rapide sur l’interface CLI Speech](./spx-basics.md)
