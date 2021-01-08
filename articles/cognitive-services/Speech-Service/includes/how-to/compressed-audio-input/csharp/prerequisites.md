---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821551"
---
La gestion d'un contenu audio compressé est implémentée à l’aide de [GStreamer](https://gstreamer.freedesktop.org). Pour une raison liée à la gestion des licences, les fichiers binaires GStreamer ne sont pas compilés et liés avec le Kit de développement logiciel (SDK) Speech. Les développeurs doivent installer plusieurs dépendances et plug-ins, consultez [Installation sur Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) ou [Installation sur Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Les fichiers binaires GStreamer doivent se trouver dans le chemin d’accès système, afin que le kit de développement logiciel (SDK) puisse charger les binaires au cours de l’exécution. Par exemple, sur Windows, si le kit de développement logiciel (SDK) Speech est en mesure de trouver `libgstreamer-1.0-0.dll` lors de l’exécution, cela signifie que les fichiers binaires GStreamer se trouvent dans le chemin système.

