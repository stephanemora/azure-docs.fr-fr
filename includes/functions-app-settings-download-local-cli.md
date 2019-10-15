---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839092"
---
Vous avez déjà créé une application de fonction dans Azure ainsi que le compte de stockage nécessaire. La chaîne de connexion pour ce compte est stockée de manière sécurisée dans les paramètres d’application au sein d’Azure. Dans cet article, vous allez écrire des messages dans une file d’attente de stockage au sein du même compte. Pour vous connecter à votre compte de stockage lors de l’exécution de la fonction localement, vous devez télécharger les paramètres de l’application dans le fichier local.settings.json. Exécutez la commande Azure Functions Core Tools suivante pour télécharger les paramètres dans le fichier local.settings.json, en remplaçant `<APP_NAME>` par le nom de votre application de fonction décrite dans l’article précédent :

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Il se peut que vous deviez vous connecter à votre compte Azure.

> [!IMPORTANT]  
> Comme il contient des secrets, le fichier local.settings.json n’est jamais publié et doit être exclu du contrôle de code source.

Vous avez besoin de la valeur `AzureWebJobsStorage`, qui est la chaîne de connexion de compte de stockage. Cette connexion vous permet de vérifier que la liaison de sortie fonctionne comme prévu.
