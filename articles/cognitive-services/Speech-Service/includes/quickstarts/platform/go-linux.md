---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 06e4eea32aefcb400c144be98c274e3e4bb4b121
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188263"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

Linux : consultez la liste des [architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous devez disposer des éléments suivants :

* gcc
* [Binaire Go (1.13 ou version ultérieure)](https://golang.org/dl/)

* Les plateformes Linux prises en charge nécessitent l’installation de certaines bibliothèques (`libssl` pour la prise en charge du protocole SSL et `libasound2` pour la prise en charge du son). Reportez-vous à votre distribution ci-dessous pour connaître les commandes nécessaires à l’installation des versions appropriées de ces bibliothèques.

   * Sur Ubuntu/Debian :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Si libssl1.0.0 n’est pas disponible, installez libssl1.0.x (où x est supérieur à 0) ou libssl1.1 à la place.

   * Sur RHEL/CentOS :

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - Sur RHEL/CentOS 7, suivez les instructions décrivant [comment configurer RHEL/CentOS 7 pour le Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Configurer l’environnement Go

1. Étant donné que les liaisons s’appuient sur `cgo`, vous devez définir les variables d’environnement pour que Go puisse trouver le kit de développement logiciel (SDK) :

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. En outre, pour exécuter des applications, y compris le kit de développement logiciel (SDK), nous devons indiquer au système d’exploitation où trouver les bibliothèques :

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list-go.md)]