---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006410"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, réinitialiser ou arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler `Stop()`. Ainsi, le traitement de tous les observateurs et environnements s’arrête, même si vous appelez `ProcessFrame()`. Vous pouvez ensuite appeler `Start()` pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
