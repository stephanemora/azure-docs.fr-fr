---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907707"
---
## <a name="pause-reset-or-stop-the-session"></a>Suspendre, de réinitialiser ou d’arrêter la session

Pour arrêter la session temporairement, vous pouvez appeler Stop(). Cela arrête tout observateurs et traitement de l’environnement, même si vous appelez ProcessFrame(). Vous pouvez ensuite appeler Start() pour reprendre le traitement. Lors de la reprise, les données d’environnement déjà capturées dans la session sont conservées.
