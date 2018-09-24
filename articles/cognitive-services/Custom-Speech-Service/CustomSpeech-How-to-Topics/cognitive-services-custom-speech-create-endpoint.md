---
title: Créer un point de terminaison vocal personnalisé avec Custom Speech Service sur Azure | Microsoft Docs
description: Découvrez comment créer un point de terminaison de reconnaissance vocale personnalisé avec Custom Speech Service dans Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: ed93afa8e10fdfbb0d45f4500b4a648716e25e00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952220"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Créer un point de terminaison de reconnaissance vocale personnalisé
Une fois que vous avez créé des modèles acoustiques ou des modèles de langage personnalisés, vous pouvez déployer ces derniers dans un point de terminaison de reconnaissance vocale personnalisé. 

## <a name="create-an-endpoint"></a>Création d’un point de terminaison
Pour créer un nouveau point de terminaison personnalisé, sélectionnez **Déploiements** dans le menu **Custom Speech** en haut de la page. Cette action vous permet d’accéder à la page **Déploiements** qui contient un tableau répertoriant les points de terminaison personnalisés. Si vous n’avez pas encore créé de point de terminaison, le tableau est vide. Les paramètres régionaux actuels sont indiqués dans le titre du tableau. 

Pour créer un déploiement dans une autre langue, sélectionnez **Modifier les paramètres régionaux**. Pour plus d’informations sur les langues prises en charge, consultez [Paramètres régionaux pris en charge dans Custom Speech Service](cognitive-services-custom-speech-change-locale.md).

Pour créer un point de terminaison, cliquez sur **Créer un nouveau**. Dans le volet **Créer un déploiement**, entrez les informations dans les zones **Nom** et **Description** de votre déploiement personnalisé.

Dans la zone de liste modifiable **Abonnement**, sélectionnez l’abonnement que vous souhaitez utiliser. S’il s’agit d’un abonnement S2, vous pouvez sélectionner les unités d’échelle et la journalisation du contenu. Pour plus d’informations sur les unités d’échelle et la journalisation, consultez [Quotas et compteurs de Custom Speech Service](../cognitive-services-custom-speech-meters.md).

Le tableau suivant montre comment mapper des unités d’échelle vers des requêtes simultanées disponibles :

| Unité d'échelle | Nombre de requêtes simultanées |
| ------ | ----- |
| 0 | 1 |
| 1 | 5. |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

Vous pouvez également spécifier si la journalisation du contenu doit être activée ou désactivée. Autrement dit, vous choisissez si le trafic de point de terminaison est stocké pour une utilisation interne par Microsoft. Si l’option n’est pas sélectionnée, le stockage du trafic est supprimé. La suppression de la journalisation du contenu entraîne des coûts supplémentaires. Consultez la [page d’informations de tarification](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) pour plus d’informations.

> [!NOTE]
> La journalisation du contenu est appelée « Sans trace » sur la page de tarification.
>


De plus, Microsoft propose une estimation approximative des coûts afin que vous soyez conscient de l’impact des coûts des unités d’échelle et de la journalisation du contenu. Cette estimation est approximative et peut être différente des coûts réels.

> [!NOTE]
> Ces paramètres ne sont pas disponibles avec les abonnements F0 (de niveau gratuit).
>

Dans la liste **Modèle acoustique**, sélectionnez le modèle acoustique de votre choix, puis dans la liste **Modèle de langage**, sélectionnez le modèle de langage de votre choix. Les choix de modèles acoustique et de langage comprennent toujours les modèles Microsoft de base. Le choix d’un modèle de base limite les combinaisons. Vous ne pouvez pas mélanger des modèles de conversation de base avec des modèles de recherche et de dictée de base.

![Page Créer un déploiement](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> N’oubliez pas d’accepter les conditions d’utilisation et les informations sur la tarification en cochant la case.
>

Une fois que vous avez sélectionné vos modèles acoustique et de langage, cliquez sur **Créer**. Cette action vous renvoie à la page **Déploiements**. Le tableau inclut désormais une entrée qui correspond à votre nouveau point de terminaison. L’état du point de terminaison reflète son état actuel lors de sa création. L’instanciation d’un nouveau point de terminaison avec vos modèles personnalisés peut prendre jusqu’à 30 minutes. Lorsque l’état du déploiement est *Complete*, le point de terminaison est prêt à être utilisé.

![Page Déploiements](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

Quand le déploiement est prêt, le nom du déploiement devient un lien. En cliquant sur le lien, vous affichez la page **Informations de déploiement** qui indique les URL de votre point de terminaison personnalisé à utiliser avec une requête HTTP ou la Bibliothèque cliente Speech Microsoft Cognitive Services, qui utilise des sockets web.

![Page Informations de déploiement](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>Étapes suivantes

Pour les autres tutoriels, consultez :
* [Utiliser un point de terminaison de reconnaissance vocale personnalisé](cognitive-services-custom-speech-use-endpoint.md)
* [Créer un modèle acoustique personnalisé](cognitive-services-custom-speech-create-acoustic-model.md)
* [Créer un modèle de langage personnalisé](cognitive-services-custom-speech-create-language-model.md)
