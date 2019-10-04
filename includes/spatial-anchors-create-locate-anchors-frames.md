---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176766"
---
## <a name="provide-frames-to-the-session"></a>Fournir des images à la session

La session d’ancrage spatial fonctionne en mappant l’espace autour de l’utilisateur. Cela aide à déterminer l’emplacement des points d’ancrage. Les plateformes mobiles (iOS et Android) nécessitent un appel natif à l’appareil photo pour obtenir des images à partir de la bibliothèque AR de votre plateforme. En revanche, HoloLens analyse constamment l’environnement. Il n’est donc pas nécessaire pour un appel spécifique, comme avec Mobile.