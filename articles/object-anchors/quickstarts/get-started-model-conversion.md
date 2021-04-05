---
title: 'Démarrage rapide : Créer un modèle Object Anchors à utiliser dans une application'
description: Ce démarrage rapide explique comment créer un modèle Object Anchors à partir d’un modèle 3D.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607898"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Démarrage rapide : Créer un modèle Object Anchors à partir d’un modèle 3D

Azure Object Anchors est un service cloud géré qui convertit les modèles 3D en modèles IA qui permettent des expériences de réalité mixte prenant en charge les objets pour HoloLens. Ce démarrage rapide explique comment créer un modèle Object Anchors à partir d’un modèle 3D à l’aide du kit de développement logiciel (SDK) C#/.NET Core.

Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un compte Object Anchors
> * Convertir un modèle 3D pour créer un modèle Object Anchors

## <a name="prerequisites"></a>Prérequis

Pour suivre ce guide de démarrage rapide, veillez à avoir :

* Machine Windows sur laquelle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> est installé.
* <a href="https://git-scm.com" target="_blank">Git pour Windows</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">Kit de développement logiciel (SDK) .NET Core 3.1</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Créer un compte Object Anchors

Tout d’abord, vous devez créer un compte avec le service Object Anchors.

1. Accédez au [portail Azure](https://portal.azure.com/) et sélectionnez **Créer une ressource**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Créer une nouvelle ressource":::

2. Recherchez la ressource **Object Anchors**.

   Recherchez « Object Anchors ».

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Sélectionner la ressource Object Anchors":::

   Sur la ressource **Object Anchors** dans les résultats de la recherche, sélectionnez les **Créer -> Object Anchors**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Créer une ressource Object Anchors":::

3. Dans la boîte de dialogue **Compte Object Anchors** :
    * Entrez un nom de ressource unique.
    * Sélectionnez l’abonnement auquel attacher la ressource.
    * Créez un groupe de ressources ou utilisez un groupe existant.
    * Sélectionnez la région dans laquelle vous souhaitez que votre ressource existe.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Entrer les détails du compte de ressource Object Anchors":::

    Sélectionnez **Créer** pour commencer à créer la ressource.

4. Une fois la ressource créée, sélectionnez **Accéder à la ressource**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Accéder à la ressource":::

5. Sur la page de présentation :

   Prenez note du **Domaine du compte**. Vous en aurez besoin ultérieurement.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Copier le domaine du compte pour votre ressource Object Anchors":::

   Prenez note de l’**ID de compte**. Vous en aurez besoin ultérieurement.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Copier l’ID de compte pour votre ressource Object Anchors":::

   Accédez à la page **Clés d’accès** et prenez note de la **Clé primaire**. Vous en aurez besoin ultérieurement.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Copier la clé de compte pour votre ressource Object Anchors":::

## <a name="get-the-sample-project"></a>Récupération de l’exemple de projet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Convertir un modèle 3D

Vous pouvez maintenant continuer et convertir votre modèle 3D.

1. Ouvrez `quickstarts/conversion/Conversion.sln` dans Visual Studio. Cette solution inclut un seul projet de console C#.

2. Ouvrez le fichier `Configuration.cs` situé dans la racine du projet et remplacez les valeurs `set-me` dans le champs suivants :

   | Champ         | Description                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | **Domaine de compte** du compte Object Anchors créé ci-dessus. |
   | AccountId     | **ID de compte** du compte Object Anchors créé ci-dessus.     |
   | AccountKey    | **Clé primaire** du compte Object Anchors créé ci-dessus     |

   Quatre champs supplémentaires nécessitent une vérification :

    | Champ                    | Description                       |
    | ---                      | ---                               |
    | InputAssetPath           | Chemin d’accès absolu à un modèle 3D sur votre ordinateur local. Les formats de fichier pris en charge sont `fbx`, `ply`, `obj`, `glb` et `gltf`. |
    | AssetDimensionUnit       | Unité de mesure de votre modèle 3D. Toutes les unités de mesure prises en charge sont accessibles à l’aide de l’énumération `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit`. |
    | Gravité                  | Direction du vecteur de gravité du modèle 3D. Ce vecteur 3D donne la direction vers le bas dans le système de coordonnées de votre modèle. Par exemple, si `y` négatif représente la direction vers le bas dans l’espace 3D du modèle, cette valeur est `Vector3(0.0f, -1.0f, 0.0f)`. |

3. Générez et exécutez le projet pour charger votre modèle 3D, inscrivez un nouveau travail de conversion auprès du service, puis attendez qu’il soit terminé. Une fois le travail terminé, le modèle Object Anchors est téléchargé en regard du fichier spécifié dans `InputAssetPath`. Vous devriez obtenir un résultat similaire à la sortie de console suivante :

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Prenez note de l’**ID de travail** pour référence ultérieure. Cela peut être utile lors du débogage ou de la résolution des problèmes.

4. Une fois le travail terminé, vous devriez voir un fichier au format `<Model-Filename-Without-Extension>_<JobID>.ou` dans l’emplacement de sortie spécifié. Par exemple, si le nom de fichier de votre modèle 3D est `chair.ply` et que l’ID de votre travail est `00000000-0000-0000-0000-000000000000`, le nom de fichier généré par le service sera `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un compte Object Anchors et converti un modèle 3D pour créer un modèle Object Anchors. Pour savoir comment intégrer ce modèle avec le Kit de développement logiciel (SDK) Object Anchors dans votre application de réalité mixte, passez à l’un des articles suivants :

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens avec MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
