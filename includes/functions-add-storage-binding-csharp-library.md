---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 4f2aa82388882c192213b168faedd4f61069ae64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829535"
---
Dans un projet C#, les liaisons sont définies comme des attributs de liaison sur la méthode de fonction. Les définitions spécifiques varient selon que votre application s’exécute in-process (bibliothèque de classes C#) ou dans un processus isolé.  

# <a name="in-process"></a>[In-process](#tab/in-process)

Ouvrez le fichier projet *HttpExample.cs*, puis ajoutez le paramètre suivant à la définition de la méthode `Run` :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Le paramètre `msg` est un type `ICollector<T>`, qui représente une collection de messages écrits dans une liaison de sortie quand la fonction se termine. Dans ce cas, la sortie est une file d’attente de stockage nommée `outqueue`. La chaîne de connexion pour le compte de stockage est définie par `StorageAccountAttribute`. Cet attribut indique le paramètre qui contient la chaîne de connexion du compte de stockage et qui peut être appliqué au niveau de la classe, de la méthode ou du paramètre. Dans ce cas, vous pourriez omettre `StorageAccountAttribute`, car vous utilisez déjà le compte de stockage par défaut.

La définition de la méthode Run doit maintenant se présenter comme ceci :  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# <a name="isolated-process"></a>[Processus isolé](#tab/isolated-process)

Ouvrez le fichier projet *HttpExample.cs* et ajoutez la classe `MultiResponse` suivante :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

La classe `MultiResponse` vous permet d’écrire dans une file d’attente de stockage nommée `outqueue` et dans un message de réussite HTTP. Comme l’attribut `QueueOutput` est appliqué à un tableau de chaînes, plusieurs messages peuvent être envoyés à la file d’attente. 

La chaîne de connexion du compte de stockage est définie par la propriété `Connection`. Dans ce cas, vous pourriez omettre `Connection`, car vous utilisez déjà le compte de stockage par défaut.

---