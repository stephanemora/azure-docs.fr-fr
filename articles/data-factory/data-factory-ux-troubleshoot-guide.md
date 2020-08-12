---
title: Résoudre des problèmes d’expérience utilisateur dans Azure Data Factory
description: Découvrez comment résoudre des problèmes d’expérience utilisateur dans Azure Data Factory.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567804"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Résoudre des problèmes d’expérience utilisateur dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes de résolution des problèmes couramment utilisée pour l’expérience utilisateur dans Azure Data Factory.

## <a name="adf-ux-not-loading"></a>Non-chargement de l’expérience utilisateur ADF

> [!NOTE]
> L’expérience utilisateur Azure Data Factory prend officiellement en charge Microsoft Edge et Google Chrome. L’utilisation d’autres navigateurs web peut entraîner un comportement inattendu ou non documenté.

### <a name="third-party-cookies-blocked"></a>Cookies tiers bloqués

L’expérience utilisateur ADF utilise des cookies de navigateurs pour conserver une session utilisateur et permettre des expériences de développement et de supervision interactives. Il est possible que votre navigateur bloque les cookies tiers, parce que vous utilisez une session incognito ou avez activé un bloqueur de publicités. Le blocage des cookies tiers peut provoquer des problèmes lors du chargement du portail, comme la redirection vers une page vierge, https://adf.azure.com/accesstoken.html, ou l’obtention d’un message d’avertissement indiquant que les cookies tiers sont bloqués. Pour résoudre ce problème, activez les options de cookies tiers sur votre navigateur en procédant comme suit :

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Autoriser tous les cookies

1. Visitez **chrome://settings/cookies** dans votre navigateur.
1. Sélectionnez l’option **Autoriser tous les cookies** ![Chrome-Allow-All-Cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Autoriser uniquement l’expérience utilisateur ADF à utiliser des cookies
Si vous ne souhaitez pas autoriser tous les cookies, vous pouvez n’autoriser que l’expérience utilisateur ADF :
1. Visitez **chrome://settings/cookies**.
1. Sous l’option **Sites qui peuvent toujours utiliser des cookies**, cliquez sur **Ajouter** ![Ajouter l’expérience utilisateur ADF aux sites autorisés](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png).
1. Ajoutez le site **adf.azure.com**, cochez l’option **tous les cookies**, puis enregistrez. ![Autoriser tous les cookies du site d’expérience utilisateur ADF](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Visitez **edge://settings/content/cookies** dans votre navigateur.
1. Assurez-vous que l’option **Autoriser les sites à enregistrer et à lire les données de cookie** est activée, et que l’option **Bloquer les cookies tiers** est désactivée ![Autoriser tous les cookies dans Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png).
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Autoriser uniquement l’expérience utilisateur ADF à utiliser des cookies

Si vous ne souhaitez pas autoriser tous les cookies, vous pouvez n’autoriser que l’expérience utilisateur ADF :

1. Visitez **edge://settings/content/cookies**.
1. Sous la section **Autoriser**, cliquez sur **Ajouter**, puis ajoutez le site **adf.azure.com**. ![Ajouter l’expérience utilisateur ADF aux sites autorisés](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Actualisez l’expérience utilisateur ADF, puis réessayez.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

* [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/)
* [Page de questions Microsoft Q&R](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
