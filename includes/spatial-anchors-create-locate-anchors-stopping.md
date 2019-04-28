---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232373"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, de réinitialiser ou d’arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler `Stop()`. Cela arrête tout observateurs et traitement de l’environnement, même si vous appelez ProcessFrame(). Vous pouvez ensuite appeler `Start()` pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
