---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: gelecaro
ms.openlocfilehash: a1bc980f8334ca815a1805f33f3572cded4ba0ef
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "86156685"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous devez disposer des éléments suivants :

* gcc
* [Binaire Go (1.13 ou version ultérieure)](https://golang.org/dl/)

* Les plateformes Linux prises en charge nécessitent l’installation de certaines bibliothèques (`libssl` pour la prise en charge du protocole SSL et `libasound2` pour la prise en charge du son). Reportez-vous à votre distribution ci-dessous pour connaître les commandes nécessaires à l’installation des versions appropriées de ces bibliothèques.

   * Sur Ubuntu :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Sur Debian 9 :

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * Sur RHEL/CentOS 8 :

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

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