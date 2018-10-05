---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044507"
---
Quand vous développez des fonctions localement, vous pouvez installer les extensions dont vous avez besoin en utilisant Azure Functions Core Tools à partir du terminal ou d’une invite de commandes.

Après avoir mis à jour le fichier *function.json* pour y inclure toutes les liaisons dont votre fonction a besoin, exécutez la commande suivante dans le dossier de projet.

```bash
func extensions install
```

La commande lit le fichier *function.json* pour voir les packages dont vous avez besoin, les installe et regénère le projet des extensions. Il ajoute les nouvelles liaisons à la version actuelle, mais ne met pas à jour les liaisons existantes. Utilisez l’option `--force` pour mettre à jour les liaisons existantes vers la dernière version pendant les nouvelles installations.

Si vous souhaitez installer une version particulière d’un package ou que vous souhaitez installer les packages avant de modifier le fichier *function.json*, utilisez la commande `func extensions install` avec le nom du package, comme indiqué dans l’exemple suivant :

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Remplacez `<target_version>` par une version spécifique du package, telle que `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org).
