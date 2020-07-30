---
title: Détection de personnages animés avec Video Indexer
titleSuffix: Azure Media Services
description: Cette rubrique montre comment utiliser la détection de personnages animés avec Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 3b449f68b25fce19dc034d2a0db0ae5ce8a28ec7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047489"
---
# <a name="animated-character-detection-preview"></a>Détection de personnages animés (préversion)

Azure Media Services Video Indexer prend en charge la détection, le regroupement et la reconnaissance de personnages dans le contenu animé via l'intégration avec [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Cette fonctionnalité est disponible à la fois via le portail et l’API.

Après avoir chargé une vidéo animée avec un modèle d’animation spécifique, Video Indexer extrait les images clés, détecte les personnages animés dans ces images, regroupe les personnages similaires et choisit le meilleur échantillon. Ensuite, il envoie les personnages groupés à Custom Vision qui identifie les personnages en fonction des modèles sur lesquels il a été formé. 

Avant de commencer la formation de votre modèle, les personnages sont détectés sans nom. À mesure que vous ajoutez des noms et que vous formez le modèle, Video Indexer reconnaît les personnages et les nomme en conséquence.

## <a name="flow-diagram"></a>Diagramme de flux

Le diagramme suivant illustre le déroulement du processus de détection de personnages animés.

![Diagramme de flux](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Comptes

En fonction du type de votre compte Video Indexer, différents ensembles de fonctionnalités sont disponibles. Pour plus d’informations sur la connexion de votre compte à Azure, consultez [Créer un compte Video Indexer connecté à Azure](connect-to-azure.md).

* Compte d’essai : Video Indexer utilise un compte Custom Vision interne pour créer un modèle et le connecter à votre compte Video Indexer. 
* Compte payant : Connectez votre compte Custom Vision à votre compte Video Indexer (vous devez d’abord créer un compte si vous n’en avez pas déjà un).

### <a name="trial-vs-paid"></a>Différences entre les comptes d’essai et payants

|Fonctionnalités|Version d’évaluation|Payant|
|---|---|---|
|Compte Custom Vision|Géré en arrière-plan par Video Indexer. |Votre compte Custom Vision est connecté à Video Indexer.|
|Nombre de modèles d’animation|Une|Jusqu’à 100 modèles par compte (limitation de Custom Vision).|
|Formation du modèle|Video Indexer forme le modèle pour de nouveaux personnages et des exemples supplémentaires des personnages existants.|Le propriétaire du compte forme le modèle lorsqu’il est prêt à apporter des modifications.|
|Options avancées dans Custom Vision|Pas d’accès au portail Custom Vision.|Vous pouvez ajuster les modèles vous-même dans le portail Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal"></a>Utiliser la détection de personnages animés avec le portail 

Cette section décrit les étapes à suivre pour commencer à utiliser le modèle de détection de personnages animés. 

Dans la mesure où, dans les comptes d’évaluation, l’intégration de Custom Vision est gérée par Video Indexer, vous pouvez commencer à créer et à utiliser le modèle de personnages animés et ignorer la section suivante (« Connecter votre compte Custom Vision »).

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Connexion de votre compte Custom Vision (comptes payants uniquement)

Si vous êtes propriétaire d’un compte Video Indexer payant, vous devez d’abord connecter un compte Custom Vision. Si vous n’avez pas encore de compte Custom Vision, créez-en un. Pour plus d’informations, consultez [Custom Vision](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Les deux comptes doivent se trouver dans la même région. L’intégration de Custom Vision n’est actuellement pas prise en charge dans la région du Japon.

#### <a name="connect-a-custom-vision-account-with-api"></a>Connecter un compte Custom Vision à l’aide de l’API 

Procédez comme suit pour connecter votre compte Custom Vision à Video Indexer ou pour modifier le compte Custom Vision actuellement connecté à Video Indexer :

1. Accédez à [www.customvision.ai](https://www.customvision.ai) et connectez-vous.
1. Copiez les clés suivantes : 

    * Clé de formation (pour la ressource de formation)
    * Clé de prédiction (pour la ressource de prédiction)
    * Point de terminaison 
    * ID de la ressource de prédiction
    
    > [!NOTE]
    > Pour fournir toutes les clés, vous devez disposer de deux ressources distinctes dans Custom Vision, une pour la formation et une pour la prédiction.
1. Parcourez et connectez-vous à [Video Indexer](https://vi.microsoft.com/).
1. Cliquez sur le point d’interrogation dans le coin supérieur droit de la page, puis choisissez **Référence de l’API**.
1. Vérifiez que vous êtes abonné à Gestion des API en cliquant sur l’onglet **Produits**. Si vous disposez d’une API connectée, vous pouvez passer à l’étape suivante ; sinon, abonnez-vous. 
1. Dans le portail des développeurs, cliquez sur la **Toutes les informations de référence sur l’API** et accédez à **Opérations**.  
1. Sélectionnez **Connecter un compte Custom Vision (PRÉVERSION)** et cliquez sur **Essayer**.
1. Renseignez les champs obligatoires, ainsi que le jeton d’accès, puis cliquez sur **Envoyer**. 

    Pour plus d’informations sur la façon d’obtenir le jeton d’accès Video Indexer, accédez au [portail des développeurs](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?) et consultez la [documentation correspondante](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Une fois que l’appel a renvoyé une réponse 200 OK, votre compte est connecté.
1. Pour vérifier votre connexion en accédant au portail [Video Indexer](https://vi.microsoft.com/)) :
1. Cliquez sur le bouton **Personnalisation du modèle de contenu** dans l’angle supérieur droit.
1. Accédez à l’onglet **Personnages animés**.
1. Une fois que vous avez cliqué sur « Gérer les modèles dans Custom Vision »**, vous êtes transféré vers le compte Custom Vision que vous venez de connecter.

> [!NOTE]
> À l’heure actuelle, seuls les modèles qui ont été créés via Video Indexer sont pris en charge. Les modèles créés par le biais de Custom Vision ne sont pas disponibles. En outre, la meilleure pratique consiste à modifier les modèles qui ont été créés via Video Indexer uniquement par le biais de la plateforme Video Indexer, car les modifications apportées via Custom Vision peuvent entraîner des résultats inattendus.

### <a name="create-an-animated-characters-model"></a>Créer un modèle de personnages animés

1. Accédez au site web [Video Indexer](https://vi.microsoft.com/) et connectez-vous.
1. Cliquez sur le bouton de personnalisation du modèle de contenu en haut à droite de la page.

    ![Personnalisation du modèle de contenu](./media/animated-characters-recognition/content-model-customization.png)
1. Accédez à l'onglet **Personnages animés** dans la section de personnalisation du modèle.
1. Cliquez sur **Ajouter un modèle**.
1. Nommez votre modèle, puis cliquez sur Entrée pour enregistrer le nom.

> [!NOTE]
> La meilleure pratique consiste à avoir un modèle de vision personnalisé pour chaque série animée. 

### <a name="index-a-video-with-an-animated-model"></a>Indexer une vidéo avec un modèle animé

1. Cliquez sur le bouton **Télécharger** dans le menu du haut.
1. Choisissez une vidéo à télécharger (à partir d’un fichier ou d’une URL).
1. Cliquez sur **Options avancées**.
1. Sous **Personnes/Personnages animés**, choisissez **Modèles d’animation**.
1. Si vous avez un modèle, il est automatiquement choisi, et si vous avez plusieurs modèles, vous pouvez choisir celui qui convient dans le menu déroulant.
1. Cliquez sur Télécharger.
1. Une fois la vidéo indexée, les personnages détectés s’affichent dans la section **Personnages animés** du volet **Insights**.

> [!NOTE] 
> Avant de marquer et de former le modèle, tous les personnages animés sont nommés « Inconnu #X ». Une fois le modèle formé, ils sont également reconnus.

### <a name="customize-the-animated-characters-models"></a>Personnaliser les modèles de personnages animés

1. Marquez et formez le modèle.

    1. Marquez le personnage détecté en modifiant son nom. Une fois qu’un personnage est formé dans le modèle, il est reconnu dans la vidéo suivante indexée avec ce modèle. 
    1. Pour marquer un personnage animé dans votre vidéo, accédez à l'onglet **Insights** et cliquez sur le bouton **Modifier** dans le coin supérieur droit de la fenêtre.
    1. Dans le volet **Insights**, cliquez sur l’un des personnages animés détectés, puis remplacez son nom, qui devrait actuellement être « Inconnu #X » (ou le nom précédemment affecté au personnage).
    1. Après avoir tapé le nouveau nom, cliquez sur l’icône de coche en regard du nouveau nom. Cela enregistre le nouveau nom dans le modèle dans Video Indexer.
    1. Une fois que vous avez fini de modifier tous les noms souhaités, vous devez effectuer la formation du modèle.

        Ouvrez la page de personnalisation, cliquez sur l’onglet **Personnages animés** et cliquez sur le bouton **Former** afin de former votre modèle.
         
        Si vous disposez d’un compte payant, vous pouvez cliquer sur le lien **Manage models in Custom Vision** (Gérer les modèles dans Customer Vision) (comme indiqué ci-dessous). Cela vous redirigera vers la page du modèle dans **Custom Vision**.
 
        ![Personnalisation du modèle de contenu](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Une fois l’apprentissage effectué, toute vidéo qui sera indexée ou réindexée à l’aide de ce modèle reconnaîtra les personnages formés. 
    Les comptes payants qui ont accès à leur compte Custom Vision peuvent voir les modèles et les images marquées à cet endroit. En savoir plus sur [l’amélioration de votre classifieur dans Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md).

1. Supprimer un personnage animé.

    1. Pour supprimer un personnage animé dans les insights de votre vidéo, accédez à l'onglet **Insights** et cliquez sur le bouton **Modifier** dans le coin supérieur droit de la fenêtre.
    1. Choisissez le personnage animé, puis cliquez sur le bouton **Supprimer** sous son nom.

    > [!NOTE]
    > Cette opération supprime les informations de cette vidéo, mais n’affecte pas le modèle.

1. Supprimer un modèle.

    1. Cliquez sur le bouton **Personnalisation du modèle de contenu** dans le menu du haut et accédez à l’onglet **Personnages animés**.
    1. Cliquez sur l’icône de points de suspension à droite du modèle que vous souhaitez supprimer, puis sur le bouton Supprimer.
    
    * Compte payant : le modèle sera déconnecté de Video Indexer et vous ne pourrez pas le reconnecter.
    * Compte d’essai : le modèle sera également supprimé de Custom Vision. 
    
        > [!NOTE]
        > Dans un compte d’essai, vous n’avez qu’un seul modèle que vous pouvez utiliser. Après l’avoir supprimé, vous ne pouvez pas former d’autres modèles.

## <a name="use-the-animated-character-detection-with-api"></a>Utiliser la détection de personnages animés avec l’API 

1. Connectez un compte Custom Vision.

    Si vous êtes propriétaire d’un compte Video Indexer payant, vous devez d’abord connecter un compte Custom Vision. <br/>
    Si vous n’avez pas encore de compte Custom Vision, créez-en un. Pour plus d’informations, consultez [Custom Vision](../../cognitive-services/custom-vision-service/home.md).

    [Connectez votre compte Custom Vision à l'aide de l’API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Créez un modèle de personnages animés.

    Utilisez l'API [Créer un modèle d’animation](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag).
1. Indexez ou réindexez une vidéo.

    Utilisez l' API de [réindexation](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?). 
1. Personnalisez les modèles de personnages animés.

    Utilisez l'API [Former un modèle d’animation](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag).

### <a name="view-the-output"></a>Affichage du résultat

Vérifiez les personnages animés dans le fichier JSON généré.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Limites

* Actuellement, la fonctionnalité « Identification d’animation » n’est pas prise en charge dans la région Asie de l’Est.
* Les personnages qui semblent être petits ou éloignés dans la vidéo peuvent ne pas être identifiés correctement si la qualité de la vidéo est médiocre.
* Il est recommandé d’utiliser un modèle par ensemble de personnages animés (par exemple, une série animée).

## <a name="next-steps"></a>Étapes suivantes

[Présentation de Video Indexer](video-indexer-overview.md)
