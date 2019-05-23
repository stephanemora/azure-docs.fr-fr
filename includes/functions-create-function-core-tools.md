---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132158"
---
## <a name="create-a-function"></a>Créer une fonction

La commande suivante crée une fonction déclenchée via HTTP nommée `MyHttpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Lorsque la commande s’exécute, une sortie similaire à la suivante s’affiche :

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
