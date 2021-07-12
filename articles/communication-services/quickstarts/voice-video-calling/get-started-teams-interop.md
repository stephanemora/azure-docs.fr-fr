---
title: Démarrage rapide - Interopérabilité de Teams sur Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez découvrir comment rejoindre une réunion Teams avec le SDK Azure Communication Calling.
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 1fb7373842e7edfdede482d83fdc7e27da0e6965
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113107760"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Démarrage rapide : Joindre votre application d’appel à une réunion Teams

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Pour activer/désactiver l’[interopérabilité des locataires Teams](../../concepts/teams-interop.md), remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Commencez à utiliser Azure Communication Services en connectant votre solution d’appel à Microsoft Teams à l’aide du SDK JavaScript.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Calling with Windows](./includes/teams-interop/teams-interop-windows.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop/teams-interop-ios.md)]
::: zone-end

Les fonctionnalités décrites dans ce document utilisent la version de disponibilité générale des SDK Communication Services. L’interopérabilité Teams requiert la version bêta des SDK Communication Services. Les SDK bêta peuvent être explorés dans la [page des notes de publication](https://github.com/Azure/Communication/tree/master/releasenotes).

Lors de l’exécution de l’étape « Installer le package » avec les SDK bêta, remplacez la version de votre package par la dernière version bêta en spécifiant la version `@1.0.0-beta.10` (version au moment de la rédaction de cet article) dans le nom du package `communication-calling`. Vous n’avez pas besoin de modifier la commande de package `communication-common`. Par exemple :

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- Consultez notre [exemple de bannière d’appel](../../samples/calling-hero-sample.md).
- Apprenez-en davantage sur les [fonctionnalités du SDK Calling](./calling-client-samples.md)
- Apprenez-en davantage sur le [fonctionnement des appels](../../concepts/voice-video-calling/about-call-types.md)
