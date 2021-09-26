---
title: Prise en charge de la Gestion des API Azure pour la monétisation
description: Découvrez comment la Gestion des API Azure prend en charge les stratégies de monétisation pour vos produits API.
author: dlepow
ms.author: danlep
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: ad6e4358ebd312c498504da6650045d9019fadbe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548710"
---
# <a name="how-api-management-supports-monetization"></a>Prise en charge de la monétisation par la Gestion des API

Grâce à la plateforme de service [Gestion des API Azure](./api-management-key-concepts.md), vous pouvez :
* Publiez les API auxquelles vos consommateurs s’abonnent.
* Implémentation des risques de l’appareil. 
* Accélérez les échelles de temps du projet.
* Mettez à l’échelle vos API en toute confiance.

Dans ce document, nous nous concentrons sur les fonctionnalités de Gestion des API qui permettent d’implémenter votre stratégie de monétisation, comme fournir une expérience sans heurts pour :
* Découvrir vos API publiques.
* Entrer les informations de paiement.
* Activer votre abonnement.
* Consommer les API.
* Surveiller l’utilisation.
* Payer automatiquement pour l’utilisation de l’API.

Le diagramme ci-dessous présente ces principales fonctionnalités de Gestion des API :

:::image type="content" source="media/monetization-support/architecture-overview.png" alt-text="Diagramme des principales fonctionnalités de monétisation de la Gestion des API":::

## <a name="api-discovery"></a>Découverte de l’API

Lancez votre API et les consommateurs d’API intégrés à l’aide du portail développeur intégré de la Gestion des API. Mettez l’accent sur un contenu de développement de qualité pour le portail développeur permettant aux consommateurs de l’API d’explorer et d’utiliser vos API sans interruption. Testez le contenu et les informations fournis pour l’accessibilité, la rigueur et l’usage.

Pour plus d’informations sur la façon d’ajouter du contenu et de contrôler la personnalisation du portail développeur, consultez la [vue d’ensemble du portail développeur](./api-management-howto-developer-portal.md).

## <a name="api-packaging"></a>Packaging DRM

La gestion des API gère la manière dont vos API sont empaquetées et présentées à l’aide du concept des *produits* des *stratégies*.

### <a name="products"></a>Produits

Les API sont publiées [via les produits](./api-management-howto-add-products.md). Les produits vous permettent de définir :
* Les API auxquelles un abonné peut accéder.
* [Stratégies](./api-management-howto-policies.md) de limitation spécifiques, comme limiter un abonnement spécifique à un quota d’appels par mois.

Lorsqu’un consommateur d’API s’abonne à un produit, il reçoit une clé API, avec laquelle il effectue des appels. Au départ, l’abonnement est défini sur un état `submitted`. Activez l’abonnement pour permettre aux abonnés d’utiliser les API.

Configurez les produits de Gestion des API pour empaqueter votre API sous-jacente afin de refléter votre modèle de revenus, avec :
* Relation un-à-un entre chaque niveau de votre modèle de revenus.
* Un produit de Gestion des API correspondant.

Les exemples de projets utilisent les produits de Gestion des API comme moyen de niveau supérieur de codification de la stratégie de monétisation. Les produits de Gestion des API reflètent les niveaux de modèle de revenus et indexent le modèle de tarification spécifique pour chaque niveau. Cette configuration offre une approche flexible et pilotée par la configuration pour la préparation de la stratégie de monétisation.

### <a name="policies"></a>Stratégies

Appliquez des stratégies de Gestion des API pour contrôler la qualité de service pour chaque produit. Les exemples de projets utilisent deux fonctionnalités de stratégie spécifiques pour contrôler la qualité du service, en fonction du modèle de chiffre d’affaires :

| Fonctionnalité de stratégie | Description |
| ----- | ----- |
| **Quota** | Définit le nombre total d’appels que l’utilisateur peut passer sur l’API au cours d’une période spécifiée. Par exemple, « 100 appels par mois ». Une fois que l’utilisateur a atteint le quota, les appels sur l’API échouent et l’appelant reçoit un code d’état de réponse `403 Forbidden`. |
| **Limite du taux** | Définit le nombre d’appels dans un créneau horaire glissant qui peut être établi sur l’API. Par exemple, « 200 appels par mois ». Conçu pour empêcher les pics d’utilisation d’API au-delà de la qualité de service payante avec le produit choisi. Lorsque le débit d’appels est atteint, l’appelant reçoit le code d’état de réponse `429 Too Many Requests`. |

Pour plus d’informations sur les stratégies, consultez la documentation relative aux [Stratégies dans la Gestion API Azure](./api-management-howto-policies.md).

## <a name="api-consumption"></a>Consommation de l’API

Accordez l’accès aux consommateurs d’API vers vos API via les produits à l’aide d’abonnements d’API.

1. Les consommateurs d’API établissent des abonnements d’API lors de l’inscription à un produit de Gestion des API spécifique. 
1. Intégrez le processus d’abonnement avec le fournisseur de paiement à l’aide de la délégation de Gestion des API. 
1. Une fois que vous avez fourni les informations de paiement, les utilisateurs obtiennent l’accès à l’API avec une clé de sécurité unique générée pour l’abonnement.

Pour plus d’informations sur les abonnements, consultez la documentation relative aux [Abonnements dans la Gestion des API Azure](./api-management-subscriptions.md).

## <a name="api-usage-monitoring"></a>Surveillance de l'utilisation des API

Obtenez des Insights sur l’utilisation et les performances de votre API à l’aide de l’analyse intégrée de la Gestion des API. Ces analyses fournissent des rapports par :
* API
* Geography
* Opérations d’API
* Produit
* Requête
* Abonnement
* Temps 
* Utilisateur

Passez régulièrement en revue les rapports d’analyse pour comprendre comment votre stratégie de monétisation est adoptée par les consommateurs d’API.

Pour plus d'informations, consultez [Obtenir l’analyse des API dans la Gestion des API Azure](./howto-use-analytics.md).

## <a name="security"></a>Sécurité

Contrôlez le niveau d’accès de chaque utilisateur pour chaque produit à l’aide des produits, des stratégies d’API et des abonnements de la Gestion des API. Évitez la mauvaise utilisation et l’utilisation abusive en accordant un accès d’API au niveau de l’abonnement si l’utilisateur s’est correctement authentifié auprès du fournisseur de paiement, même si le produit d’API spécifique est gratuit.

## <a name="integration"></a>Intégration

Créez une expérience de monétisation transparente via l’intégration frontale et principale entre la Gestion des API et le fournisseur de paiement que vous avez choisi.  Utilisez la délégation Gestion des API pour l’intégration frontale et l’API REST pour l’intégration principale.

### <a name="delegation"></a>La délégation

Dans les exemples de projets, vous pouvez utiliser la [délégation de la Gestion des API](./api-management-howto-setup-delegation.md) pour effectuer des intégrations personnalisées avec les fournisseurs de paiement tiers. La démonstration utilise la délégation pour les expériences d’inscription/de connexion et d’abonnement au produit.

#### <a name="sign-upsign-in-workflow"></a>Flux de travail d’inscription/de connexion

1. Le développeur clique sur le lien de connexion ou d’inscription sur le portail développeur de la Gestion des API.
1. Le navigateur redirige vers le point de terminaison de la délégation (configuré sur une page de l’application du portail de facturation personnalisée).
1. L’application du portail de facturation personnalisé présente une interface utilisateur de connexion/inscription.
1. Une fois la connexion/inscription réussie, l’utilisateur est authentifié et redirigé vers la page de démarrage du portail développeur de la Gestion des API.

#### <a name="product-subscription-workflow"></a>Flux de travail de l’abonnement au produit

1. Le développeur sélectionne un produit dans le portail développeur de la Gestion des API, puis clique sur le bouton **S’abonner**.
1. Le navigateur redirige vers le point de terminaison de la délégation (configuré sur une page de l’application du portail de facturation personnalisée).
1. Application du portail de facturation personnalisée :
    * Présente une interface utilisateur configurée en fonction du fournisseur de paiement (Stripe ou Adyen).
    * Guide l’utilisateur dans le processus de validation approprié.
1. L’utilisateur est redirigé vers la page de démarrage du produit de la Gestion des API. 
    * Le produit sera actif et les clés API disponibles.

### <a name="rest-api"></a>API REST

Utilisez l’API REST pour l’automatisation du fonctionnement de votre stratégie de monétisation par la Gestion des API.

Les exemples de projets utilisent l’API pour réaliser par programmation ce qui suit  :

- Récupérer les produits et les stratégies de la Gestion des API pour activer la configuration synchronisée de concepts similaires dans les fournisseurs de paiement, tels que Stripe.
- Interroger régulièrement la gestion des API pour récupérer les métriques d’utilisation des API pour chaque abonnement et piloter le processus de facturation.

Pour plus d’informations, consultez la vue d’ensemble [ REST API de la Gestion des API Azure ](/rest/api/apimanagement/#rest-operation-groups).

## <a name="devops"></a>DevOps

Modifications du contrôle de version et de l’automatisation du déploiement apportées à la Gestion des API à l’aide du Gestionnaire de ressource Azure, notamment la configuration des fonctionnalités qui implémentent votre stratégie de monétisation, comme :
* Produits
* Stratégies
* Le portail des développeurs

Dans les exemples de projets, les scripts du Gestionnaire de ressource Azure sont augmentés par un fichier JSON, qui définit le modèle de tarification de chaque produit de la Gestion des API. Avec cette augmentation, vous pouvez synchroniser la configuration entre la Gestion des API et le fournisseur de paiement choisi. La solution entière est gérée dans un référentiel de contrôle de code source unique, pour :
* Coordonner toutes les modifications associées à l’évolution de la stratégie de monétisation en cours sous la forme d’une seule version.
* Effectuer les modifications, en suivant les exigences de gouvernance et d’audit.

## <a name="initialization-and-deployment"></a>Initialisation et déploiement

La Gestion des API peut être déployée via :
* L’interface utilisateur du portail Azure ou
* Une « infrastructure en tant que code » utilisant les [modèles du gestionnaire de ressource Azure](https://azure.microsoft.com/services/arm-templates). 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les stratégies de monétisation de la Gestion des API](monetization-overview.md).
* Déployez une Adyen de démonstration ou une intégration par répartition via le [Référentiel Git](https://github.com/microsoft/azure-api-management-monetization) associé.