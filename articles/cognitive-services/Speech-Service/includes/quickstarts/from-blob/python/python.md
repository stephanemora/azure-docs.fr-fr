---
title: 'Démarrage rapide : Reconnaître la voix stockée dans le stockage Blob, C# – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9223fa484d45b927059c73e23e0607526e70fae4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828930"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Charger un fichier source dans un objet blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>Télécharger et installer la bibliothèque de client de l’API

Pour exécuter l’exemple, vous devez générer la bibliothèque Python pour l’API REST qui est générée par [Swagger](https://swagger.io).

Effectuez les étapes suivantes pour procéder à l’installation :

1. Accédez à https://editor.swagger.io
1. Cliquez sur **Fichier**, puis sur **Importer l’URL**.
1. Entrez l’URL de Swagger, en incluant la région de votre abonnement au service Speech : `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Cliquez sur **Générer un client** et sélectionnez **Python**.
1. Enregistrez la bibliothèque de client.
1. Extrayez le fichier python-client-generated.zip téléchargé dans votre système de fichiers.
1. Installez le module python-client extrait dans votre environnement Python à l’aide de pip : `pip install path/to/package/python-client`.
1. Le package installé porte le nom de `swagger_client`. Vous pouvez vérifier que l’installation a réussi à l’aide de la commande `python -c "import swagger_client"`.

> **Remarque :** En raison d’un [bogue connu avec la génération automatique Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541), vous pouvez rencontrer des erreurs lors de l’importation du package `swagger_client`.
> Celles-ci peuvent être corrigées en supprimant la ligne où se trouve le contenu.
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> dans le fichier `swagger_client/models/model.py`, ainsi que la ligne où se trouve le contenu
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> dans le fichier `swagger_client/models/inner_error.py`, à l’intérieur du package installé. Le message d’erreur indique où se trouvent ces fichiers pour votre installation.

## <a name="install-other-dependencies"></a>Installer d’autres dépendances

L’exemple utilise la bibliothèque `requests`. Vous pouvez l’installer go-spew avec la commande

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Commencer par du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(vous devrez remplacer les valeurs de `YourSubscriptionKey`, `YourServiceRegion` et `YourFileUrl` par vos propres valeurs).

## <a name="create-and-configure-an-http-client"></a>Créer et configurer un client HTTP
La première chose dont nous avons besoin est d’un client HTTP disposant d’une URL de base correcte et pour lequel l’authentification a été définie.
Insérez ce code dans `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Générer une demande de transcription
Nous allons ensuite générer la demande de transcription. Ajoutez ce code à `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Envoyer la requête et vérifier son état
Nous allons maintenant envoyer la requête au service Speech et vérifier le code de réponse initial. Ce code de réponse indique simplement si le service a reçu la requête. Le service va retourner une URL dans les en-têtes de réponse qui correspond à l’emplacement où il va stocker l’état de la transcription.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendez que la transcription se termine
Étant donné que le service traite la transcription de manière asynchrone, nous devons interroger l’état régulièrement. Nous allons le vérifier toutes les 5 secondes.

Nous allons énumérer toutes les transcriptions traitées par cette ressource du service Speech, puis rechercher celle que nous avons créée.

Voici le code d’interrogation pour tous les états sauf celui correspondant à une tâche terminée sans erreurs (nous l’aborderons plus tard).
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Afficher les résultats de la transcription
Une fois que le service a terminé la transcription et que celle-ci s’est bien déroulée, les résultats sont stockés dans une autre URL que nous pouvons obtenir dans la réponse d’état.

Ici, nous obtenons ce résultat JSON, puis l’affichons.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Vérifier votre code
À ce stade, votre code doit ressembler à ceci : (Nous avons ajouté des commentaires à cette version) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Vous êtes maintenant prêt à créer votre application et à tester la reconnaissance vocale à l’aide du service Speech.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
