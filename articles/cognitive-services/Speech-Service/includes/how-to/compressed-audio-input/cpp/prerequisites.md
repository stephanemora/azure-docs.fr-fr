---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: d555322c0fd4067a4232e53387d811de7b50a8f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417721"
---
La gestion d'un contenu audio compressé est implémentée à l’aide de [GStreamer](https://gstreamer.freedesktop.org). Pour une raison liée à la gestion des licences, les fichiers binaires GStreamer ne sont pas compilés et liés avec le Kit de développement logiciel (SDK) Speech. Les développeurs doivent installer plusieurs dépendances et plug-ins.

> [!NOTE]
> Consultez la liste des [architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md).

# <a name="ubuntudebian"></a>[Ubuntu/Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - Sur RHEL/CentOS 7 et RHEL/CentOS 8, en cas d’utilisation du format compressé « ANY », d’autres plug-ins GStreamer doivent être installés si le plug-in de format de média de flux ne figure pas dans les plug-ins installés ci-dessus. 
> - Sur RHEL/CentOS 7, suivez les instructions décrivant [comment configurer RHEL/CentOS 7 pour le Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).


---