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
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987962"
---
## <a name="create-a-function"></a>Créer une fonction

La commande suivante crée une fonction déclenchée via HTTP nommée `MyHtpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Lorsque la commande s’exécute, une sortie similaire à la suivante s’affiche :

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```