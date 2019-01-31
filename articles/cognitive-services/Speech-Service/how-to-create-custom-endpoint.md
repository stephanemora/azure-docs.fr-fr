---
title: Créer un point de terminaison vocal personnalisé avec le service Speech sur Azure | Microsoft Docs
description: Découvrez comment créer un point de terminaison de reconnaissance vocale personnalisé avec le service Speech de Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 63d2c15c14340b0d7407b0144a8b8c75a2634e6a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220240"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>Créer un point de terminaison de reconnaissance vocale personnalisé

Une fois que vous avez créé des modèles acoustiques ou des modèles de langage personnalisés, vous pouvez déployer ces derniers dans un point de terminaison de reconnaissance vocale personnalisé. 

## <a name="create-an-endpoint"></a>Création d’un point de terminaison
Pour créer un point de terminaison personnalisé, sélectionnez **Points de terminaison** dans le menu **Custom Speech** en haut de la page. Cette action vous fait accéder à la page **Points de terminaison** qui contient un tableau répertoriant les points de terminaison personnalisés actuels. Si vous n’avez pas encore créé de point de terminaison, le tableau est vide. Les paramètres régionaux actuels sont indiqués dans le titre du tableau. 

Pour créer un déploiement dans une autre langue, sélectionnez **Modifier les paramètres régionaux**. Pour plus d’informations sur les langues prises en charge.

Pour créer un point de terminaison, cliquez sur **Créer un nouveau**. Dans le volet **Créer un point de terminaison**, renseignez les zones **Nom** et **Description** de votre déploiement personnalisé.

Dans la zone de liste modifiable **Abonnement**, sélectionnez l’abonnement que vous souhaitez utiliser. S’il s’agit d’un abonnement S0 (payant), vous pouvez sélectionner des demandes simultanées.

Vous pouvez également spécifier si la journalisation du contenu doit être activée ou désactivée. Autrement dit, vous choisissez si le trafic de point de terminaison est stocké. Si l’option n’est pas sélectionnée, le stockage du trafic est supprimé. Pour tout le contenu consigné, vous trouverez des liens de téléchargement sous Point de terminaison-> Détails

Dans la liste **Modèle acoustique**, sélectionnez le modèle acoustique de votre choix, puis dans la liste **Modèle de langage**, sélectionnez le modèle de langage de votre choix. Les choix de modèles acoustique et de langage comprennent toujours les modèles Microsoft de base. Le choix d’un modèle de base limite les combinaisons. Vous ne pouvez pas mélanger des modèles de conversation de base avec des modèles de recherche et de dictée de base.

> [!NOTE]
> N’oubliez pas d’accepter les conditions d’utilisation et les informations sur la tarification en cochant la case.
>

Une fois que vous avez sélectionné vos modèles acoustique et de langage, cliquez sur **Créer**. Cette action vous renvoie à la page **Déploiements**. Le tableau inclut désormais une entrée qui correspond à votre nouveau point de terminaison. L’état du point de terminaison reflète son état actuel lors de sa création. L’instanciation d’un nouveau point de terminaison avec vos modèles personnalisés peut prendre jusqu’à 30 minutes. Lorsque l’état du déploiement est *Complete*, le point de terminaison est prêt à être utilisé.

Dès que le déploiement est prêt, le nom du point de terminaison devient un lien. En cliquant sur le lien, vous affichez la page **Informations sur le point de terminaison** qui indique les URL de votre point de terminaison personnalisé à utiliser avec soit une requête HTTP soit la Bibliothèque cliente Speech Microsoft Cognitive Services, laquelle utilise des sockets web.

## <a name="next-steps"></a>Étapes suivantes

Pour les autres tutoriels, consultez :
- [Obtenir votre abonnement d’essai gratuit à Speech Service](https://azure.microsoft.com/try/cognitive-services/)
- [Créer des modèles acoustiques personnalisés](how-to-customize-acoustic-models.md)
- [Créer un modèle linguistique personnalisé](how-to-customize-language-model.md)
