---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7c739c65e638657cffbfade0c2a491cdefd6b626
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700484"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### <a name="record-calls"></a>Enregistrer les appels
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

L’enregistrement des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité d’enregistrement :

```js
const callRecordingApi = call.api(Features.Recording);
```

Ensuite, pour vérifier si l’appel est en train d’être enregistré, inspectez la propriété `isRecordingActive` de `callRecordingApi`. Elle retourne `Boolean`.

```js
const isRecordingActive = callRecordingApi.isRecordingActive;
```

Vous pouvez également vous abonner à l’enregistrement des modifications :

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
```