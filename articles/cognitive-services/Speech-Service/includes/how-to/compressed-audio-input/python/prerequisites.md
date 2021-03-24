---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821517"
---
La gestion d'un contenu audio compressé est implémentée à l’aide de [GStreamer](https://gstreamer.freedesktop.org). Pour une raison liée à la gestion des licences, les fichiers binaires GStreamer ne sont pas compilés et liés avec le Kit de développement logiciel (SDK) Speech. Les développeurs doivent installer plusieurs dépendances et plug-ins. Voir [Installation sur Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) ou [Installation sur Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Les fichiers binaires GStreamer doivent se trouver sous le chemin d’accès système afin que le Kit de développement logiciel (SDK) Speech puisse les charger lors de l’exécution du runtime. Par exemple, sur Windows, si le Kit de développement logiciel (SDK) Speech est en mesure de trouver `libgstreamer-1.0-0.dll` lors de l’exécution, cela signifie que les fichiers binaires GStreamer se trouvent sous le chemin d’accès système.

