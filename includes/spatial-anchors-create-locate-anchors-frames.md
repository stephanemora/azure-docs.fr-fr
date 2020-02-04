---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694380"
---
## <a name="provide-frames-to-the-session"></a>Fournir des images à la session

La session d’ancrage spatial fonctionne en mappant l’espace autour de l’utilisateur. Cela aide à déterminer l’emplacement des points d’ancrage. Les plateformes mobiles (iOS et Android) nécessitent un appel natif à l’appareil photo pour obtenir des images à partir de la bibliothèque AR de votre plateforme. En revanche, HoloLens analyse constamment l’environnement. Il n’est donc pas nécessaire d’effectuer un appel, comme avec Mobile.