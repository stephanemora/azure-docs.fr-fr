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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176842"
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
