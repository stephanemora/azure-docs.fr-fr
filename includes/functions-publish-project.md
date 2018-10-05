---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452953"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Déployer le projet d’application de fonction sur Azure

Une fois l’application de fonction créée dans Azure, vous pouvez utiliser la commande [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) pour déployer votre code de projet sur Azure.

```bash
func azure functionapp publish <FunctionAppName>
```

Une sortie similaire à la suivante, tronquée pour une meilleure lisibilité, s’affiche.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Vous pouvez désormais tester vos fonctions dans Azure.