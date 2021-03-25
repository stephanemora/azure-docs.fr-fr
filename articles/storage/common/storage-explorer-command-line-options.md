---
title: Options de ligne de commande de l’Explorateur Stockage Azure | Microsoft Docs
description: Documentation des options de ligne de commande de démarrage de l’Explorateur Stockage Azure
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743119"
---
# <a name="azure-storage-explorer-command-line-options"></a>Options de ligne de commande de l’Explorateur Stockage Azure

L’Explorateur Stockage Microsoft Azure possède un ensemble d’options de ligne de commande qui peuvent être ajoutées au démarrage de l’application. La plupart des options de ligne de commande sont destinées au débogage ou à la résolution des problèmes.

## <a name="command-line-options"></a>Options de ligne de commande
Option  | Description
:------- | :-----------
`--debug`/`--prod`  | Démarrez l’application en mode débogage ou de production. En mode débogage, les données des pièces jointes locales sont stockées dans le stockage local de l’application et ne sont pas chiffrées. Les propriétés masquées s’affichent dans le panneau Propriétés pour les nœuds de ressource sélectionnés. Le niveau de détail du journal sera défini pour imprimer les messages de débogage révélant la logique d’installation interne de l’Explorateur Stockage. La valeur par défaut est `--prod`.
`--lang`  | Démarrez l’application avec une langue donnée. Par exemple : `--lang="zh-Hans"`.
`--disable-gpu` | Démarrez l’application sans l’accélération GPU.
`--auto-open-dev-tools` | Laissez l’application ouvrir la fenêtre des outils de développement dès que la fenêtre du navigateur s’affiche. Cette option est utile lorsque vous souhaitez atteindre un point d’arrêt sur une ligne dans le code de démarrage de la fenêtre du navigateur.
`--verbosity` | Définissez le niveau de détail de la journalisation de l’Explorateur Stockage. Les niveaux de détail de la journalisation pris en charge sont les suivants : `debug`, `verbose`, `info`, `warn`, `error` et `silent`. Par exemple : `--verbosity=verbose`. Lors de l’exécution en mode de production, le niveau de détail par défaut est `info`. Lors de l’exécution en mode débogage, le niveau de détail du journal sera toujours `debug`.
`--log-dir` | Définissez le répertoire dans lequel enregistrer les fichiers journaux. Par exemple : `--log-dir=path_to_a_directory`.

Exemple de démarrage de l’Explorateur Stockage avec des options de ligne de commande personnalisées

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> Ces options de ligne de commande peuvent changer dans les nouvelles versions de l’Explorateur Stockage.

## <a name="when-to-use-command-line-options"></a>Quand utiliser les options de ligne de commande

Certaines options de ligne de commande peuvent être utilisées pour personnaliser l’Explorateur Stockage. Pour les options avec des paramètres utilisateur correspondants, tels que `--lang`. Nous vous recommandons d’utiliser les paramètres utilisateur au lieu de l’option de ligne de commande. 

Les autres options de ligne de commande peuvent être utiles pour le débogage et la résolution des problèmes. Si vous rencontrez un problème dans l’Explorateur Stockage, reproduire le problème en mode débogage peut nous aider à des informations plus détaillées à examiner.