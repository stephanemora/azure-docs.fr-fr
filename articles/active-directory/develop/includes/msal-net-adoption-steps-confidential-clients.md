---
ms.openlocfilehash: fa43176a945a01651dc768558d14604d0a15b300
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726826"
---
Les étapes suivantes pour la mise à jour du code s’appliquent à tous les scénarios de client confidentiel :

1. Ajoutez l’espace de noms MSAL.NET dans votre code source : `using Microsoft.Identity.Client;`.
2. Au lieu d’instancier `AuthenticationContext`, utilisez `ConfidentialClientApplicationBuilder.Create` pour instancier `IConfidentialClientApplication`.
3. Au lieu de la chaîne `resourceId`, MSAL.NET utilise les étendues. Comme les applications qui utilisent ADAL.NET sont pré-autorisées, vous pouvez toujours utiliser les étendues suivantes : `new string[] { $"{resourceId}/.default" }`.
4. Remplacez l’appel à `AuthenticationContext.AcquireTokenAsync` par un appel à `IConfidentialClientApplication.AcquireTokenXXX`, où *XXX* dépend de votre scénario.
