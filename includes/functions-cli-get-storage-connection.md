---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: ceb52a4e3e2b66388b73bcb63b68913d8dcb467a
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830510"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Récupérer la chaîne de connexion de Stockage Azure

Vous avez créé auparavant un compte de stockage Azure que l’application de fonction va utiliser. La chaîne de connexion pour ce compte est stockée de manière sécurisée dans les paramètres d’application au sein d’Azure. En téléchargeant le paramètre dans le fichier *local.settings.json*, vous pouvez utiliser cette connexion pour écrire dans une file d’attente de stockage du même compte au moment de l’exécution locale de la fonction. 

1. À partir de la racine du projet, exécutez la commande suivante, en remplaçant `<APP_NAME>` par le nom de votre application de fonction créée dans le guide de démarrage rapide précédent. Cette commande remplace toutes les valeurs existantes dans le fichier.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Ouvrez *local.settings.json* et recherchez la valeur nommée `AzureWebJobsStorage`, qui est la chaîne de connexion du compte de stockage. Vous utilisez le nom `AzureWebJobsStorage` et la chaîne de connexion dans d’autres sections de cet article.

> [!IMPORTANT]
> Dans la mesure où *local.settings.json* contient des secrets téléchargés à partir d’Azure, excluez toujours ce fichier du contrôle de code source. Le fichier *.gitignore* créé avec un projet Functions local exclut le fichier par défaut.