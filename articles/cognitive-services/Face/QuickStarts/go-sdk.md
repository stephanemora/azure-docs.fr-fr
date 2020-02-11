---
title: 'Démarrage rapide : Bibliothèque de client Visage pour Go | Microsoft Docs'
description: Découvrez la bibliothèque de client Visage pour Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961226"
---
# <a name="quickstart-face-client-library-for-go"></a>Démarrage rapide : Bibliothèque de client Visage pour Go

Découvrez la bibliothèque de client Visage pour Go. Suivez les étapes suivantes pour installer la bibliothèque et essayer nos exemples de tâches de base. Le service Visage vous donne accès à des algorithmes avancés pour la détection et la reconnaissance des visages dans des images.

Vous pouvez effectuer les tâches suivantes à l’aide de la bibliothèque de client du service Visage pour Go :

* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group)
* [Identifier un visage](#identify-a-face)
* [Prendre une capture instantanée pour la migration de données](#take-a-snapshot-for-data-migration)

[Documentation de référence](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [Téléchargement du SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Conditions préalables requises

* Un abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)
* La dernière version de [Go](https://golang.org/dl/)

## <a name="set-up"></a>Configurer

### <a name="create-a-face-azure-resource"></a>Créer une ressource Visage Azure 

Commencez à utiliser le service Visage en créant une ressource Azure. Choisissez le type de ressource qui vous convient :

* Une [ressource d’essai](https://azure.microsoft.com/try/cognitive-services/#decision) (aucun abonnement Azure n’est nécessaire) : 
    * Valable pendant sept jours, gratuitement. Une fois l’inscription terminée, une clé d’essai et un point de terminaison seront disponibles sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Cette option est intéressante si vous voulez essayer le service Visage, mais que vous n’avez pas d’abonnement Azure.
* Une [ressource du service Visage](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) :
    * Disponible via le portail Azure jusqu’à ce que vous supprimiez la ressource.
    * Utilisez le niveau tarifaire Gratuit pour tester le service, puis effectuez par la suite une mise à niveau vers un niveau payant pour la production.
* Une [ressource multiservice](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) :
    * Disponible via le portail Azure jusqu’à ce que vous supprimiez la ressource.  
    * Utilisez la même clé et le même point de terminaison pour vos applications, dans plusieurs services Cognitive Services.

### <a name="create-an-environment-variable"></a>Créer une variable d’environnement

>[!NOTE]
> Les points de terminaison pour les ressources autres que d’essai créées après le 1er juillet 2019 utilisent le format de sous-domaine personnalisé indiqué ci-dessous. Pour obtenir plus d’informations et une liste complète des points de terminaison régionaux, consultez [Noms de sous-domaines personnalisés pour Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

En utilisant votre clé et le point de terminaison de la ressource que vous avez créée, créez deux variables d’environnement pour l’authentification :
* `FACE_SUBSCRIPTION_KEY` : clé de ressource pour l’authentification de vos requêtes.
* `FACE_ENDPOINT` : point de terminaison de ressource pour l’envoi de requêtes d’API. Il se présente comme suit : 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Utilisez les instructions pour votre système d’exploitation.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

Après avoir ajouté la variable d’environnement, redémarrez la fenêtre de console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source ~/.bashrc` depuis la fenêtre de console pour appliquer les changements.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Modifiez votre profil `.bash_profile` et ajoutez la variable d’environnement :

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Après avoir ajouté la variable d’environnement, exécutez `source .bash_profile` depuis la fenêtre de console pour appliquer les changements.
***

### <a name="create-a-go-project-directory"></a>Créer un répertoire de projet Go

Dans une fenêtre de console (cmd, PowerShell, terminal, bash), créez un espace de travail pour votre projet Go (nommé `my-app`) et accédez-y.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Votre espace de travail contiendra trois dossiers :

* **src** : ce répertoire contient le code source et les packages. Tous les packages installés à l’aide de la commande `go get` se trouvent dans ce dossier.
* **pkg** : ce répertoire contient les objets de package Go compilés. Ces fichiers ont tous une extension `.a`.
* **bin** : ce répertoire contient les fichiers exécutables binaires créés lors de l’exécution de `go install`.

> [!TIP]
> Pour plus d’informations sur la structure d’un espace de travail Go, consultez la [documentation du langage Go](https://golang.org/doc/code.html#Workspaces). Ce guide comprend des informations pour configurer `$GOPATH` et `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installer la bibliothèque de client pour Go

Ensuite, installez la bibliothèque de client pour Go :

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

Ou, si vous utilisez dep, au sein de votre dépôt, exécutez :

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Créer une application Go

Ensuite, créez un fichier nommé `sample-app.go` dans le répertoire **src** :

```bash
cd src
touch sample-app.go
```

Ouvrez `sample-app.go` dans l’éditeur de texte ou l’environnement de développement intégré de votre choix. Ajoutez ensuite le nom du package et importez les bibliothèques suivantes :

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Ensuite, vous commencerez à ajouter du code pour effectuer différentes opérations du service Visage.

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Go du service Visage.

|Name|Description|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Cette classe représente votre autorisation d’utiliser le service Visage. Vous en avez besoin pour toutes les fonctionnalités de Visage. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes. |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Cette classe gère les tâches de détection et de reconnaissance de base que vous pouvez effectuer avec les visages. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Cette classe représente toutes les données détectées à partir d’un visage unique dans une image. Vous pouvez l’utiliser pour récupérer des informations détaillées sur le visage.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Cette classe gère les constructions **FaceList** stockées dans le cloud, comprenant un ensemble de visages assortis. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Cette classe gère les constructions **Person** stockées dans le cloud, comprenant un ensemble de visages appartenant à une même personne.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Cette classe gère les constructions **PersonGroup** stockées dans le cloud, comprenant un ensemble d’objets **Person** assortis. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Cette classe gère la fonctionnalité de capture instantanée. Vous pouvez l’utiliser pour enregistrer temporairement toutes vos données de visage basées sur le cloud et migrer ces données vers un nouvel abonnement Azure. |

## <a name="code-examples"></a>Exemples de code

Ces exemples de code vous montrent comment effectuer des tâches de base à l’aide de la bibliothèque de client du service Visage pour Go :

* [Authentifier le client](#authenticate-the-client)
* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group)
* [Identifier un visage](#identify-a-face)
* [Prendre une capture instantanée pour la migration de données](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE] 
> Ce guide de démarrage rapide suppose que vous avez [créé des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour votre clé et votre point de terminaison du service Visage, nommées `FACE_SUBSCRIPTION_KEY` et `FACE_ENDPOINT` respectivement.

Créez une fonction **main** et ajoutez-lui le code suivant pour instancier un client avec votre point de terminaison et votre clé. Créez un objet **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Ce code instancie également un objet de contexte, qui est nécessaire pour la création d’objets clients. Il définit aussi un emplacement distant où se trouvent certains des exemples d’images utilisés dans ce guide de démarrage rapide.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

Ajoutez le code suivant à la méthode **main**. Ce code définit un exemple d’image distante et spécifie les fonctionnalités de visage à extraire de l’image. Il définit également le modèle IA à utiliser pour extraire des données du ou des visages détectés. Pour plus d’informations sur ces options, consultez [Spécifier un modèle de reconnaissance](../Face-API-How-to-Topics/specify-recognition-model.md). Enfin, la méthode **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** effectue l’opération de détection de visage sur l’image et enregistre les résultats dans la mémoire du programme.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Afficher les données de visage détectées

Le bloc de code suivant prend le premier élément dans le tableau d’objets **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** et affiche ses attributs sur la console. Si vous avez utilisé une image comportant plusieurs visages, vous devez faire une itération dans le tableau à la place.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Rechercher des visages semblables

Le code suivant recherche un ensemble de visages (la cible) correspondant à un unique visage détecté (la source). Quand il trouve une correspondance, il affiche l’ID du visage correspondant sur la console.

### <a name="detect-faces-for-comparison"></a>Détecter des visages pour les comparer

Tout d’abord, enregistrez une référence au visage qui a été détecté dans la section [Détecter des visages dans une image](#detect-faces-in-an-image). Ce visage constituera la source.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Entrez maintenant le code suivant pour détecter un ensemble de visages dans une autre image. Ces visages constitueront la cible.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Rechercher des correspondances

Le code suivant utilise la méthode **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** pour rechercher tous les visages cibles qui correspondent au visage source.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Afficher les correspondances

Le code suivant affiche les détails relatifs aux correspondances sur la console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Créer et entraîner un groupe de personnes

Pour suivre ce scénario, vous devez enregistrer les images suivantes dans le répertoire racine de votre projet : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Ce groupe d’images contient trois ensembles d’images avec un seul visage qui correspondent à trois personnes différentes. Le code définit trois objets **PersonGroup Person** et les associe aux fichiers image qui commencent par `woman`, `man` et `child`.

### <a name="create-persongroup"></a>Créer un PersonGroup

Une fois que vous avez téléchargé vos images, ajoutez le code suivant en bas de la méthode **main**. Ce code authentifie un objet **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** , puis l’utilise pour définir un nouvel objet **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Créer des objets PersonGroup Person

Le bloc de code suivant authentifie un objet **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** et l’utilise pour définir trois nouveaux objets **PersonGroup Person**. Ces objets représentent chacun une seule personne dans l’ensemble d’images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Attribuer des visages aux personnes

Le code suivant trie les images en fonction de leur préfixe, détecte les visages, puis attribue les visages à leur objet **PersonGroup Person** respectif sur la base du nom du fichier image.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Entraîner le PersonGroup

Une fois que vous avez attribué les visages, vous entraînez le **PersonGroup** pour qu’il puisse identifier les caractéristiques visuelles associées à chacun de ses objets **Person**. Le code suivant appelle la méthode **train** asynchrone et interroge le résultat en affichant l’état sur la console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identifier un visage

Le code suivant recherche l’identité de chaque personne dans une image contenant plusieurs visages. Il compare chaque visage détecté à un **PersonGroup**, une base de données comprenant différents objets **Person** dont les caractéristiques du visage sont connues.

> [!IMPORTANT]
> Pour exécuter cet exemple, vous devez d’abord exécuter le code fourni à la section [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obtenir une image de test

Le code suivant recherche une image _test-image-person-group.jpg_ à la racine du projet et la charge dans la mémoire du programme. Vous trouverez cette image dans le même dépôt que les images utilisées dans la section [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group) : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Détecter les visages sources dans l’image de test

Le bloc de code suivant effectue une détection de visage ordinaire sur l’image de test pour récupérer toutes les données de visage et les enregistrer dans un tableau.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identifier des visages

La méthode **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** prend le tableau des visages détectés et les compare à l’objet **PersonGroup** (qui a été défini et entraîné dans la section précédente). Si elle trouve une correspondance entre un visage détecté et un objet **Person** dans le groupe, elle enregistre le résultat.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Ce code affiche ensuite les résultats de correspondance détaillés sur la console.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Vérifier les visages

L’opération Vérifier accepte un ID de visage et soit un autre ID de visage, soit un objet **Person**, et détermine s’ils appartiennent à la même personne.

Le code suivant détecte les visages dans deux images sources, puis il compare chaque visage détecté à un visage détecté dans une image cible.

### <a name="get-test-images"></a>Obtenir les images de test

Les blocs de code suivants déclarent des variables qui pointent vers les images sources et cibles pour l’opération de vérification.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Détecter les visages à vérifier

Le code suivant détecte des visages dans les images source et cible, et les enregistre dans des variables.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Obtenir les résultats de la vérification

Le code suivant compare chacune des images sources à l’image cible et affiche un message indiquant si elles appartiennent à la même personne.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Prendre une capture instantanée pour la migration de données

La fonctionnalité de captures instantanées vous permet de déplacer les données de visage enregistrées, telles qu’un **PersonGroup** entraîné, vers un autre abonnement Azure Cognitive Services Visage. Utilisez cette fonctionnalité si, par exemple, vous avez créé un objet **PersonGroup** avec un abonnement d’essai gratuit et que vous voulez maintenant migrer cet objet vers un abonnement payant. Consultez la section [Migrer vos données de visage](../Face-API-How-to-Topics/how-to-migrate-face-data.md) pour obtenir une vue d’ensemble de la fonctionnalité de captures instantanées.

Dans cet exemple, vous allez migrer l’objet **PersonGroup** que vous avez créé à l’étape [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group). Vous pouvez d’abord terminer cette section ou utiliser vos propres constructions de données Visage.

### <a name="set-up-target-subscription"></a>Configurer l’abonnement cible

Tout d’abord, vous avez besoin de configurer un deuxième abonnement Azure avec une ressource Visage. Pour ce faire, répétez les étapes de la section [Configurer](#set-up). 

Après quoi, créez les variables suivantes en haut de la méthode **main**. Vous devez également créer des variables d’environnement pour l’ID d’abonnement de votre compte Azure ainsi que la clé, le point de terminaison et l’ID d’abonnement de votre nouveau compte (cible).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Ensuite, ajoutez la valeur de l’ID d’abonnement dans un tableau pour les étapes suivantes.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Authentifier le client cible

Plus loin dans le script, enregistrez votre objet client d’origine en tant que client source, puis authentifiez un nouvel objet client pour votre abonnement cible. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Prendre un instantané

L’étape suivante consiste à prendre l’instantané avec **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** . Cette opération enregistre les données de visage de votre abonnement d’origine à un emplacement temporaire dans le cloud. Cette méthode retourne un ID que vous utilisez pour interroger l’état de l’opération.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Interrogez ensuite l’ID jusqu’à ce que l’opération soit terminée.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Appliquer l’instantané

Utilisez l’opération **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** pour écrire les nouvelles données de visage chargées dans votre abonnement cible. Cette méthode retourne également un ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Là encore, interrogez l’ID jusqu’à ce que l’opération soit terminée.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

Quand vous avez terminé ces étapes, vous pouvez accéder à vos constructions de données de visage à partir de votre nouvel abonnement (cible).

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application Go avec la commande `go run [arguments]` à partir du répertoire de votre application.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Si vous avez créé un objet **PersonGroup** dans le cadre de ce guide de démarrage rapide et que vous voulez le supprimer, appelez la méthode **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** . Si vous avez migré des données à l’aide de la fonctionnalité de capture instantanée dans le cadre de ce guide de démarrage rapide, vous devez également supprimer le **PersonGroup** enregistré dans l’abonnement cible.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à effectuer différentes tâches de base en utilisant la bibliothèque Visage pour Go. Pour plus d’informations sur la bibliothèque, reportez-vous à la documentation de référence.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Visage (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Qu’est ce que le service Visage ?](../overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
