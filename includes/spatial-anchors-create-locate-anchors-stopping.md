---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67176784"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, réinitialiser ou arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler `Stop()`. Ainsi, le traitement de tous les observateurs et environnements s’arrête, même si vous appelez ProcessFrame(). Vous pouvez ensuite appeler `Start()` pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
