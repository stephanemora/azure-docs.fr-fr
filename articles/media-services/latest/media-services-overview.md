---
title: Vue d’ensemble d’Azure Media Services v3 | Microsoft Docs
description: Cet article fournit une vue d’ensemble générale de Media Services, ainsi que des liens vers des articles contenant plus d’informations.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, flux, diffusion, en direct, hors connexion
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 489801852202163ef40d57da0082e39793196d85
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264092"
---
# <a name="what-is-azure-media-services-v3"></a>Présentation d’Azure Media Services v3

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 2 - Disponibilité générale](../previous/media-services-overview.md)
> * [Version 3 - Préversion](media-services-overview.md)

> [!NOTE]
> La version la plus récente d’Azure Media Services est disponible en préversion et peut être appelée v3.

Azure Media Services est une plateforme sur le cloud vous permettant de créer des solutions qui assurent une diffusion de vidéo en continu de qualité, améliorent l’accessibilité et la distribution, analysent le contenu et bien plus encore. Que vous soyez un développeur d’applications, un centre d’appels, une agence gouvernementale, une société de divertissement, Media Services vous aide à créer des applications proposant des expériences multimédias de qualité exceptionnelle à un large public sur les navigateurs et les appareils les plus populaires actuellement. 

## <a name="what-can-i-do-with-media-services"></a>Que puis-je faire avec Media Services ?

Media Services vous permet de créer une variété de workflows multimédias dans le cloud. Vous trouverez ci-dessous quelques exemples de ce qui peut être accompli avec Media Services.  

* Fournir des vidéos dans différents formats afin qu’elles puissent être lues sur un large éventail de navigateurs et d’appareils. Pour le streaming en direct et à la demande sur différents clients (appareils mobiles, télévisions, PC, etc.), le contenu vidéo et audio doit être codé et empaqueté de façon appropriée. Pour savoir comment livrer et diffuser ce type de contenu, consultez [Démarrage rapide : encoder et diffuser des fichiers](stream-files-dotnet-quickstart.md).
* Diffuser des événements sportifs en direct à un grand public en ligne, comme le football, le base-ball, le sport universitaire et bien plus encore. 
* Diffuser des réunions et des événements publics, comme des réunions de conseil municipal et de corps législatifs.
* Analyser les vidéos ou le contenu audio enregistrés. Par exemple, pour atteindre une satisfaction client optimale, les organisations peuvent extraire une reconnaissance vocale, et générer des index de recherche et des tableaux de bord. Elles peuvent ensuite extraire des informations sur les réclamations, les causes des réclamations et autres données pertinentes. 
* Créer un abonnement au service vidéo et diffuser du contenu DRM protégé lorsqu’un client (par exemple, un studio de cinéma) doit limiter l’accès et l’utilisation de l’œuvre protégée par copyright propriétaire.
* Proposer du contenu hors connexion dans des avions, des trains et des voitures. Un client peut avoir besoin de télécharger du contenu sur son téléphone ou sa tablette pour pouvoir le lire lorsqu’il sera déconnecté du réseau.
* Ajouter des sous-titres et des légendes à des vidéos pour répondre aux besoins d’un plus large public (par exemple, des personnes souffrant d’un handicap d’audition ou souhaitant lire simultanément dans une autre langue). 
* Implémenter une plateforme vidéo de formation en ligne éducative avec Azure Media Services et les [API Azure Cognitive Services](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) pour le sous-titrage de la reconnaissance vocale, la traduction en plusieurs langues, etc.
* Activer Azure CDN pour une mise à grande échelle afin d’améliorer la gestion instantanée des charges importantes (par exemple le début de l’événement de lancement d’un produit). 

## <a name="v3-capabilities"></a>Fonctionnalités de v3

v3 est basée sur une surface d’API unifiée qui expose des fonctionnalités de gestion et de fonctionnement qui s’appuient sur Azure Resource Manager. 

Cette version offre les fonctionnalités suivantes :  

* **Transformations** : définir des workflows simples des tâches d’analytique ou de traitement multimédia. La transformation est une formule pour le traitement de vos fichiers audio et vidéo. Vous pouvez ensuite l’appliquer à plusieurs reprises pour traiter tous les fichiers dans votre bibliothèque de contenu, en envoyant des travaux à la Transformation.
* **Travaux** : traiter (encoder ou analyser) vos vidéos. Un contenu d’entrée peut être spécifié sur un travail à l’aide d’URL HTTP(s), d’URL SAP ou de chemins d’accès aux fichiers situés dans le stockage Blob Azure. 
* **Notifications** : surveiller la progression ou les états du travail, le démarrage/l’arrêt de Live Channel et les événements d’erreur. Les notifications sont intégrées au système de notification Azure Event Grid. Vous pouvez facilement vous abonner à des événements sur plusieurs ressources dans Azure Media Services. 
* Vous pouvez utiliser les gabarits **Azure Resource Management** pour créer et déployer des transformations, des points de terminaison de streaming, des canaux et bien plus encore.
* Il est possible de configurer le **contrôle d’accès en fonction du rôle** au niveau de la ressource, ce qui vous permet de bloquer l’accès à des ressources spécifiques comme les transformations, les canaux et bien plus encore.
* **Kits SDK clients** en plusieurs langages : .NET, .NET core, Python, Go, Java et Node.js.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Les noms de ressources Azure Media Services v3 (par exemple Assets, Jobs, Transforms) sont sujets à des restrictions d’appellation par Azure Resource Manager. Conformément à Azure Resource Manager, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour les noms de vos ressources. 

Les noms de ressources Media Services ne peuvent pas contenir : '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', le caractère de citation unique ou tout caractère de commande. Tous les autres caractères sont autorisés. La longueur maximale d’un nom de ressources est de 260 caractères. 

Pour en savoir plus sur l’affectation de noms avec Azure Resource Manager, consultez : [Exigences d’affectation des noms](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) et [Convention d’affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="how-can-i-get-started-with-v3"></a>Comment bien démarrer avec v3 ?

En tant que développeur, vous pouvez utiliser [l’API REST](https://go.microsoft.com/fwlink/p/?linkid=873030) ou les bibliothèques clientes de Media Services qui vous permettent d’interagir avec l’API REST, de créer, gérer et mettre à jour facilement les workflows multimédias personnalisés. Vous pouvez trouver un exemple REST Postman [à cette adresse](https://github.com/Azure-Samples/media-services-v3-rest-postman). Vous pouvez également utiliser une [API REST Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates).

Microsoft génère et prend en charge les bibliothèques clientes suivantes : 

|Bibliothèque cliente|Exemples|
|---|---|
|[Kit de développement logiciel (SDK) Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Exemples d’interface de ligne de commande Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[Exemples relatifs à .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[Kit de développement logiciel (SDK) .NET Core](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (choisissez l’onglet **.NET CLI**)|[Exemples .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Kit de développement logiciel (SDK) Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[Kit de développement logiciel (SDK) Node.js](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Exemples relatifs à Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[Kit de développement logiciel (SDK) Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[Kit de développement logiciel (SDK) Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

Media Services fournit des [fichiers Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) que vous pouvez utiliser pour générer des kits SDK pour votre langage ou technologie préféré(e).  

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir combien il est facile de démarrer le codage et le streaming de fichiers vidéo, consultez [Diffuser des fichiers](stream-files-dotnet-quickstart.md). 

