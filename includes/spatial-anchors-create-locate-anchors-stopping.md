---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632442"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, de réinitialiser ou d’arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler `Stop()`. Cela arrête tout observateurs et traitement de l’environnement, même si vous appelez ProcessFrame(). Vous pouvez ensuite appeler `Start()` pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
