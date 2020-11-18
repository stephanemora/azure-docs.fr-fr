---
title: Modèle d’identité
titleSuffix: An Azure Communication Services concept
description: Découvrir les identités et les jetons d’accès
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 996f138a14923319381738e7a55cd7ba4e8c4320
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517765"
---
# <a name="identity-model"></a>Modèle d’identité

Azure Communication Services est un service indépendant de l’identité. Cette conception présente plusieurs avantages :
- Réutilisation des identités existantes à partir de votre système d’administration des identités
- Flexibilité pour les scénarios d’intégration
- Maintien du caractère privé de vos identités dans Azure Communication Services

Au lieu de dupliquer les informations existantes dans votre système, vous conservez la relation de mappage qui est propre à votre cas métier. Par exemple, mappage des identités 1:1, 1:N, N:1, N:M. Les identificateurs externes (tels que les numéros de téléphone, les utilisateurs, les appareils, les applications, le GUID) ne peuvent pas être utilisés en tant qu’identité Azure Communication. Les jetons d’accès générés pour l’identité Azure Communication Services sont utilisés pour accéder aux primitives telles que la conversation ou l’appel. 

## <a name="identity"></a>Identité

Les identités sont créées avec la bibliothèque d’administration Azure Communication Services. L’identité sert d’identificateur dans les conversations et est utilisée pour la création de jetons d’accès. La même identité peut participer à plusieurs sessions simultanées sur plusieurs appareils. L’identité peut avoir plusieurs jetons d’accès actifs en même temps. La suppression de l’identité, de la ressource ou de l’abonnement entraîne l’invalidation de tous ses jetons d’accès et la suppression de toutes les données stockées pour cette identité. L’identité supprimée ne peut pas émettre de nouveaux jetons d’accès, ni accéder aux données stockées (par exemple, les messages de conversation). 

Vous n’êtes pas facturé en fonction du nombre d’identités que vous avez, mais de l’utilisation des primitives. Le nombre d’identités ne doit pas nécessairement limiter la façon dont vous mappez les identités de votre application aux identités Azure Communication Services. La liberté de mappage va de pair avec la responsabilité en termes de confidentialité. Quand l’utilisateur de votre application veut être supprimé de votre système, vous devez supprimer toutes les identités qui ont été associées à cet utilisateur.

Azure Communication Services ne fournit pas d’identités spéciales pour les utilisateurs anonymes. Il ne conserve pas le mappage entre les utilisateurs et les identités et ne peut pas déterminer si l’identité est anonyme. Vous pouvez concevoir le concept en fonction de vos besoins. Nous vous recommandons de créer une identité pour chaque utilisateur anonyme de l’application. En possession du jeton d’accès valide, tout le monde peut accéder au contenu non supprimé d’une identité. Par exemple, les messages de conversation envoyés par l’utilisateur. L’accès n’est limité qu’aux étendues qui font partie du jeton d’accès. Pour plus d’informations sur les étendues, consultez la section *Jeton d’accès*.

### <a name="mapping-of-identities"></a>Mappage des identités

Azure Communication Services ne réplique pas les fonctionnalités du système d’administration des identités. Il ne permet pas aux clients d’utiliser des identités qui leur sont propres. Par exemple, numéro de téléphone ou adresse e-mail. Au lieu de cela, il fournit des identificateurs uniques que vous pouvez assigner aux identités de votre application. Azure Communication Services ne stocke aucun type d’informations susceptibles de révéler l’identité réelle de vos utilisateurs.

Au lieu d’envisager une duplication, vous êtes encouragé à concevoir la façon dont les utilisateurs de votre domaine d’identité seront mappés aux identités Azure Communication Services. Vous pouvez suivre tout type de modèle 1:1, 1:N, N:1 ou M:N. Vous pouvez décider si un utilisateur donné est mappé à une ou à plusieurs identités. Quand une identité est créée, nous vous recommandons de stocker le mappage de cette identité à l’utilisateur ou aux utilisateurs de votre application. Comme les identités nécessitent les jetons d’accès pour l’utilisation des primitives, l’identité doit être connue pour l’utilisateur ou les utilisateurs de votre application.

Si vous utilisez une base de données relationnelle pour le stockage des utilisateurs, l’implémentation peut varier en fonction de votre scénario de mappage. Pour les scénarios avec mappage 1:1 ou N:1, vous pouvez ajouter une colonne *CommunicationServicesId* à la table pour stocker votre identité Azure Communication Services. Dans les scénarios avec une relation 1:N ou N:M, vous pouvez envisager de créer une table distincte dans la base de données relationnelle.

## <a name="access-token"></a>Access token (Jeton d’accès)

Le jeton d’accès est un jeton JWT qui peut être utilisé pour obtenir l’accès aux primitives Azure Communication Services. Le jeton d’accès émis dispose d’une protection de l’intégrité ; ses revendications ne peuvent pas être changées après l’émission. Autrement dit, la modification manuelle des propriétés telles que l’identité, l’expiration ou les étendues rend le jeton d’accès non valide. L’utilisation de primitives avec des jetons invalidés entraîne un refus de l’accès à la primitive. 

Les propriétés du jeton d’accès sont les suivantes : *identité, expiration* et *étendues*. Le jeton d’accès est toujours valide pendant 24 heures. Après ce délai, le jeton d’accès n’est plus valide et ne peut pas être utilisé pour accéder à une primitive. L’identité doit avoir un moyen de demander un nouveau jeton d’accès au service côté serveur. Le paramètre d’*étendue* définit un ensemble non vide de primitives qui peut être utilisées. Azure Communication Services prend en charge les étendues suivantes pour les jetons d’accès :

|Nom|Description|
|---|---|
|Conversation|  Permet de participer à une conversation|
|VoIP|  Octroie la possibilité d’appeler des identités et des numéros de téléphone|


Si vous souhaitez révoquer le jeton d’accès avant son expiration, vous pouvez utiliser la bibliothèque d’administration Azure Communication Services pour ce faire. La révocation du jeton n’est pas immédiate ; sa propagation prend jusqu’à 15 minutes. La suppression de l’identité, de la ressource ou de l’abonnement entraîne la révocation de tous les jetons d’accès. Si vous souhaitez retirer à un utilisateur la possibilité d’accéder à des fonctionnalités spécifiques, révoquez tous les jetons d’accès. Émettez ensuite un nouveau jeton d’accès avec un ensemble d’étendues plus limité.
La permutation des clés d’accès d’Azure Communication Services entraîne la révocation de tous les jetons d’accès actifs qui ont été créés avec la clé d’accès précédente. Toutes les identités perdent l’accès à Azure Communication Services et doivent émettre de nouveaux jetons d’accès. 

Nous vous recommandons d’émettre les jetons d’accès dans votre service côté serveur, et non dans l’application du client. En effet, l’émission nécessite une clé d’accès ou doit faire appel à une identité managée. Pour des raisons de sécurité, nous vous déconseillons de partager les clés d’accès avec l’application du client. L’application cliente doit utiliser un point de terminaison de service approuvé qui peut authentifier vos clients et émettre un jeton d’accès en leur nom. Vous trouverez plus d’informations sur l’architecture [ici](./client-and-server-architecture.md).

Si vous mettez en cache les jetons d’accès dans un magasin de stockage, nous vous recommandons d’utiliser le chiffrement. Le jeton d’accès étant constitué de données sensibles, il peut être utilisé pour des activités malveillantes s’il n’est pas protégé. En possession du jeton d’accès, vous pouvez initialiser le SDK et accéder à l’API. L’API accessible est limitée uniquement en fonction des étendues dont dispose le jeton d’accès. Nous vous recommandons de n’émettre des jetons d’accès qu’avec les étendues qui sont nécessaires.

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation de la gestion des jetons d’accès, consultez [Créer et gérer des jetons d’accès](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
* Pour une présentation de l’authentification, consultez [S’authentifier auprès d’Azure Communication Services](https://docs.microsoft.com/azure/communication-services/concepts/authentication).
* Pour une présentation de la confidentialité et de la résidence des données, consultez [Disponibilité des régions et résidence des données](https://docs.microsoft.com/azure/communication-services/concepts/privacy).