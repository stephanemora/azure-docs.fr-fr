---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110729"
---
## <a name="provide-frames-to-the-session"></a>Fournir des images à la session

La session d’ancrage spatial fonctionne en mappant l’espace autour de l’utilisateur. Ainsi, pour déterminer l’emplacement des points d’ancrage. Plateformes mobiles (iOS et Android) nécessitent un appel natif à l’appareil photo pour obtenir des images à partir de la bibliothèque de AR de votre plateforme de flux. En revanche, HoloLens analyse constamment l’environnement, il est donc pas nécessaire pour un appel spécifique, comme avec Mobile.