---
title: Recette d’instance de conteneur Azure
titleSuffix: Azure Cognitive Services
description: Apprendre à déployer des conteneurs Cognitive Services dans une instance de conteneur Azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 4641723ed1ad334ab9f960728f4986b03fd0ac99
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065886"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Déployer et exécuter un conteneur sur Azure Container Instances

Les étapes suivantes permettent de mettre facilement à l’échelle des applications Azure Cognitive Services dans le cloud avec [Azure Container Instances](../../container-instances/index.yml). La conteneurisation vous aide à vous concentrer sur la création de vos applications au lieu de gérer l’infrastructure. Pour plus d’informations sur l’utilisation de conteneurs, voir [Fonctionnalités et avantages](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Prérequis

Cette recette fonctionne avec n’importe quel conteneur Cognitive Services. Vous devez créer la ressource Cognitive Services avant d’utiliser cette recette. Chaque service cognitif qui prend en charge les conteneurs dispose d’un article « Procédure d’installation » qui vous explique comment installer et configurer le service pour un conteneur. Certains services nécessitant un fichier ou un ensemble de fichiers en entrée pour le conteneur, avant d’utiliser cette solution, il est important d’avoir compris le fonctionnement du conteneur et de l’avoir déjà utilisé avec succès.

* Une ressource Azure pour le service cognitif Azure que vous utilisez.
* Une **URL de point de terminaison** de service cognitif : examinez la procédure d’installation du conteneur propre à votre service de façon à trouver l’emplacement de l’URL du point de terminaison sur le portail Azure ainsi qu’un exemple approprié de présentation de l’URL. Le format exact peut changer d’un service à l’autre.
* **Clé** du service cognitif : les clés se trouvent dans la page **Clés** de la ressource Azure. Vous avez seulement besoin d’une des deux clés. La clé est une chaîne de 32 caractères alphanumériques.

* Un seul conteneur Cognitive Services sur votre hôte local (votre ordinateur). Vérifiez que vous pouvez :
  * Extraire l’image à l’aide d’une commande `docker pull`.
  * Exécuter correctement le conteneur local avec tous les paramètres de configuration nécessaires à l’aide d’une commande `docker run`.
  * Appeler le point de terminaison du conteneur, pour obtenir une réponse de HTTP 2xx et une réponse JSON en retour.

Toutes les variables entre chevrons, `<>`, doivent être remplacées par vos propres valeurs. Ce remplacement inclut les chevrons.

> [!IMPORTANT]
> Le conteneur LUIS requiert un fichier de modèle `.gz` extrait au moment du runtime. Le conteneur doit être en mesure d’accéder à ce fichier de modèle via un montage de volume à partir de l’instance de conteneur. Pour charger un fichier de modèle, procédez comme suit :
> 1. [Créez un partage de fichiers Azure](../../storage/files/storage-how-to-create-file-share.md). Notez le nom du compte de stockage Azure, la clé et le nom du partage de fichiers, car vous en aurez besoin plus tard.
> 2. [Exportez votre modèle LUIS (application empaquetée) à partir du portail LUIS](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Dans le portail Azure, accédez à la page **Vue d’ensemble** de la ressource de votre compte de stockage et sélectionnez **Partages de fichiers**. 
> 4. Sélectionnez le nom du partage de fichiers que vous avez créé récemment, puis sélectionnez **Charger**. Chargez ensuite votre application empaquetée. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Utiliser l’instance de conteneur

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Sélectionnez **Vue d’ensemble** et copiez l’adresse IP. Il s’agit d’une adresse IP numérique telle que `55.55.55.55`.
1. Ouvrez un nouvel onglet de navigateur et utilisez l’adresse IP, par exemple, `http://<IP-address>:5000 (http://55.55.55.55:5000`). La page d’accueil du conteneur s’affiche en vous informant que le conteneur est en cours d’exécution.

    ![Page d’accueil du conteneur](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Sélectionnez **Description de l’API de service** pour afficher la page swagger pour le conteneur.

1. Sélectionnez une des API **POST**, puis choisissez **Essayer**.  Les paramètres s’affichent, y compris l’entrée. Complétez les paramètres.

1. Sélectionnez **Exécuter** pour envoyer la demande à votre instance de conteneur.

    Vous êtes parvenu à créer et utiliser des conteneurs Cognitive Services dans une instance de conteneur Azure.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Si vous exécutez l’analyse de texte pour le conteneur d’intégrité, utilisez l’URL suivante pour envoyer des requêtes : `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
