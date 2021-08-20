---
title: 'Démarrage rapide : Encodage simple avec Python pour Media Services'
description: Ce guide de démarrage rapide vous montre comment effectuer un encodage simple avec Python et Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 7/2/2021
ms.author: inhenkel
ms.openlocfilehash: a0534bc562d20f96cc1c12a8b4dbe0adfc8f9282
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284406"
---
# <a name="media-services-basic-encoding-with-python"></a>Encodage simple pour Media Services avec Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Introduction

Ce guide de démarrage rapide vous montre comment effectuer un encodage simple avec Python et Azure Media Services. Il utilise l’API 2020-05-01 Media Service v3.

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Créez un groupe de ressources à utiliser avec ce guide de démarrage rapide.
- Créez un compte Media Services v3.
- Récupérez la clé de votre compte de stockage.
- Créez un principal de service et une clé.

## <a name="get-the-sample"></a>Obtenir l’exemple

Créez une duplication et clonez l’exemple qui se trouve dans le [dépôt d’exemples Python](https://github.com/Azure-Samples/media-services-v3-python). Pour ce guide de démarrage rapide, nous travaillons avec l’exemple BasicEncoding.

## <a name="create-the-env-file"></a>Créer le fichier .env

Récupérez les valeurs de votre compte pour créer un fichier *.env*. C’est correct, enregistrez-le sans nom, juste l’extension.  Utilisez *sample.env* comme modèle, puis enregistrez le fichier *.env* dans le dossier BasicEncoder de votre clone local.

## <a name="use-python-virtual-environments"></a>Utiliser des environnements virtuels Python
Pour des exemples, nous vous recommandons de toujours créer et activer un environnement virtuel Python en procédant comme suit :

1. Ouvrir le dossier d’exemples dans VSCode ou dans un autre éditeur
2. Créer l’environnement virtuel

    ``` bash
      # py -3 uses the global python interpreter. You can also use python -m venv .venv.
      py -3 -m venv .venv
    ```

   Cette commande exécute le module Python venv et crée un environnement virtuel dans un dossier nommé .venv.

3. Activez l’environnement virtuel :

    ``` bash
      .venv\scripts\activate
    ```

  Un environnement virtuel est un dossier dans un projet qui isole une copie d’un interpréteur Python spécifique. Une fois que vous avez activé cet environnement (Visual Studio Code le fait automatiquement), l’exécution de pip install installe une bibliothèque seulement dans cet environnement. Quand vous exécutez ensuite votre code Python, il s’exécute dans le contexte exact de l’environnement avec des versions spécifiques de chaque bibliothèque. Quand vous exécutez pip freeze, vous recevez la liste exacte de ces bibliothèques. (Dans de nombreux exemples de cette documentation, vous créez un fichier requirements.txt pour les bibliothèques dont vous avez besoin, puis vous utilisez pip install -r requirements.txt. Un fichier d’exigences est généralement nécessaire lorsque vous déployez du code sur Azure.)

## <a name="set-up"></a>Configurer

Installer et [configurer votre environnement de développement Python local pour Azure](/azure/developer/python/configure-local-development-environment)

Installez la bibliothèque azure-identity pour Python. Ce module est nécessaire pour l’authentification Azure Active Directory. Consultez les détails dans [Bibliothèque de client d’identité Azure pour Python](/python/api/overview/azure/identity-readme#environment-variables)

  ``` bash
  pip install azure-identity
  ```

Installer le SDK Python pour [Azure Media Services](/python/api/overview/azure/media-services)

La page Pypi pour le SDK Media Services Python avec les dernières informations sur la version se trouve dans [azure-mgmt-media](https://pypi.org/project/azure-mgmt-media/)

  ``` bash
  pip install azure-mgmt-media
  ```

Installer le [SDK Stockage Azure pour Python](https://pypi.org/project/azure-storage-blob/)

  ``` bash
  pip install azure-storage-blob
  ```

Si vous le souhaitez, vous pouvez installer tous les éléments nécessaires pour un exemple donné en utilisant le fichier « requirements.txt » qui se trouve dans le dossier des exemples.

  ``` bash
  pip install -r requirements.txt
  ```

## <a name="try-the-code"></a>Essayer le code

Le code ci-dessous est entièrement mis en commentaire.  Utilisez l’ensemble du script ou une partie de celui-ci pour votre propre script.

Dans cet exemple, un nombre aléatoire est généré pour nommer les éléments afin de vous permettre de les identifier en tant que groupe créé lors de l’exécution du script.  Le nombre aléatoire est facultatif et peut être supprimé quand vous avez fini de tester le script.

Nous n’utilisons pas dans cet exemple l’URL SAS pour la ressource d’entrée.

[!code-python[Main](../../../media-services-v3-python/BasicEncoding/basic-encoding.py)]

## <a name="delete-resources"></a>Supprimer des ressources

Quand vous avez terminé le guide de démarrage rapide, supprimez les ressources créées dans le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

Familiarisez-vous avec le [SDK Python Media Services](/python/api/azure-mgmt-media/)

## <a name="resources"></a>Ressources

- Consultez l’[API de gestion](/python/api/overview/azure/mediaservices/management) d’Azure Media Services.
- Découvrez comment utiliser les [API Stockage avec Python](/azure/developer/python/azure-sdk-example-storage-use?tabs=cmd)
- En savoir plus sur la [Bibliothèque de client d’identité Azure pour Python](/python/api/overview/azure/identity-readme#environment-variables)
- En savoir plus sur [Azure Media Services v3](./media-services-overview.md).
- En savoir plus sur les [SDK Azure Python](/azure/developer/python)
- En savoir plus sur les [modèles d’utilisation des SDK Azure Python](/azure/developer/python/azure-sdk-library-usage-patterns)
- Rechercher d’autres SDK Azure Python dans l’[index des SDK Azure Python](/azure/developer/python/azure-sdk-library-package-index)
- [Informations de référence sur les SDK Stockage Blob Azure Python](/python/api/azure-storage-blob/)