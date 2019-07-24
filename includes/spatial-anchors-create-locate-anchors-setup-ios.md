---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176764"
---
## <a name="setting-up-the-library"></a>Configuration de la bibliothèque

Appelez Start() pour activer votre session afin de traiter les données de l’environnement.

Pour gérer les événements déclenchés par votre session, définissez la propriété `delegate` de votre session sur un objet, comme votre affichage. Cet objet doit implémenter le protocole SSCCloudSpatialAnchorSessionDelegate.
