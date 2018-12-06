---
title: Abonnements dans Gestion des API Azure | Microsoft Docs
description: Découvrez plus d’informations sur le concept des abonnements dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621713"
---
# <a name="subscriptions-in-azure-api-management"></a>Abonnements dans Gestion des API Azure

Les abonnements sont un concept important dans Gestion des API Azure (APIM). C’est l’approche la plus courante pour permettre aux consommateurs d’API d’obtenir l’accès aux API publiées par une instance APIM. Cet article fournit une vue d’ensemble du concept.

## <a name="what-is-subscriptions"></a>Qu’est-ce que les abonnements

Quand vous publiez des API avec APIM, la façon la plus simple et la plus courante de sécuriser l’accès à ces API est d’utiliser des clés d’abonnement. En d’autres termes, les développeurs qui ont besoin d’utiliser les API publiées doivent inclure une clé d’abonnement valide dans les requêtes HTTP lors des appels à ces API. Sinon, les appels sont immédiatement rejetés par la passerelle APIM et ne sont pas transférés aux services back-end.

Pour obtenir une clé d’abonnement afin d’accéder aux API, vous avez besoin d’un abonnement. L’abonnement est essentiellement un conteneur nommé qui contient une paire de clés d’abonnement. Les abonnements peuvent être obtenus par les développeurs qui ont besoin d’utiliser les API publiées, avec ou sans approbation des éditeurs d’API. Les éditeurs d’API peuvent également créer des abonnements directement, pour le compte des consommateurs d’API.

> [!TIP]
> APIM prend également en charge d’autres mécanismes pour sécuriser l’accès aux API, y compris [OAuth2.0](api-management-howto-protect-backend-with-aad.md), les [certificats clients](api-management-howto-mutual-certificates-for-clients.md) et la [création d’une liste verte d’adresses IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Étendue des abonnements

Les abonnements peuvent être associés à différentes étendues : un produit, toutes les API ou une API individuelle.

### <a name="subscriptions-for-a-product"></a>Abonnements pour un produit

Traditionnellement, les abonnements dans APIM étaient toujours associés à une seule étendue de [produit d’API](api-management-terminology.md). Les développeurs trouvaient la liste des produits sur le portail des développeurs et envoyaient des demandes d’abonnement aux produits qu’ils voulaient utiliser. Une fois qu’une demande d’abonnement est approuvée (automatiquement ou par les éditeurs d’API), le développeur peut utiliser les clés qu’il contient pour accéder à toutes les API du produit.

![Abonnements de produit](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Dans certains scénarios, les éditeurs d’API veulent publier un produit d’API pour le public sans passer par les abonnements. Ils peuvent décocher l’option **Exiger un abonnement** dans la page **Paramètres** du produit dans le portail Azure. De cette façon, toutes les API du produit sont accessibles sans clé API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Abonnements pour toutes les API ou une API individuelle

> [!NOTE]
> Actuellement, cette fonctionnalité est disponible uniquement dans le niveau Consommation de Gestion des API.

Quand nous avons introduit le niveau [Consommation](https://aka.ms/apimconsumptionblog) d’APIM, nous avons apporté quelques changements pour simplifier la gestion des clés. Tout d’abord, nous avons ajouté deux étendues d’abonnement : toutes les API et une seule API. L’étendue des abonnements n’est plus limitée à un produit d’API. Vous pouvez maintenant créer des clés accordant l’accès à une API (ou toutes les API au sein d’une instance APIM), sans avoir à créer un produit et lui ajouter les API. Par ailleurs, chaque instance APIM comprend maintenant un abonnement immuable à toutes les API, qui simplifie les tests et le débogage des API dans la console de test.

Ensuite, APIM autorise désormais les abonnements « autonomes ». Les abonnements ne doivent plus forcément être associés à un compte de développeur. Cela est utile dans des scénarios où un abonnement est partagé par plusieurs développeurs ou équipes.

Pour finir, les éditeurs d’API peuvent désormais [créer des abonnements](api-management-howto-create-subscriptions.md) directement dans le portail Azure :

![Abonnements flexibles](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Gestion des API :

+ Découvrez d’autres [concepts](api-management-terminology.md) de Gestion des API
+ Suivez nos [tutoriels](import-and-publish.md) pour en savoir plus sur Gestion des API
+ Consultez notre [page de FAQ](api-management-faq.md) pour les questions courantes