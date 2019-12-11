---
title: Essayez le service Speech gratuitement
titleSuffix: Azure Cognitive Services
description: La prise en main du service Speech est simple et économique. Deux options sont disponibles gratuitement pour que vous puissiez découvrir ce que le service peut faire et de déterminer s’il convient à vos besoins.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 3ddba414ef1801b812d157ad734847099a8a9f25
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806182"
---
# <a name="try-the-speech-service-for-free"></a>Essayez le service Speech gratuitement

L’utilisation du service Speech est simple et économique. Deux options sont disponibles gratuitement pour que vous puissiez découvrir ce que le service peut faire et de déterminer s’il convient à vos besoins :

- Obtenir une évaluation gratuite sans fournir d’informations sur votre carte de crédit (vous devez disposer d’un compte Azure existant)
- Créer gratuitement un compte Azure pour une période d’essai (informations de carte de crédit requises)

Dans cet article, vous allez choisir celle de ces options qui répond le mieux à vos besoins.

> [!NOTE]
> Le service Speech propose deux niveaux de service, gratuit et abonnement, chacun présentant des limitations et des avantages différents. Lorsque vous vous inscrivez pour obtenir un compte Azure gratuit, celui-ci est fourni avec 200 $ de crédit de service que vous pouvez utiliser pour un abonnement au service Speech payant, valide pendant 30 jours.
>
> Si vous utilisez le niveau de service Speech gratuit à faible volume, vous pouvez conserver cet abonnement gratuit, même après l’expiration de votre évaluation gratuite ou de votre crédit de service.
>
> Pour plus d’informations, consultez [Tarifs de Cognitive Services - Service Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="try-the-speech-service-without-credit-card-info"></a>Essayer le service Speech sans informations de carte de crédit

Bien que nous vous recommandons de tester le service Speech à l’aide des instructions de la section suivante, vous êtes susceptible de préférer les instructions de cette section si les conditions suivantes s’appliquent :

- Vous disposez déjà d’un compte Azure actif.
- Vous souhaitez évaluer le service Speech sans créer de compte Azure.
- Vous préférez qu’aucune carte de crédit ne soit requise et qu’aucune donnée ne soit enregistrée après la période d’évaluation.

> [!NOTE]
> La période d’évaluation commence immédiatement une fois les étapes suivantes terminées.

1. Accédez à [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).
1. Sélectionnez l’onglet **API Reconnaissance vocale**.
1. Choisissez **Obtenir la clé API**.

Des options de facturation s’affichent. Choisissez l’option gratuite, puis lisez et acceptez le contrat d’utilisation. Des clés vous seront proposées pour vous permettre d’essayer le service Speech pendant une période limitée.

## <a name="try-the-speech-service-using-a-new-azure-account"></a>Essayer le service Speech à l’aide d’un nouveau compte Azure

Pour vous inscrire à un nouveau compte Azure, vous avez besoin d’un compte Microsoft. Si vous n’avez pas de compte Microsoft, vous pouvez vous inscrire gratuitement sur le [portail de compte Microsoft](https://account.microsoft.com/account). Sélectionnez **Se connecter avec Microsoft** puis, à l’invite de connexion, sélectionnez **Créer un compte Microsoft**. Suivez les étapes pour créer et vérifier votre nouveau compte Microsoft.

Lorsque vous disposez d’un compte Microsoft, accédez à la [page d’inscription Azure](https://azure.microsoft.com/free/ai/), sélectionnez **Démarrer gratuitement** et créez un compte Azure à l’aide de votre compte Microsoft.

### <a name="create-a-speech-resource-in-azure"></a>Créer une ressource Speech dans Azure

> [!NOTE]
> Vous pouvez créer autant d’abonnements de niveau standard que vous le souhaitez dans une ou plusieurs régions. Toutefois, vous ne pouvez créer qu’un seul abonnement de niveau gratuit. Les modèles de déploiement au niveau gratuit qui restent inutilisés durant sept jours sont supprimés automatiquement.

Pour ajouter une ressource de service Speech (niveau gratuit ou payant) à votre compte Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en utilisant votre compte Microsoft.

1. Sélectionnez **Créer une ressource** en haut à gauche du portail. Si vous ne voyez pas l’option **Créer une ressource**, vous pouvez toujours la trouver en sélectionnant le menu réduit dans le coin supérieur gauche :

   ![bouton de navigation réduit](media/index/collapsed-nav.png)

1. Dans la fenêtre **Nouveau**, saisissez « speech » dans la zone de recherche et appuyez sur ENTRÉE.

1. Dans les résultats de la recherche, sélectionnez **Speech**.

   ![résultats de la recherche speech](media/index/speech-search.png)

1. Sélectionnez **Créer**, puis :

   - Donnez un nom unique à votre nouvelle ressource. Ce nom vous permet de faire la distinction entre plusieurs abonnements au même service.
   - Choisissez l’abonnement Azure associé à la nouvelle ressource pour déterminer la façon dont les frais sont facturés.
   - Choisissez la [région](regions.md) où la ressource sera utilisée.
   - Choisissez soit un niveau tarifaire payant (S0) ou gratuit (F0). Pour obtenir des informations complètes sur la tarification et les quotas d’utilisation pour chaque niveau, sélectionnez **Afficher tous les détails de la tarification**.
   - Créez un groupe de ressources pour cet abonnement Speech ou affectez l’abonnement à un groupe de ressources existant. Les groupes de ressources vous permettent d’organiser vos différents abonnements Azure.
   - Sélectionnez **Create** (Créer). Cela vous permet d’atteindre la vue d’ensemble du déploiement et d’afficher les messages de progression de ce dernier.

Le déploiement de votre nouvelle ressource Speech prend quelques instants. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource** et, dans le volet de navigation gauche, sélectionnez **Clés** pour afficher vos clés d’abonnement au service Speech. Chaque abonnement dispose de deux clés ; vous pouvez utiliser l’une ou l’autre dans votre application. Pour copier-coller rapidement une clé dans votre éditeur de code ou dans un autre emplacement, cliquez sur le bouton Copier à côté de chaque clé, puis changez de fenêtre pour coller le contenu du Presse-papiers à l’emplacement souhaité.

> [!IMPORTANT]
> Ces clés d’abonnement sont utilisées pour accéder à votre API Cognitive Service. Ne partagez pas vos clés. Stockez-les en toute sécurité, par exemple, à l’aide d’Azure Key Vault. Nous vous recommandons également de régénérer ces clés régulièrement. Une seule clé est nécessaire pour effectuer un appel d’API. Lors de la régénération de la première clé, vous pouvez utiliser la deuxième clé pour un accès continu au service.

## <a name="switch-to-a-new-subscription"></a>Basculer vers un nouvel abonnement

Pour basculer d’un abonnement à un autre, par exemple quand votre essai gratuit expire ou quand vous publiez votre application, remplacez la région et la clé d’abonnement dans votre code par celles de la nouvelle ressource Azure.

## <a name="about-regions"></a>À propos des régions

- Si votre application utilise un [SDK Speech](speech-sdk.md), vous fournissez le code de région, tel que `westus`, quand vous créez une configuration de reconnaissance vocale.
- Si votre application utilise l’une des [API REST](rest-apis.md) du service Speech, la région fait partie de l’URI de point de terminaison que vous utilisez pour effectuer des requêtes.
- Les clés créées pour une région sont valides uniquement dans cette région. Si vous essayez de les utiliser avec d’autres régions, des erreurs d’authentification se produisent.

## <a name="next-steps"></a>Étapes suivantes

Effectuez l’un de nos Démarrages rapides de 10 minutes ou consultez nos exemples de SDK :

> [!div class="nextstepaction"]
> [Démarrage rapide : Reconnaissance vocale en C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Exemples du SDK Speech](speech-sdk.md#get-the-samples)
