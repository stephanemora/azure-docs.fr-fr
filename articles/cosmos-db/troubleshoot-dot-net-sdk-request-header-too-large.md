---
title: Résoudre les problèmes d’en-tête de demande trop volumineux ou de requête incorrecte 400 dans Azure Cosmos DB
description: Comment diagnostiquer et corriger une exception d’en-tête de demande trop volumineux
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293753"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnostiquer et résoudre un message d’en-tête de demande trop volumineux dans Azure Cosmos DB
Le message d’en-tête de demande trop volumineux est généré avec un code d’erreur HTTP 400. Cette erreur se produit si la taille de l’en-tête de demande est devenue trop importante et dépasse la taille maximale autorisée. Nous vous recommandons d’utiliser la dernière version du Kit de développement logiciel (SDK). Veillez à utiliser au moins la version 3.x ou 2.x, car ces versions ajoutent un suivi de la taille d’en-tête au message d’exception.

## <a name="troubleshooting-steps"></a>Étapes de dépannage
Le message d’en-tête de demande trop volumineux apparaît si le jeton de session ou de continuation est trop volumineux. Les sections suivantes décrivent la cause et la solution du problème dans chaque catégorie.

### <a name="1-session-token-too-large"></a>1. Jeton de session trop volumineux

#### <a name="cause"></a>Cause :
la demande incorrecte 400 est probablement due au fait que le jeton de session est trop volumineux. Si les instructions suivantes sont vraies, cela confirme que le jeton de session est trop volumineux.

* L’erreur se produit lors d’une opération ponctuelle, par exemple, de création, de lecture, de mise à jour, etc., quand il n’y a pas de jeton de continuation.
* L’exception a démarré sans apporter de modification à l’application. Le jeton de session croît à mesure que le nombre de partitions dans le conteneur augmente. Les nombres de partitions augmentent à mesure que croît la quantité de données ou le débit.

#### <a name="temporary-mitigation"></a>Atténuation temporaire : 
Redémarrez votre application cliente pour réinitialiser tous les jetons de session. Le jeton de session reviendra finalement à la taille à l’origine du problème. Utilisez donc la solution dans la section suivante pour éviter complètement ce problème.

#### <a name="solution"></a>Solution :
1. suivez les instructions de l’article de conseils en matière de performances [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) ou [.NET V2](performance-tips.md), et convertissez l’application pour utiliser le mode de connexion directe avec le protocole TCP. Le mode direct avec le protocole TCP n’a pas de restriction de taille d’en-tête comme le protocole HTTP, ce qui évite ce problème. Veillez à utiliser la dernière version du Kit de développement logiciel (SDK), qui contient un correctif pour les opérations de requête lorsque l’interopérabilité du service n’est pas disponible.
2. Si le mode de connexion directe avec le protocole TCP n’est pas une option pour votre charge de travail, atténuez le problème en modifiant le [niveau de cohérence du client](how-to-manage-consistency.md). Le jeton de session est utilisé uniquement pour la cohérence de session, ce qui est le niveau de cohérence par défaut pour Azure Cosmos DB. D’autres niveaux de cohérence n’utilisent pas le jeton de session.

### <a name="2-continuation-token-too-large"></a>2. Jeton de continuation trop volumineux

#### <a name="cause"></a>Cause :
la demande incorrecte 400 se produit sur des opérations de requête où le jeton de continuation est utilisé. Si le jeton de continuation est devenu trop volumineux ou si des requêtes différentes ont des tailles de jeton de continuation différentes.
    
#### <a name="solution"></a>Solution :
1. suivez les instructions de l’article de conseils en matière de performances [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) ou [.NET V2](performance-tips.md), et convertissez l’application pour utiliser le mode de connexion directe avec le protocole TCP. Le mode direct avec le protocole TCP n’a pas de restriction de taille d’en-tête comme le protocole HTTP, ce qui évite ce problème. 
3. Si le mode de connexion directe avec le protocole TCP n’est pas une option pour votre charge de travail, essayez de définir l’option `ResponseContinuationTokenLimitInKb`. Vous pouvez trouver cette option dans `FeedOptions` pour la version v2 ou dans `QueryRequestOptions` pour la version v3.

## <a name="next-steps"></a>Étapes suivantes
* [Diagnostiquer et résoudre](troubleshoot-dot-net-sdk.md) les problèmes lors de l’utilisation du SDK .NET Azure Cosmos DB
* Découvrez les recommandations relatives aux performances pour [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) et [.NET V2](performance-tips.md)
