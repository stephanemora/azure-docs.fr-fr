---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176784"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, réinitialiser ou arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler `Stop()`. Ainsi, le traitement de tous les observateurs et environnements s’arrête, même si vous appelez ProcessFrame(). Vous pouvez ensuite appeler `Start()` pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
