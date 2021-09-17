---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1c9875f6c3c6b69098c915d4257ed05e3ca0abc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785394"
---
### <a name="connect-to-the-service"></a>Se connecter au service

[!INCLUDE [az webpubsub client](cli-awps-client-connect.md)]

Testez l’instance et essayez de rejoindre des groupes à l’aide de `joingroup <group-name>` et envoyez des messages à des groupes à l’aide de `sendtogroup <group-name>` :

```azurecli
joingroup group1
```

```azurecli
sendtogroup group1 hello
```

[!INCLUDE [publish messages](cli-awps-service.md)]
