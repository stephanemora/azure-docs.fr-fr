---
title: Azure Front Door – Redirection d’URL | Microsoft Docs
description: Cet article vous aide à comprendre comment Azure Front Door prend en charge la redirection d’URL pour les itinéraires, si configurée.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295471"
---
# <a name="url-redirect"></a>Redirection d’URL
Vous pouvez utiliser Azure Front Door pour rediriger le trafic. Vous pouvez rediriger le trafic à plusieurs niveaux (protocole, nom d’hôte, chemin d’accès, chaîne de requête), et toutes les fonctionnalités peuvent être configurées pour des microservices individuels, car la redirection est basée sur le chemin d’accès. Cela simplifie la configuration de l’application, optimise l’utilisation des ressources et prend en charge de nouveaux scénarios de redirection, notamment la redirection globale et basée sur le chemin d’accès.
</br>

![Redirection d’URL Azure Front Door][1]

## <a name="redirection-types"></a>Types de redirection
Un type de redirection définit le code d’état de réponse pour aider les clients à comprendre l’objectif de la redirection. Les types suivants de redirection sont pris en charge :

- **301 Déplacé de façon permanente** : Indique que la ressource cible a été affectée à un nouvel URI permanente, et que toute référence future à cette ressource doit utiliser un des URI contenus. Utilisez le code d’état 301 pour la redirection du protocole HTTP vers HTTPS. 
- **302 Trouvé** : Indique que la ressource cible réside temporairement sous un autre URI. Étant donné que la redirection peut être modifiée à l’occasion, le client doit continuer à utiliser l’URI de requête en effet pour les demandes ultérieures.
- **307 Redirection temporaire** : Indique que la ressource cible réside temporairement sous un autre URI, et que l’agent utilisateur ne doit pas modifier la méthode de requête si elle effectue une redirection automatique vers cet URI. Étant donné que la redirection peut changer au fil du temps, le client doit continuer à utiliser l’URI de requête en effet d’origine pour les requêtes ultérieures.
- **308 Redirection permanente** : Indique que la ressource cible a été affectée à un nouvel URI permanente, et que toute référence future à cette ressource doit utiliser un des URI contenus. Les clients avec des fonctionnalités de modification de liens doivent automatiquement lier à nouveau les références à l’URI de requête en effet à une ou plusieurs des nouvelles références envoyées par le serveur, lorsque cela est possible.

## <a name="redirection-protocol"></a>Protocole de redirection
Vous pouvez définir le protocole qui sera utilisé pour la redirection. Cela vous permet de procéder à une des utilisations les plus courantes de la fonctionnalité de redirection, à savoir définir la redirection HTTP vers HTTPS.

- **HTTPS uniquement** : Définissez le protocole sur HTTPS uniquement si vous cherchez à rediriger le trafic HTTP vers HTTPS. Azure Front Door recommande de toujours définir la redirection vers HTTPS uniquement.
- **HTTP uniquement** : Cela redirige la requête entrante vers HTTP. Utilisez cette valeur uniquement si vous souhaitez conserver votre trafic HTTP tel qu’il est, à savoir non chiffré.
- **Faire correspondre la requête** : Cette option conserve le protocole utilisé par la requête entrante. Par conséquent, une requête HTTP reste HTTP et une requête HTTPS reste une redirection HTTPS.

## <a name="destination-host"></a>Hôte de destination
Dans le cadre de la configuration d’un routage de redirection, vous pouvez également modifier le nom d’hôte ou le domaine pour la requête de redirection. Vous pouvez définir ce champ pour modifier le nom d’hôte dans l’URL pour la redirection, ou bien conserver le nom d’hôte de la demande entrante. Ainsi, avec ce champ, vous pouvez rediriger toutes les requêtes envoyées sur `https://www.contoso.com/*` vers `https://www.fabrikam.com/*`.

## <a name="destination-path"></a>Chemin de destination
Pour les cas où vous souhaiteriez remplacer le segment de chemin d’accès d’une URL dans le cadre de la redirection, vous pouvez définir ce champ avec la nouvelle valeur de chemin d’accès. Sinon, vous pouvez choisir de conserver la valeur de chemin d’accès dans le cadre de la redirection. Ainsi, avec ce champ, vous pouvez rediriger toutes les requêtes envoyées à `https://www.contoso.com/\*` vers `https://www.contoso.com/redirected-site`.

## <a name="query-string-parameters"></a>Paramètres de chaîne de requête
Vous pouvez également remplacer les paramètres de chaîne de requête dans l’URL redirigée. Afin de remplacer n’importe quelle chaîne de requête existante à partir de l’URL de requête entrante, définissez ce champ sur 'Remplacer' et définissez la valeur appropriée. Sinon, vous pouvez conserver le jeu initial de chaînes de requête en définissant le champ sur 'Conserver'. Par exemple, à l’aide de ce champ, vous pouvez rediriger tout le trafic envoyé à `https://www.contoso.com/foo/bar` vers `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`. 

## <a name="destination-fragment"></a>Fragment de destination
Le fragment de destination est la partie de l’URL qui figure après « # », normalement utilisée par les navigateurs pour accéder à une section spécifique d’une page. Vous pouvez définir ce champ pour ajouter un fragment à l’URL de redirection.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png