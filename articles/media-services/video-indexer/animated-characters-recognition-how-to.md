---
title: Procédures pour la détection des personnages animés avec Video Indexer
titleSuffix: Azure Media Services
description: Cette procédure illustre comment utiliser la détection de personnages animés avec Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531556"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Utiliser la détection de personnages animés (préversion) avec le portail et l’API 

Azure Media Services Video Indexer prend en charge la détection, le regroupement et la reconnaissance de personnages dans du contenu animé. Cette fonctionnalité est disponible par le biais du portail Azure et d’une API. Consultez [cette rubrique de présentation](animated-characters-recognition.md).

Cet article explique comment utiliser la détection de personnages animés avec le portail Azure et l’API Video Indexer.

## <a name="use-the-animated-character-detection-with-portal"></a>Utiliser la détection de personnages animés avec le portail 

Dans les comptes d’évaluation, l’intégration de Custom Vision est gérée par Video Indexer. Vous pouvez commencer à créer et à utiliser le modèle de personnages animés. Si vous utilisez le compte d’évaluation, vous pouvez ignorer la section suivante (« Connexion de votre compte Custom Vision »).

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Connexion de votre compte Custom Vision (comptes payants uniquement)

Si vous êtes propriétaire d’un compte Video Indexer payant, vous devez d’abord connecter un compte Custom Vision. Si vous n’avez pas encore de compte Custom Vision, créez-en un. Pour plus d’informations, consultez [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Les deux comptes doivent se trouver dans la même région. L’intégration de Custom Vision n’est actuellement pas prise en charge dans la région du Japon.

Les comptes payants qui ont accès à leur compte Custom Vision peuvent voir les modèles et les images marquées à cet endroit. Apprenez-en davantage sur l’ [amélioration de votre classifieur dans Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). 

Notez que l’entraînement du modèle doit être effectué uniquement via Video Indexer, et non pas via le site web de Custom Vision. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Connecter un compte Custom Vision à l’aide de l’API 

Procédez comme suit pour connecter votre compte Custom Vision à Video Indexer ou pour modifier le compte Custom Vision actuellement connecté à Video Indexer :

1. Accédez à [www.customvision.ai](https://www.customvision.ai) et connectez-vous.
1. Copiez les clés pour les ressources d’entraînement et de prédiction :

    > [!NOTE]
    > Pour fournir toutes les clés, vous devez disposer de deux ressources distinctes dans Custom Vision, une pour la formation et une pour la prédiction.
1. Fournissez d’autres informations :

    * Point de terminaison 
    * ID de la ressource de prédiction
1. Parcourez et connectez-vous à [Video Indexer](https://vi.microsoft.com/).
1. Cliquez sur le point d’interrogation dans le coin supérieur droit de la page, puis choisissez **Référence de l’API**.
1. Vérifiez que vous êtes abonné à Gestion des API en cliquant sur l’onglet **Produits**. Si vous disposez d’une API connectée, vous pouvez passer à l’étape suivante ; sinon, abonnez-vous. 
1. Dans le portail des développeurs, cliquez sur la **Toutes les informations de référence sur l’API** et accédez à **Opérations**.  
1. Sélectionnez **Connecter un compte Custom Vision (PRÉVERSION)** et cliquez sur **Essayer**.
1. Renseignez les champs obligatoires, ainsi que le jeton d’accès, puis cliquez sur **Envoyer**. 

    Pour plus d’informations sur la façon d’obtenir le jeton d’accès Video Indexer, accédez au [portail des développeurs](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token) et consultez la [documentation correspondante](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Une fois que l’appel a renvoyé une réponse 200 OK, votre compte est connecté.
1. Pour vérifier votre connexion en accédant au portail [Video Indexer](https://vi.microsoft.com/)) :
1. Cliquez sur le bouton **Personnalisation du modèle de contenu** dans l’angle supérieur droit.
1. Accédez à l’onglet **Personnages animés**.
1. Une fois que vous avez cliqué sur Gérer les modèles dans Custom Vision, vous êtes transféré vers le compte Custom Vision que vous venez de connecter.

> [!NOTE]
> À l’heure actuelle, seuls les modèles qui ont été créés via Video Indexer sont pris en charge. Les modèles créés par le biais de Custom Vision ne sont pas disponibles. En outre, la meilleure pratique consiste à modifier les modèles qui ont été créés via Video Indexer uniquement par le biais de la plateforme Video Indexer, car les modifications apportées via Custom Vision peuvent entraîner des résultats inattendus.

### <a name="create-an-animated-characters-model"></a>Créer un modèle de personnages animés

1. Accédez au site web [Video Indexer](https://vi.microsoft.com/) et connectez-vous.
1. Pour personnaliser un modèle dans votre compte, sélectionnez le bouton **Personnalisation du modèle de contenu** à gauche de la page.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Personnaliser le modèle de contenu dans Video Indexer":::
1. Accédez à l'onglet **Personnages animés** dans la section de personnalisation du modèle.
1. Cliquez sur **Ajouter un modèle**.
1. Nommez votre modèle, puis cliquez sur Entrée pour enregistrer le nom.

> [!NOTE]
> La meilleure pratique consiste à avoir un modèle de vision personnalisé pour chaque série animée. 

### <a name="index-a-video-with-an-animated-model"></a>Indexer une vidéo avec un modèle animé

Pour l’entraînement initial, chargez au moins deux vidéos. Chacune doit de préférence dépasser 15 minutes pour espérer obtenir un bon modèle de reconnaissance. Si vous avez des épisodes plus courts, nous vous recommandons de charger au moins 30 minutes de contenu vidéo avant l’entraînement. Cela vous permet de fusionner des groupes qui appartiennent au même personnage à partir de scènes et d’arrière-plans différents et, par conséquent, d’augmenter la probabilité de détecter le personnage dans les prochains épisodes que vous indexerez. Pour entraîner un modèle sur plusieurs vidéos (épisodes), vous devez toutes les indexer avec le même modèle d’animation. 

1. Cliquez sur le bouton **Charger**.
1. Choisissez une vidéo à télécharger (à partir d’un fichier ou d’une URL).
1. Cliquez sur **Options avancées**.
1. Sous **Personnes/Personnages animés**, choisissez **Modèles d’animation**.
1. Si vous avez un modèle, il est automatiquement choisi, et si vous avez plusieurs modèles, vous pouvez choisir celui qui convient dans le menu déroulant.
1. Cliquez sur Télécharger.
1. Une fois la vidéo indexée, les personnages détectés s’affichent dans la section **Personnages animés** du volet **Insights**.

Avant de marquer et de former le modèle, tous les personnages animés sont nommés « Inconnu #X ». Une fois le modèle formé, ils sont également reconnus.

### <a name="customize-the-animated-characters-models"></a>Personnaliser les modèles de personnages animés

1. Nommez les personnages dans Video Indexer.

    1. Une fois que le modèle a créé le groupe de personnages, il est recommandé de passer en revue ces groupes dans Custom Vision. 
    1. Pour étiqueter un personnage animé dans votre vidéo, accédez à l’onglet  **Insights**  et cliquez sur le bouton  **Modifier**  en haut à droite de la fenêtre. 
    1. Dans le volet  **Insights** , cliquez sur l’un des personnages animés détectés et remplacez son nom « Unknown #X » (Inconnu n°X) par un nom temporaire (ou par le nom précédemment affecté au personnage). 
    1. Après avoir tapé le nouveau nom, cliquez sur l’icône de coche en regard du nouveau nom. Cela enregistre le nouveau nom dans le modèle dans Video Indexer. 
1. Comptes payants uniquement : passez en revue les groupes dans Custom Vision 

    > [!NOTE]
    > Les comptes payants qui ont accès à leur compte Custom Vision peuvent voir les modèles et les images marquées à cet endroit. Apprenez-en davantage sur l’ [amélioration de votre classifieur dans Custom Vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). Il est important de noter que l’entraînement du modèle doit être effectué uniquement via Video Indexer (comme décrit dans cette rubrique), et non pas via le site web de Custom Vision. 

    1. Accédez à la page **Modèles personnalisés** dans Video Indexer et choisissez l’onglet **Personnages animés**. 
    1. Cliquez sur le bouton Modifier du modèle sur lequel vous travaillez pour le gérer dans Custom Vision. 
    1. Passez en revue chaque groupe de personnages : 

        * Si le groupe contient des images non liées, il est recommandé de les supprimer sur le site web de Custom Vision. 
        * Si des images appartiennent à un autre personnage, modifiez l’étiquette sur ces images spécifiques en cliquant sur l’image, en ajoutant l’étiquette appropriée et en supprimant l’étiquette incorrecte. 
        * Si le groupe n’est pas correct, à savoir qu’il contient principalement des images sans personnage ou des images de plusieurs personnages, vous pouvez le supprimer sur le site web de Custom Vision ou dans les insights Video Indexer. 
        * L’algorithme de regroupement sépare parfois vos personnages en différents groupes. Par conséquent, il est recommandé de donner le même nom à tous les groupes appartenant au même personnage (dans les insights Video Indexer), ce qui aura pour effet immédiat d’afficher tous ces groupes sur le site web de Custom Vision. 
    1. Une fois le groupe affiné, veillez à ce que le nom initial avec lequel vous l’avez étiqueté reflète le personnage dans le groupe. 
1. Effectuer l’apprentissage du modèle 

    1. Une fois que vous avez fini de modifier tous les noms souhaités, vous devez effectuer la formation du modèle. 
    1. Une fois qu’un personnage est formé dans le modèle, il est reconnu dans la vidéo suivante indexée avec ce modèle. 
    1. Ouvrez la page de personnalisation et cliquez sur l’onglet  **Personnages animés** , puis cliquez sur le bouton  **Entraîner** afin d’entraîner votre modèle. Pour conserver la connexion entre Video 
    
Indexer et le modèle, n’entraînez pas le modèle sur le site web Custom Vision (les comptes payants ont accès au site web Custom Vision), mais uniquement dans Video Indexer. Une fois l’apprentissage effectué, toute vidéo qui sera indexée ou réindexée à l’aide de ce modèle reconnaîtra les personnages formés. 

## <a name="delete-an-animated-character-and-the-model"></a>Supprimer un personnage animé et le modèle

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
    Si vous n’avez pas encore de compte Custom Vision, créez-en un. Pour plus d’informations, consultez [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

    [Connectez votre compte Custom Vision à l'aide de l’API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. Créez un modèle de personnages animés.

    Utilisez l'API [Créer un modèle d’animation](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model).
1. Indexez ou réindexez une vidéo.

    Utilisez l' API de [réindexation](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video). 
1. Personnalisez les modèles de personnages animés.

    Utilisez l'API [Former un modèle d’animation](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model).

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