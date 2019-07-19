---
title: Recettes de conteneur Docker Compose
titleSuffix: Azure Cognitive Services
description: Découvrez comment déployer plusieurs conteneurs Cognitive Services. Cette procédure vous montre comment orchestrer plusieurs images conteneur Docker avec Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445788"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Utiliser plusieurs conteneurs dans un réseau privé avec Docker Compose

Découvrez comment déployer plusieurs conteneurs Cognitive Services. Cette procédure vous montre comment orchestrer plusieurs images conteneur Docker avec Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) est un outil destiné à définir et exécuter des applications Docker à plusieurs conteneurs. Avec Compose, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec une seule commande, vous créez et démarrez tous les services à partir de votre configuration.

Quand le scénario s’y prête, il peut être intéressant d’orchestrer plusieurs images conteneur sur un même ordinateur hôte. Dans cet article, nous allons regrouper les services Reconnaître le texte et Form Recognizer.

## <a name="prerequisites"></a>Prérequis

Cette procédure nécessite plusieurs outils qui doivent être installés et exécutés localement.

* Utilisez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Moteur docker](https://www.docker.com/products/docker-engine) ; vérifiez que l’interface CLI Docker fonctionne dans une fenêtre de console.
* Une ressource Azure avec le niveau de tarification approprié. Certains niveaux tarifaires ne fonctionnent pas avec ce conteneur :
  * La ressource **Vision par ordinateur** avec les niveaux tarifaires F0 ou Standard.
  * La ressource **Form Recognizer** avec les niveaux tarifaires F0 ou Standard.
  * La ressource **Cognitive Services** avec un niveau tarifaire S0.

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Complétez et envoyez le [formulaire de demande de conteneurs Cognitive Services Speech](https://aka.ms/speechcontainerspreview/) pour demander l’accès au conteneur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Fichier Docker Compose

Le fichier YAML définit tous les services à déployer. Ces services s’appuient soit sur un `DockerFile` soit sur une image conteneur existante. Dans ce cas, nous utiliserons deux images d’aperçu. Copiez et collez le fichier YAML suivant et enregistrez-le sous le nom *docker-compose.yaml*. Entrez les valeurs appropriées pour _apikey_, _billing_ et _endpoint URI_ dans le fichier _docker-compose.yml_ ci-dessous.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Créez les répertoires sur l’ordinateur hôte qui sont spécifiés sous le nœud `volumes`. Il s’agit d’une étape incontournable, car les répertoires doivent exister avant de monter une image avec des liaisons de volume.

## <a name="start-the-configured-docker-compose-services"></a>Démarrer les services Docker Compose configurés

Un fichier Docker Compose permet de gérer tous les cycles de vie du service défini (démarrage/arrêt et reconstruction des services, affichage de l’état des services et diffusion de journaux). Ouvrez une interface de ligne de commande à partir du répertoire du projet (où réside le fichier *docker-compose.yaml*).

> [!NOTE]
> Pour éviter les erreurs, vérifiez que l’ordinateur hôte partage correctement les lecteurs avec le **moteur Docker**. Par exemple, si *e:\publicpreview* est utilisé comme répertoire dans *docker-compose.yaml*, partagez le *lecteur E* avec Docker.

À partir de l’interface de ligne de commande, exécutez la commande suivante pour démarrer (ou redémarrer) tous les services définis dans *docker-compose.yaml* :

```console
docker-compose up
```

À la première exécution de la commande `docker-compose up` avec cette configuration, **Docker** extrait les images configurées sous le nœud `services` (en les téléchargeant/montant) :

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Une fois les images téléchargées, les services d’image démarrent.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Vérifier la disponibilité du service

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Voici un exemple de sortie :

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Tester le conteneur Reconnaître le texte

Ouvrez un navigateur sur l’ordinateur hôte et accédez à `localhost` avec le port spécifié dans *docker-compose.yaml*, par exemple, `http://localhost:5021/swagger/index.html`. Vous pouvez utiliser la fonctionnalité Essayer de l’API pour tester le point de terminaison Reconnaître le texte.

![Recognize Text Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Tester le conteneur Form Recognizer

Ouvrez un navigateur sur l’ordinateur hôte et accédez à `localhost` avec le port spécifié dans *docker-compose.yaml*, par exemple, `http://localhost:5010/swagger/index.html`. Vous pouvez utiliser la fonctionnalité Essayer de l’API pour tester le point de terminaison Form Recognizer.

![Form Recognizer Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Conteneurs Cognitive Services](../cognitive-services-container-support.md)