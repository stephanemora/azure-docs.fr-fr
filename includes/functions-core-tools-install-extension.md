---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a050ce62f745591608249b41ba56992d8fd35204
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935780"
---
## <a name="register-extensions"></a>Inscrire des extensions

À l’exception des déclencheurs HTTP et de minuteur, les liaisons Functions dans les versions 2.x et ultérieures du runtime sont implémentées sous la forme de packages d’extension. Dans les versions 2.x et ultérieures du runtime Azure Functions, vous devez inscrire explicitement les extensions pour les types de liaisons utilisés dans vos fonctions. Les exceptions à cette obligation sont les liaisons HTTP et les déclencheurs de minuteur, qui ne nécessitent pas d’extension.

Vous pouvez choisir d’installer les extensions de liaison individuellement, ou ajouter une référence de bundle d’extensions au fichier du projet host.json. Les bundles d’extensions suppriment le risque d’avoir des problèmes de compatibilité de packages lors de l’utilisation de plusieurs types de liaisons. C’est la méthode recommandée pour inscrire des extensions de liaison. Les bundles d’extensions éliminent également l’obligation d’installer le kit SDK .NET Core 2.x. 

### <a name="extension-bundles"></a>Bundles d’extensions

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Pour en savoir plus, consultez [Inscrire des extensions de liaison Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Vous devez ajouter les bundles d’extensions à host.json avant d’ajouter des liaisons au fichier functions.json.

### <a name="register-individual-extensions"></a>Inscrire des extensions particulières

Si vous avez besoin d’installer des extensions qui ne font pas partie d’un bundle, vous pouvez inscrire manuellement des packages d’extensions particulières pour des liaisons spécifiques. 

> [!NOTE]
> Pour enregistrer manuellement des extensions avec `func extensions install`, le kit SDK .NET Core 2.x doit être installé.

Après avoir mis à jour le fichier *function.json* pour y inclure toutes les liaisons dont votre fonction a besoin, exécutez la commande suivante dans le dossier de projet.

```bash
func extensions install
```

La commande lit le fichier *function.json* pour voir les packages dont vous avez besoin, les installe et regénère le projet des extensions. Il ajoute les nouvelles liaisons à la version actuelle, mais ne met pas à jour les liaisons existantes. Utilisez l’option `--force` pour mettre à jour les liaisons existantes vers la dernière version pendant les nouvelles installations.