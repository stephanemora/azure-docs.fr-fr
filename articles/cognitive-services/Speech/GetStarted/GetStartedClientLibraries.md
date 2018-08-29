---
title: Bien démarrer avec l’API Reconnaissance vocale de Microsoft à l’aide des bibliothèques clientes de la Reconnaissance vocale Bing | Microsoft Docs
description: Les bibliothèques clientes du service Microsoft Speech dans Microsoft Cognitive Services permettent de développer des applications qui convertissent du contenu vocal en texte.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: f4b6a97260c6dc176600af8844001e4de819ff7c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42357508"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Bien démarrer avec les bibliothèques clientes du service Reconnaissance vocale Bing

En plus d’effectuer des requêtes HTTP directes via une API REST, le service Reconnaissance vocale Bing fournit aux développeurs des bibliothèques clientes Speech dans différentes langues. Les bibliothèques clientes Speech :

- Prennent en charge des fonctionnalités de reconnaissance vocale plus avancées, telles que les résultats intermédiaires en temps réel, le flux audio de longue durée (jusqu’à 10 minutes) et la reconnaissance continue.
- Fournissent une API simple et idiomatique dans la langue de votre choix.
- Masquent les détails de communication de bas niveau.

Les bibliothèques clientes Reconnaissance vocale Bing suivantes sont disponibles :

- [Bibliothèque de bureau C#](GetStartedCSharpDesktop.md)
- [Bibliothèque de services C#](GetStartedCSharpServiceLibrary.md)
- [Bibliothèque JavaScript](GetStartedJSWebsockets.md)
- [Bibliothèque Java pour Android](GetStartedJavaAndroid.md)
- [Bibliothèque Objective-C pour iOS](Get-Started-ObjectiveC-iOS.md)

> [!NOTE] 
En mai 2018, nous avons également publié le nouveau [service Speech](/speech-service/overview.md) en préversion publique. Nous vous encourageons à l’[essayer gratuitement](/speech-service/get-started.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

- La page [d’exemples](../samples.md) fournit des exemples complets d’utilisation des bibliothèques clientes Speech.
- Si vous avez besoin d’une bibliothèque cliente qui n’est pas encore prise en charge, vous pouvez créer votre propre SDK. Implémentez le [protocole WebSocket Speech](../API-Reference-REST/websocketprotocol.md) sur la plateforme et utilisez la langue de votre choix.

## <a name="license"></a>Licence

Tous les kits SDK Cognitive Services et les exemples sont fournis sous licence MIT. Pour plus d’informations, consultez [Licence](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
