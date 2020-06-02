---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006475"
---
## <a name="setting-up-the-library"></a>Configuration de la bibliothèque

Appelez `Start()` pour activer votre session pour traiter les données de l’environnement.

Pour gérer les événements déclenchés par votre session, définissez la propriété `delegate` de votre session sur un objet, comme votre affichage. Cet objet doit implémenter le protocole `SSCCloudSpatialAnchorSessionDelegate`.
