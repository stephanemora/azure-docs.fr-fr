---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760684"
---
## <a name="retrying-after-errors-and-exceptions"></a>Nouvelle tentative après des erreurs et exceptions

Vous êtes censé implémenter vos propres stratégies de nouvelle tentative lors de l’appel de MSAL. MSAL effectue des appels HTTP en direction du service Azure AD et, des échecs peuvent parfois se produire. Par exemple, le réseau peut tomber en panne ou le serveur peut être surchargé.  

### <a name="http-429"></a>HTTP 429

Lorsque le serveur de jeton de service (STS) est surchargé avec un trop grand nombre de requêtes, il renvoie une erreur HTTP 429 en indiquant, dans le champ de réponse `Retry-After`, la durée qui s’écoule avant que vous puissiez renouveler votre demande.