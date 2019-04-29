---
title: Personnaliser un modèle de personne dans Video Indexer - Azure
titlesuffix: Azure Media Services
description: Cet article présente les modèles de personne dans Video Indexer et explique comment le personnaliser.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553723"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personnaliser un modèle de personne dans Video Indexer

Video Indexer prend en charge la reconnaissance de célébrités dans vos vidéos. La fonctionnalité de reconnaissance de célébrités couvre environ un million de visages en fonction de la source de données couramment demandée comme IMDB, Wikipedia et les principaux influenceurs LinkedIn. Les visages qui ne sont pas reconnus par Video Indexer sont toujours détectés mais sont laissés sans nom. Les clients peuvent créer des modèles de personne personnalisés et activer Video Indexer à identifier des visages qui ne sont pas reconnues par défaut. Les clients peuvent créer ces modèles de la personne en associant le nom d’une personne contenant les fichiers d’image du visage de la personne.  

Si votre compte destinée aux cas d’utilisation différents, vous pouvez bénéficier de la possibilité de créer plusieurs modèles de personne par compte. Par exemple, si le contenu de votre compte est destiné à être triées dans différents canaux, vous souhaiterez créer un modèle de personne distinct pour chaque canal. 

> [!NOTE]
> Chaque modèle de personne prend en charge jusqu'à 1 million de personnes, et chaque compte a une limite de 50 modèles de personne. 

Une fois un modèle créé, vous pouvez l’utiliser en fournissant l’ID d’un modèle de personne spécifique lors du chargement /de l’indexation ou de la réindexation d’une vidéo. Un nouveau visage pour obtenir une vidéo de formation, des mises à jour votre modèle personnalisé qui a été associée à la vidéo. 

Si vous n’avez pas besoin de la prise en charge de plusieurs modèles de personne, n’affectez aucun ID de modèle de personne à votre vidéo lors de son chargement/de son indexation ou de sa réindexation. Dans ce cas, Video Indexer utilisera le modèle de personne par défaut dans votre compte. 

Vous pouvez utiliser le site Web de Video Indexer pour modifier les visages qui ont été détectées dans une vidéo et pour gérer plusieurs modèles de personne personnalisés dans votre compte, comme décrit dans la [personnaliser un modèle de personne à l’aide d’un site Web](customize-person-model-with-website.md) rubrique. Vous pouvez également utiliser l’API, comme décrit dans [personnaliser un modèle de personne à l’aide des API](customize-person-model-with-api.md).