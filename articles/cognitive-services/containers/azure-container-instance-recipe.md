---
title: Recette d’instance de conteneur Azure
titleSuffix: Azure Cognitive Services
description: Apprendre à déployer des conteneurs Cognitive Services dans une instance de conteneur Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 288894705e1108d6dd511b60cd2bc3bcee4c6d41
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704344"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Déployer et exécuter un conteneur sur Azure Container Instances

Avec les étapes suivantes, mettez facilement à l’échelle des applications Azure Cognitive Services dans le cloud avec Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Plutôt que de gérer l’infrastructure, consacrez-vous pleinement à la création de votre application.

## <a name="prerequisites"></a>Prérequis

Cette solution fonctionne avec n’importe quel conteneur Cognitive Services. La ressource de service cognitif doit être créée sur le portail Azure avant l’utilisation de cette recette. Chaque service cognitif qui prend en charge les conteneurs propose un document d’aide qui vous explique comment installer et configurer le service pour un conteneur. Certains services nécessitent un fichier ou un ensemble de fichiers comme entrée du conteneur. Avant d’utiliser cette solution, il est donc important de comprendre le conteneur et de l’avoir déjà utilisé avec succès.

* Une ressource de service cognitif, créée sur le portail Azure.
* Une **URL de point de terminaison** de service cognitif : examinez la procédure d’installation du conteneur propre à votre service de façon à trouver l’emplacement de l’URL du point de terminaison sur le portail Azure ainsi qu’un exemple approprié de présentation de l’URL. Le format exact peut changer d’un service à l’autre.
* **Clé** du service cognitif : les clés se trouvent dans la page **Clés** de la ressource Azure. Vous avez seulement besoin d’une des deux clés. La clé est une chaîne de 32 caractères alphanumériques.
* Un seul conteneur Cognitive Services sur votre hôte local (votre ordinateur). Vérifiez que vous pouvez :
  * Extraire l’image à l’aide d’une commande `docker pull`.
  * Exécuter correctement le conteneur local avec tous les paramètres de configuration nécessaires à l’aide d’une commande `docker run`.
  * Appeler le point de terminaison du conteneur, en obtenant une réponse 2xx et une réponse JSON en retour.

Toutes les variables entre chevrons, `<>`, doivent être remplacées par vos propres valeurs. Ce remplacement inclut les chevrons.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Utiliser l’instance de conteneur

1. Sélectionnez **Vue d’ensemble** et copiez l’adresse IP. Il s’agit d’une adresse IP numérique telle que `55.55.55.55`.
1. Ouvrez un nouvel onglet de navigateur et utilisez l’adresse IP, par exemple, `http://<IP-address>:5000 (http://55.55.55.55:5000`). La page d’accueil du conteneur s’affiche en vous informant que le conteneur est en cours d’exécution.

1. Sélectionnez **Description de l’API de service** pour afficher la page swagger pour le conteneur.

1. Sélectionnez une des API **POST**, puis choisissez **Essayer**.  Les paramètres s’affichent, y compris l’entrée. Complétez les paramètres.

1. Sélectionnez **Exécuter** pour envoyer la demande à votre instance de conteneur.

    Vous êtes parvenu à créer et utiliser des conteneurs Cognitive Services dans une instance de conteneur Azure.
