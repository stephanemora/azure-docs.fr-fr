---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399915"
---
:::row:::
    :::column span="3":::
        Le Kit de développement logiciel (SDK) Speech prend en charge **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8** et **CentOS 7/8** uniquement sur les architectures cibles suivantes utilisées avec Linux :
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Lors du ciblage de Linux ARM64 et de l’utilisation de C# -, le Core 3.x .NET (package dotnet-sdk-3.x) est requis. Si vous ciblez ARM32 ou ARM64, Python n’est pas pris en charge.

> [!NOTE]
> Les architectures x86 d’Ubuntu 16.04, Ubuntu 18.04 et Debian 9 prennent en charge uniquement le développement en C++ avec le Kit de développement logiciel (SDK) Speech.

### <a name="system-requirements"></a>Configuration système requise

Pour une application native, le Kit de développement logiciel (SDK) Speech s’appuie sur `libMicrosoft.CognitiveServices.Speech.core.so`. Assurez-vous que l’architecture cible (x86, x64) correspond à l’application. Selon la version de Linux, des dépendances supplémentaires peuvent être nécessaires.

- Les bibliothèques partagées de la bibliothèque C de GNU (notamment la bibliothèque de programmation POSIX Threads, `libpthreads`)
- La bibliothèque OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
- La bibliothèque partagée pour les applications ALSA (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 and CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Suivez les instructions décrivant [comment configurer RHEL/CentOS 7 pour le Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
