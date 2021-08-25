---
title: Options de configuration de l’interface CLI Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer et gérer des fichiers de configuration à utiliser avec l’interface CLI Azure Speech.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: lajanuar
ms.openlocfilehash: eeff86e7955f2e8e32059ba1acb220d0dd2a567f
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071517"
---
# <a name="speech-cli-configuration-options"></a>Options de configuration de l’interface CLI Speech

Le comportement de l’interface CLI Speech peut s’appuyer sur des paramètres de fichiers de configuration, que vous pouvez référencer à l’aide d’un symbole `@`. L’interface CLI Speech enregistre un nouveau paramètre dans un nouveau sous-répertoire `./spx/data` créé dans le répertoire de travail actuel de l’interface CLI Speech. Lors de la recherche d’une valeur de configuration, l’interface CLI Speech recherche dans votre répertoire de travail actuel, puis dans le magasin de données dans `./spx/data`, puis dans d’autres magasins de données, y compris un magasin de données en lecture seule final dans le fichier binaire `spx`. 

Quand vous avez suivi le guide de démarrage rapide de l’interface CLI Speech, vous avez utilisé le magasin de données pour enregistrer vos valeurs `@key` et `@region`, donc vous n’aviez pas besoin de les spécifier avec chaque commande `spx`. N’oubliez pas que vous pouvez utiliser des fichiers de configuration pour stocker vos propres paramètres de configuration, voire même pour passer des URL ou d’autres contenus dynamiques générés au moment de l’exécution.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Créer et gérer des fichiers de configuration dans le magasin de données

Cette section montre comment utiliser un fichier de configuration dans le magasin de données local pour stocker et extraire des paramètres de commande à l’aide de `spx config`, et comment stocker la sortie de l’interface CLI Speech à l’aide de l’option `--output`.

L’exemple suivant efface le fichier de configuration `@my.defaults`, ajoute des paires clé-valeur pour **key** et **region** dans le fichier et utilise la configuration dans un appel à `spx recognize`.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Vous pouvez également écrire du contenu dynamique dans un fichier de configuration. Par exemple, la commande suivante crée un modèle vocal personnalisé et stocke l’URL du nouveau modèle dans un fichier de configuration. La commande suivante attend que le modèle défini à cette URL soit prêt à être utilisé pour passer la main.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

L’exemple suivant écrit deux URL dans le fichier de configuration `@my.datasets.txt`. Dans ce scénario, `--output` peut inclure un mot clé **add** facultatif pour créer un fichier de configuration ou compléter l’existant.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Pour plus d’informations sur les fichiers de magasin de données, notamment sur l’utilisation des fichiers de configuration par défaut (`@spx.default`, `@default.config`et `@*.default.config` pour les paramètres par défaut propres à une commande), entrez la commande suivante :

```console
spx help advanced setup
```

## <a name="next-steps"></a>Étapes suivantes 

* [Opérations de traitement par lots avec l’interface CLI Speech](./spx-batch-operations.md)