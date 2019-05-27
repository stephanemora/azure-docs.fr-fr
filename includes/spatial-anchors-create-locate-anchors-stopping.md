---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110469"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, de réinitialiser ou d’arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler `Stop()`. Cela arrête tout observateurs et traitement de l’environnement, même si vous appelez ProcessFrame(). Vous pouvez ensuite appeler `Start()` pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
