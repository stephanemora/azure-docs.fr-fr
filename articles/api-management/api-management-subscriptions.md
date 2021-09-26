---
title: Abonnements dans Gestion des API Azure | Microsoft Docs
description: Découvrez le concept des abonnements dans la Gestion des API Azure. Les consommateurs bénéficient d’un accès aux API à l’aide d’abonnements dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/27/2021
ms.author: danlep
ms.openlocfilehash: acf1db006dac4dd67937bf1d0788a219b6c482e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637928"
---
# <a name="subscriptions-in-azure-api-management"></a>Abonnements dans Gestion des API Azure

Dans Azure API Management, les abonnements sont la manière la plus courante pour des consommateurs d’API d’accéder aux API publiées via une instance de Gestion des API. Cet article fournit une vue d’ensemble du concept.

## <a name="what-are-subscriptions"></a>Qu’est-ce qu’un abonnement ?

En publiant des API via Gestion des API, vous pouvez facilement sécuriser l’accès aux API à l’aide de clés d’abonnement. Consommez les API publiées en incluant une clé d’abonnement valide dans les requêtes HTTP lors de l’appel de ces API. Si vous ne disposez pas d’une clé d’abonnement valide, les appels :
* Sont immédiatement rejetés par la passerelle Gestion des API. 
* Ne sont pas transférés aux services principaux.

Pour accéder aux API, vous devez disposer d’un abonnement et d’une clé d’abonnement. Un *abonnement* est un conteneur nommé pour une paire de clés d’abonnement. 

La régénération régulière des clés étant une précaution de sécurité courante, la plupart des produits Azure nécessitant une clé d’abonnement génèrent des clés par paires. Chaque application qui utilise le service peut passer de la *clé A* à la *clé B*, et régénérer la clé A avec une interruption minimale, et vice versa. 

De plus :

* Les développeurs peuvent obtenir des abonnements sans l’approbation des éditeurs d’API. 
* Les éditeurs d’API peuvent également créer des abonnements directement pour les consommateurs d’API.

> [!TIP]
> La Gestion des API prend également en charge d’autres mécanismes de protection de l’accès aux API, entre autres :
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Certificats clients](api-management-howto-mutual-certificates-for-clients.md)
> - [Restrict caller IPs](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Étendue des abonnements

Les abonnements peuvent être associés à différentes étendues : un produit, toutes les API ou une API individuelle.

### <a name="subscriptions-for-a-product"></a>Abonnements pour un produit

Habituellement, les abonnements dans Gestion des API sont associés à une seule étendue de [produit d’API](api-management-terminology.md). Développeurs :
* Ont trouvé la liste des produits sur le portail des développeurs. 
* Ont soumis des demandes d’abonnement pour les produits qu’ils souhaitent utiliser. 
* Utilisent les clés de ces abonnements (approuvées automatiquement ou par des serveurs de publication d’API) pour accéder à toutes les API dans le produit. 
    * Vous pouvez accéder aux API avec ou sans la clé d’abonnement, quelle que soit l’étendue de l’abonnement (produit, global ou API).

Actuellement, le portail des développeurs affiche uniquement les abonnements dans l’étendue du produit dans la section **Profil utilisateur**. 

> [!NOTE]
> Si vous utilisez une clé d’abonnement d’étendue API, les *stratégies* configurées dans l’étendue du produit ne sont pas appliquées à cet abonnement.

![Abonnements de produit](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Dans certains scénarios, les éditeurs d’API peuvent parfois souhaiter publier un produit d’API à destination du public, sans passer par les abonnements. Ils peuvent pour cela décocher l’option **Exiger un abonnement** dans la page **Paramètres** du produit sur le portail Azure. De cette façon, toutes les API du produit sont accessibles sans clé API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Abonnements pour toutes les API ou une API individuelle

Avec l’ajout du niveau [Consommation](https://aka.ms/apimconsumptionblog) de Gestion des API, la gestion des clés d’abonnement est simplifiée. 

#### <a name="two-more-subscription-scopes"></a>Deux autres étendues d’abonnement

À présent que les étendues d’abonnement ne sont plus limitées à un produit d’API, vous pouvez créer des clés qui accordent l’accès à :
* une seule API, ou 
* toutes les API dans une instance de Gestion des API. 

Vous n’avez pas besoin de créer un produit avant d’y ajouter des API. 

De plus, chaque instance de Gestion des API intègre désormais un abonnement immuable à toutes les API. Cet abonnement simplifie et facilite les tests et le débogage des API dans la console de test.

#### <a name="standalone-subscriptions"></a>Abonnements autonomes

La Gestion des API autorise désormais les abonnements *autonomes*. Vous n’avez plus besoin d’associer des abonnements à un compte de développeur. Cette fonctionnalité s’avère utile dans des scénarios tels que ceux où plusieurs développeurs ou équipes partagent un abonnement.

La création d’un abonnement sans attribution de propriétaire en fait un abonnement autonome. Pour permettre aux développeurs et au reste de votre équipe d’accéder à la clé d’abonnement autonome, vous pouvez :
* Partager manuellement la clé d’abonnement.
* Utiliser un système personnalisé pour mettre la clé d’abonnement à la disposition de votre équipe.

#### <a name="creating-subscriptions-in-azure-portal"></a>Création d’abonnements dans le portail Azure

Les éditeurs d’API peuvent désormais [créer des abonnements](api-management-howto-create-subscriptions.md) directement dans le portail Azure :

![Abonnements flexibles](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Étapes suivantes
Sachez-en plus sur la Gestion des API :

+ Découvrez les autres [concepts](api-management-terminology.md) de la Gestion des API.
+ Suivez nos [tutoriels](import-and-publish.md) sur la Gestion des API.
+ Consultez notre [page de FAQ](api-management-faq.yml) pour prendre connaissance des questions courantes.
