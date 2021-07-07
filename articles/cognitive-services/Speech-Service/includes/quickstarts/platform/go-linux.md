---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: c2222918106de8594881f105cc86ab1b45135027
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164281"
---
Ce guide explique comment installer le [Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/speech-sdk.md) pour Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Configuration système requise

Linux : consultez la liste des [architectures cibles et distributions Linux prises en charge](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous devez disposer des éléments suivants :

* [Binaire Go (1.13 ou version ultérieure)](https://golang.org/dl/)
* Configuration de l’environnement Linux en fonction de la [configuration système demandée et des instructions d’installation](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk).


[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Configurer l’environnement Go

Effectuez les étapes suivantes afin de configurer votre environnement Go pour qu’il trouve le SDK Speech. Dans ces deux étapes, remplacez `<architecture>` par l’architecture de votre processeur. L’architecture sera `x86`, `x64`, `arm32` ou `arm64`.

1. Étant donné que les liaisons s’appuient sur `cgo`, vous devez définir les variables d’environnement pour que Go puisse trouver le kit de développement logiciel (SDK) :

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Pour exécuter des applications, y compris le SDK, nous devons indiquer au système d’exploitation où trouver les bibliothèques :

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [windows](../quickstart-list-go.md)]
