---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710958"
---
| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **GeoType** | Transaction du cluster principal ou secondaire. Les valeurs disponibles incluent **Principal** et **Secondaire**. S’applique au stockage Géo-redondant avec accès en lecture (RA-GRS) lors de la lecture d’objets à partir du locataire secondaire. |
| **ResponseType** | Type de réponse de transaction. Les valeurs disponibles incluent : <br/><br/> <li>**ServerOtherError**: toutes les autres erreurs côté serveur sauf celles décrites </li> <li>**ServerBusyError**: requête authentifiée qui a renvoyé un code d’état HTTP 503. </li> <li>**ServerTimeoutError**: requête authentifiée et arrivée à expiration, qui a renvoyé un code d’état HTTP 500. Le délai d’expiration s’est produit en raison d’une erreur serveur. </li> <li>**AuthorizationError**: requête authentifiée qui a échoué en raison d’un accès aux données non autorisé ou d’un échec d’autorisation. </li> <li>**NetworkError**: requête authentifiée qui a échoué en raison d’erreurs réseau. Se produit généralement lorsqu’un client ferme une connexion avant la fin du délai d’expiration. </li><li>**ClientAccountBandwidthThrottlingError** : Une limitation de la bande passante est appliquée à la requête pour dépassement des [limites de scalabilité du compte de stockage](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError** : Une limitation du taux de requêtes est appliquée à la requête pour dépassement des [limites de scalabilité du compte de stockage](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: Autre erreur de limitation côté client. ClientAccountBandwidthThrottlingError et ClientAccountRequestThrottlingError sont exclus.</li> <li>**ClientTimeoutError**: requête authentifiée et arrivée à expiration, qui a renvoyé un code d’état HTTP 500. Si le délai d’expiration réseau du client ou le délai d’expiration de la requête est défini sur une valeur inférieure à ce qui est attendu par le service de stockage, il s’agit d’un délai d’expiration attendu. Sinon, il est signalé comme une erreur ServerTimeoutError. </li> <li>**ClientOtherError**: toutes les autres erreurs côté client sauf celles décrites. </li> <li>**Réussite** : requête réussie</li> <li> **SuccessWithThrottling** : Demande réussie lorsqu’un client SMB est ralenti lors des premières tentatives, mais qu’il réussit lors des suivantes.</li> |
| **ApiName** | Nom de l’opération. 
| **Authentification** | Type d’authentification utilisé dans les transactions. Les valeurs disponibles incluent : <br/> <li>**AccountKey** : la transaction est authentifiée avec la clé du compte de stockage.</li> <li>**SAS** : la transaction est authentifiée avec des signatures d’accès partagé.</li> <li>**OAuth** : la transaction est authentifiée avec des jetons d’accès OAuth.</li> <li>**Anonymous** : la transaction est demandée anonymement. Elle n’inclut pas les demandes préalables.</li> <li>**AnonymousPreflight** : la transaction est une requête préalable.</li> |