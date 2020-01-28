---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279494"
---
## <a name="test"></a>Vérifier la fonction dans Azure

Vous pouvez utiliser un navigateur web pour vérifier la fonction déployée.  Copiez l’URL, y compris la clé de fonction, dans la barre d’adresse de votre navigateur web. Ajoutez la chaîne de requête `&name=<yourname>` à l’URL avant d’exécuter la requête.

![Utiliser un navigateur web pour appeler la fonction.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

Vous pouvez aussi utiliser cURL pour vérifier la fonction déployée. À l’aide de l’URL, laquelle inclut la clé de fonction, que vous avez copiée à l’étape précédente, ajoutez la chaîne de requête `&name=<yourname>` à l’URL.

![Utiliser cURL pour appeler la fonction dans Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

