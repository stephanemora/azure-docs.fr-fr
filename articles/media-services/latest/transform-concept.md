---
title: Transformations et travaux dans Azure Media Services | Microsoft Docs
description: Lorsque vous utilisez Media Services, vous devez créer une transformation pour décrire les règles ou les spécifications de traitement de vos vidéos. Cet article explique ce qu’est une transformation et comment l’utiliser.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Transformations et travaux

## <a name="overview"></a>Vue d'ensemble 

La dernière version de l’API REST Azure Media Services (v3) introduit une nouvelle ressource de workflow basée sur un modèle pour le codage et/ou l’analyse des vidéos, appelée **transformation**. Les **transformations** peuvent être utilisées pour configurer les tâches courantes de codage ou d’analyse des vidéos. Chaque **transformation** décrit un simple workflow de tâches pour le traitement de vos fichiers vidéo ou audio. 

L’objet **Transformation** est la formule et un **travail** est la requête réelle envoyée à Azure Media Services pour appliquer cette **transformation** à un contenu vidéo ou audio d’entrée donné. Le **travail** spécifie des informations telles que l’emplacement de la vidéo d’entrée et celui de la sortie. Vous pouvez spécifier l’emplacement de votre vidéo à l’aide d’adresse(s) HTTP, d’URL, d’URL SAP ou d’un chemin d’accès à des fichiers résidant en local ou dans Azure Blob Storage. Vous pouvez avoir jusqu’à 100 transformations dans votre compte Azure Media Services et utiliser ces transformations pour envoyer des travaux. Vous pouvez ensuite vous abonner à des événements (par exemple, changements d’état du travail) en utilisant les Notifications qui s’intègrent directement au système de notifications d’Azure Event Grid. 

Puisque cette API est pilotée par Azure Resource Manager, vous pouvez utiliser des modèles Resource Manager pour créer et déployer des transformations dans votre compte Media Services. Il est également possible, dans cette API, de configurer le contrôle d’accès en fonction du rôle au niveau de la ressource, ce qui vous permet de bloquer l’accès à des ressources spécifiques, notamment des transformations.

## <a name="typical-workflow-and-example"></a>Workflow et exemple types

1. Créer une transformation 
2. Envoyer des travaux sous cette transformation 
3. Répertorier les transformations 
4. Supprimer une transformation si vous ne prévoyez pas d’utiliser cette « recette » ultérieurement. 

Supposons que vous souhaitez extraire la première image de toutes vos vidéos sous la forme d’une image miniature. Vous devrez effectuer les étapes suivantes : 

1. Définir la règle de traitement, par exemple utiliser la première image de la vidéo comme miniature 
2. Puis, pour chaque vidéo que vous avez entrée dans le service, vous devez indiquer au service : 
    1. Où trouver cette vidéo 
    2. À quel emplacement écrire la sortie, par exemple, l’image miniature 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Diffuser des fichiers vidéo en continu](stream-files-dotnet-quickstart.md)
