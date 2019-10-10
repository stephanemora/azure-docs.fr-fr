---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839165"
---
Ouvrez le fichier local.settings.json et copiez la valeur d’`AzureWebJobsStorage`, qui est la chaîne de connexion de compte de stockage. Définissez la variable d’environnement `AZURE_STORAGE_CONNECTION_STRING` sur la chaîne de connexion à l’aide de la commande Bash suivante :

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Lorsque vous définissez la chaîne de connexion dans la variable d’environnement `AZURE_STORAGE_CONNECTION_STRING`, vous pouvez accéder à votre compte de stockage sans devoir vous authentifier à chaque fois.