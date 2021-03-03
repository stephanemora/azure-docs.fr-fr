---
title: API REST de gestion de session
description: Décrit comment gérer les sessions
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530210"
---
# <a name="use-the-session-management-rest-api"></a>Utiliser l’API REST de gestion de session

Pour utiliser la fonctionnalité Azure Remote Rendering, vous devez créer une *session*. Chaque session correspond à un serveur alloué dans Azure, auquel un appareil client peut se connecter. Quand un appareil se connecte, le serveur affiche les données demandées et présente le résultat sous la forme d’un flux vidéo. Durant la création de la session, vous avez choisi le [genre de serveur](../reference/vm-sizes.md) à utiliser, ce qui détermine le tarif. Une fois que la session n’est plus nécessaire, elle doit être arrêtée. Si elle n’est pas arrêtée manuellement, il l’est automatiquement arrêté lorsque la *durée de son bail* expire.

## <a name="rest-api-reference"></a>Référence d’API REST

Les informations de référence sur l’API REST sont disponibles [ici](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering), et les définitions de Swagger sont disponibles [ici](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality).
Dans le [référentiel d’exemples ARR](https://github.com/Azure/azure-remote-rendering) dans le dossier *Scripts*, nous fournissons un script PowerShell nommé *RenderingSession. ps1* qui illustre l’utilisation de notre service. Le script et sa configuration sont décrits ici : [Exemples de scripts PowerShell](../samples/powershell-example-scripts.md).
Nous fournissons également des kits SDK pour [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java et Python.

> [!IMPORTANT]
> La latence est un facteur important pour l’utilisation de Remote Rendering. Pour la meilleure expérience possible, créez des sessions dans la région la plus proche de vous. Le [test de latence Azure](https://www.azurespeed.com/Azure/Latency) permet de déterminer la région la plus proche.

> [!IMPORTANT]
> Un kit SDK ARR Runtime est nécessaire pour permettre à un appareil client de se connecter à une session de rendu. Ces kits SDK sont disponibles en [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) et en [C++](https://docs.microsoft.com/cpp/api/remote-rendering/). En plus de la connexion au service, ces kits SDK peuvent également être utilisés pour démarrer et arrêter les sessions.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser les API Azure Front-end pour l’authentification](frontend-apis.md)
* [Exemples de scripts PowerShell](../samples/powershell-example-scripts.md)
