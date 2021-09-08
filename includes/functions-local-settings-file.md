---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/15/2021
ms.author: glenga
ms.openlocfilehash: 33a4b0f7d25162cf258e9ef6ad4ee438d6b76c8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524833"
---
## <a name="local-settings"></a>Paramètres locaux

Lors de l’exécution dans une application de fonction dans Azure, les paramètres requis par vos fonctions sont [stockés de manière sécurisée dans les paramètres de l’application](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#settings). Lors du développement local, ces paramètres sont, à la place, ajoutés à l'`Values`objet dans le fichier local.settings.json. Le fichier local.settings.json stocke également les paramètres utilisés par les outils de développement locaux. 

Étant donné que le fichier local.settings.json peut contenir des secrets, tels que des chaînes de connexion, vous ne devez jamais le stocker dans un référentiel distant. Pour en savoir plus sur les paramètres locaux, consultez le [Fichier de paramètres locaux](../articles/azure-functions/functions-develop-local.md#local-settings-file).