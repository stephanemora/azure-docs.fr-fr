---
title: Vue d’ensemble d’Azure Media Services v3 | Microsoft Docs
description: Cet article fournit une vue d’ensemble générale de Media Services, ainsi que des liens vers des articles contenant plus d’informations.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, flux, diffusion, en direct, hors connexion
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 0f4fd963ce3649c901a76f6677be059ba5be25af
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337559"
---
# <a name="what-is-azure-media-services-v3"></a>Présentation d’Azure Media Services v3

Azure Media Services est une plateforme sur le cloud vous permettant de créer des solutions qui assurent une diffusion de vidéo en continu de qualité, améliorent l’accessibilité et la distribution, analysent le contenu et bien plus encore. Que vous soyez un développeur d’applications, un centre d’appels, une agence gouvernementale, une société de divertissement, Media Services vous aide à créer des applications proposant des expériences multimédias de qualité exceptionnelle à un large public sur les navigateurs et les appareils les plus populaires actuellement. 

## <a name="what-can-i-do-with-media-services"></a>Que puis-je faire avec Media Services ?

Media Services vous permet de créer une variété de workflows multimédias dans le cloud. Vous trouverez ci-dessous quelques exemples de ce qui peut être accompli avec Media Services.  

* Fournir des vidéos dans différents formats afin qu’elles puissent être lues sur un large éventail de navigateurs et d’appareils. Pour le streaming en direct et à la demande sur différents clients (appareils mobiles, télévisions, PC, etc.), le contenu vidéo et audio doit être codé et empaqueté de façon appropriée. Pour savoir comment livrer et diffuser ce type de contenu, consultez [Démarrage rapide : encoder des fichiers et les diffuser en continu](stream-files-dotnet-quickstart.md).
* Diffuser des événements sportifs en direct à un grand public en ligne, comme le football, le base-ball, le sport universitaire et bien plus encore. 
* Diffuser des réunions et des événements publics, comme des réunions de conseil municipal et de corps législatifs.
* Analyser les vidéos ou le contenu audio enregistrés. Par exemple, pour atteindre une satisfaction client optimale, les organisations peuvent extraire une reconnaissance vocale, et générer des index de recherche et des tableaux de bord. Elles peuvent ensuite extraire des informations sur les réclamations, les causes des réclamations et autres données pertinentes.
* Créer un abonnement au service vidéo et diffuser du contenu DRM protégé lorsqu’un client (par exemple, un studio de cinéma) doit limiter l’accès et l’utilisation de l’œuvre protégée par copyright propriétaire.
* Proposer du contenu hors connexion dans des avions, des trains et des voitures. Un client peut avoir besoin de télécharger du contenu sur son téléphone ou sa tablette pour pouvoir le lire lorsqu’il sera déconnecté du réseau.
* Implémenter une plateforme vidéo de formation en ligne éducative avec Azure Media Services et les [API Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) pour le sous-titrage de la reconnaissance vocale, la traduction en plusieurs langues, etc. 
* Utilisez Azure Media Services avec les [API Azure Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=ai) pour ajouter des sous-titres et des légendes aux vidéos afin de répondre aux besoins d’une audience plus large (par exemple, les personnes ayant des problèmes d’audition ou souhaitant lire simultanément dans une autre langue).
* Activez Azure CDN pour effectuer un scaling à grande échelle afin d’améliorer la gestion instantanée des charges importantes (par exemple le début d’un événement de lancement d’un produit). 

## <a name="v3-capabilities"></a>Fonctionnalités de v3

v3 est basée sur une surface d’API unifiée qui expose des fonctionnalités de gestion et de fonctionnement qui s’appuient sur Azure Resource Manager. 

Cette version offre les fonctionnalités suivantes :  

* **Transformations** : définir des workflows simples des tâches d’analytique ou de traitement multimédia. La transformation est une formule pour le traitement de vos fichiers audio et vidéo. Vous pouvez ensuite l’appliquer à plusieurs reprises pour traiter tous les fichiers dans votre bibliothèque de contenu, en envoyant des travaux à la Transformation.
* **Travaux** : traiter (encoder ou analyser) vos vidéos. Un contenu d’entrée peut être spécifié sur un travail à l’aide d’URL HTTPS, d’URL SAP ou de chemins d’accès aux fichiers situés dans le Stockage Blob Azure. AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS.
* **Notifications** : surveiller la progression ou les états du travail, le démarrage/l’arrêt de Live Channel et les événements d’erreur. Les notifications sont intégrées au système de notification Azure Event Grid. Vous pouvez facilement vous abonner à des événements sur plusieurs ressources dans Azure Media Services. 
* Vous pouvez utiliser les gabarits **Azure Resource Management** pour créer et déployer des transformations, des points de terminaison de streaming, des canaux et bien plus encore.
* Il est possible de configurer le **contrôle d’accès en fonction du rôle** au niveau de la ressource, ce qui vous permet de bloquer l’accès à des ressources spécifiques comme les transformations, les canaux et bien plus encore.
* **Kits SDK clients** en plusieurs langages : .NET, .NET core, Python, Go, Java et Node.js.

## <a name="naming-conventions"></a>Conventions d’affectation de noms

Les noms de ressources Azure Media Services v3 (par exemple Assets, Jobs, Transforms) sont sujets à des restrictions d’appellation par Azure Resource Manager. Conformément à Azure Resource Manager, les noms des ressources sont toujours uniques. Ainsi, vous pouvez utiliser n’importe quelle chaîne d’identificateur unique (par exemple des GUID) pour les noms de vos ressources. 

Les noms de ressources Media Services ne peuvent pas contenir : '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', le caractère de citation unique ou tout caractère de commande. Tous les autres caractères sont autorisés. La longueur maximale d’un nom de ressources est de 260 caractères. 

Pour plus d'informations sur l'affectation de noms dans Azure Resource Manager, consultez : [Exigences en matière d'affectation de noms](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) et [Conventions d'affectation de noms](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Principes de conception de l’API v3

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. Les API v3 ne retournent pas de secrets ou d’informations d’identification lors d’opérations **Get** ou **List**. Les clés sont toujours null, vides ou purgées de la réponse. Vous devez appeler une méthode d’action distincte pour obtenir des informations d’identification ou des secrets. Les actions distinctes vous permettent de définir différentes autorisations de sécurité RBAC dans le cas où certaines API récupèrent ou affichent des secrets tandis que d’autres non. Pour plus d’informations sur la gestion de l’accès à l’aide de RBAC, consultez [Utiliser RBAC pour gérer l’accès](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Les exemples incluent : 

* ne pas retourner des valeurs ContentKey dans le Get de l’élément StreamingLocator ; 
* ne pas retourner les clés de restriction dans le Get de la stratégie ContentKeyPolicy ; 
* ne pas retourner la partie de la chaîne de requête de l’URL (pour supprimer la signature) des URL d’entrée HTTP des tâches.

Consultez l’exemple [Obtenir une stratégie de clé de contenu - .NET](get-content-key-policy-dotnet-howto.md).

## <a name="how-can-i-get-started-with-v3"></a>Comment bien démarrer avec v3 ?

Pour plus d’informations sur la façon de commencer à développer avec l’API de Media Services v3 en utilisant différents outils et SDK, consultez [Commencer à développer](developers-guide.md).

## <a name="v3-content-map"></a>plan du contenu v3

Le contenu de Media Services v3 est organisé selon la structure suivante (ce qui est également reflété dans la table des matières) :

|Sections| Description|
|---|---|
| Vue d’ensemble | Explique les fonctionnalités de Media Services et ce que vous pouvez faire avec le service.|
| Démarrages rapides | Montre les instructions fondamentales du 1er jour pour les nouveaux clients qui testent rapidement Media Services.|
| Tutoriels | Montre les procédures de scénarios pour certaines des tâches Media Services principales.|
| Exemples | Liens vers des exemples de code. |
| Concepts | Contient une explication détaillée des fonctionnalités de Media Services v3 et des diagrammes. Il est recommandé d’examiner les concepts fondamentaux décrits dans ces rubriques avant de commencer le développement.<br/><br/>* Chargement et stockage cloud<br/>* Encodage<br/>* Analytique multimédia<br/>* Empaquetage, remise, protection<br/>* Streaming en direct<br/>* Supervision continue<br/>* Clients Player<br/><br/>et bien plus. |
| Guides pratiques | Montrent comment effectuer une tâche.|

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir combien il est facile de démarrer le codage et le streaming de fichiers vidéo, consultez [Diffuser des fichiers](stream-files-dotnet-quickstart.md). 

