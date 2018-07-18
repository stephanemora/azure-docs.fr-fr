---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739498"
---
Lorsque l’hôte Functions s’exécute localement, il écrit des journaux dans le chemin d’accès suivant :

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Sous Windows, `<DefaultTempDirectory>` est la première valeur trouvée pour les variables d’environnement TMP, TEMP, USERPROFILE ou le répertoire Windows.
Sur Mac OS ou Linux, `<DefaultTempDirectory>` est la variable d’environnement TMPDIR.

> [!NOTE]
> Lorsque l’hôte Functions démarre, il remplace la structure de fichiers existante dans le répertoire.