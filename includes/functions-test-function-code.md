---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203147"
---
## <a name="test"></a>Vérifier la fonction dans Azure

Utilisez cURL pour vérifier la fonction déployée. À l’aide de l’URL, laquelle inclut la clé de fonction, que vous avez copiée à l’étape précédente, ajoutez la chaîne de requête `&name=<yourname>` à l’URL.

![Utiliser cURL pour appeler la fonction dans Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Vous pouvez également coller l’URL copiée, y compris la clé de fonction, dans la barre d’adresse de votre navigateur web. De nouveau, ajoutez la chaîne de requête `&name=<yourname>` à l'URL avant d'exécuter la requête.

![Utiliser un navigateur web pour appeler la fonction.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
