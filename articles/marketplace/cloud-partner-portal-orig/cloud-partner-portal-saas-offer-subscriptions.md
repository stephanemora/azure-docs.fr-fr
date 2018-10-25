---
title: SaaS - Vendre via Azure | Microsoft Docs
description: Décrit le modèle de facturation de l’abonnement pour les applications SaaS et son implémentation.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806447"
---
<a name="saas---sell-through-azure"></a>SaaS - Vendre via Azure
=========================

Cet article décrit le modèle de facturation de l’abonnement aux applications SaaS et explique comment l’implémenter dans le portail Cloud Partner.


<a name="overview"></a>Vue d’ensemble
--------

La Place de marché Microsoft Azure vous permet de publier et de commercialiser vos offres SaaS via la Place de marché Microsoft Azure. Vos clients peuvent désormais se voir facturer le service directement par le biais de la facturation Azure. Ils recevront une seule facture Azure pour toutes les ressources et tous les services qu’ils ont activés. Pour la publication, les abonnements SaaS présentent les avantages suivants :

-   **Publication homogène** : si vous publiez déjà des offres SaaS ou tout autre type d’offre dans la Place de marché Microsoft Azure, l’expérience de publication se fait via le même portail de publication.
-   **Nouvelle vitrine pour la découverte** : toutes les offres publiées seront visibles sur la Place de marché Microsoft Azure externe et dans l’extension de la Place de marché Microsoft Azure sur portail Azure.
-   **Facturation intégrée** : vous pouvez désormais vendre dans toutes les régions dans lesquelles vend Azure et laisser Azure s’occuper de la facturation à votre place.
-   **Génération de prospects intégrée** : vous pouvez désormais automatiquement recevoir des prospects d’Azure, dans le logiciel CRM de votre choix, dès qu’un utilisateur Azure s’abonne à votre service SaaS via la Place de marché Microsoft Azure.
-   **Vente jointe avec les vendeurs Microsoft** : vous pouvez remplir les conditions requises pour le partage de leads bidirectionnel, le référencement prioritaire dans les catalogues et la possibilité de conclure des accords conjoints avec les vendeurs Microsoft.

<a name="billing-models"></a>Modèles de facturation
--------------

Le seul modèle de facturation pris en charge actuellement est un tarif mensuel fixe pour l’abonnement à votre service SaaS.

**Remarque** : d’autres modèles de facturation pourront être disponibles à l’avenir.

Chaque offre SaaS peut comporter un ou plusieurs plans (par exemple, De base ou Premium). Chaque plan contient des métadonnées de base qui lui sont associées, ainsi que le prix associé au plan.

Vous avez le contrôle total de la définition du plan. Par exemple, que comprend le plan de base ? Le plan est défini par vous. Vous pouvez donc fournir le texte nécessaire pour le décrire dans le cadre de la publication de votre plan.

Le prix associé au plan correspond aux frais mensuels fixes que les utilisateurs Azure doivent payer pour utiliser le service.

### <a name="what-is-not-supported-today"></a>Quels sont les éléments qui ne sont actuellement pas pris en charge ?

-   Facturation basée sur des unités personnalisées, par exemple un prix défini en fonction du nombre de requêtes.
-   Facturation en fonction de l’allocation des postes, par exemple en permettant aux utilisateurs d’acheter des licences en fonction du nombre d’utilisateurs Azure.

<a name="end-to-end-flow"></a>Flux de bout en bout
---------------

Flux de l’offre d’abonnement SaaS du point de vue de l’utilisateur final

**Remarque :** cette description du flux part du principe que vous avez publié votre offre SaaS dans la Place de marché Microsoft Azure appelée « Contoso demo SaaS ».

![Flux utilisateur de l’abonnement SaaS](media/saas-offer-subscription/saas-subscription-user-flow.png)

L’utilisateur Azure peut bénéficier des interactions suivantes avec votre service SaaS :

-   Détecter votre service SaaS dans la Place de marché Microsoft Azure
-   S’abonner à votre service SaaS dans Azure
-   Accéder au service SaaS à partir du portail Azure
-   Créer un compte dans le service SaaS et gérer (changer de plan ou le supprimer) le compte dans SaaS
-   Se désabonner du service SaaS à partir du portail Azure

<a name="discover-your-saas-service-in-azure-marketplace"></a>Détecter votre service SaaS dans la Place de marché Microsoft Azure
-----------------------------------------------

Lorsque les utilisateurs ouvrent la Place de marché Microsoft Azure dans le portail Azure, ils voient une catégorie appelée « Software as a service (SaaS) ».

![Découvrir le SaaS à l’aide du menu des catégories](media/saas-offer-subscription/saas-category-menu.png)

Les utilisateurs peuvent également rechercher le service SaaS.

![Découvrir SaaS à l’aide de la recherche](media/saas-offer-subscription/saas-cpp-search.png)

Les utilisateurs peuvent ensuite voir les détails de votre service puis cliquer sur **Créer**.

![Création d’un abonnement SaaS](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>S’abonner à un service SaaS dans Azure

L’utilisateur peut ensuite s’abonner au service SaaS à partir d’Azure.

![Abonnement à un service SaaS](media/saas-offer-subscription/saas-subscribe-signup.png)

Lorsque l’utilisateur s’abonne à votre service SaaS, l’utilisateur fournit les informations suivantes :

-   Nom : nom que les utilisateurs peuvent découvrir ou gérer pour cette instance d’abonnement SaaS dans Azure.
-   Abonnement : contexte de l’abonnement qu’ils souhaitent associer à la facturation pour le service SaaS.
-   Plan : plan de service SaaS auquel ils souhaitent s’abonner.

Le document des conditions d’utilisation fourni dans le cadre de la publication de l’offre est également présenté à l’utilisateur avant qu’il ne s’abonne au service SaaS.

L’utilisateur peut maintenant s’abonner au service en cliquant sur **S’abonner**.
Azure envoie une notification dans le portail une fois l’abonnement terminé.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Accéder au service SaaS à partir du portail Azure

Les utilisateurs cliquent ensuite sur **Accéder à la ressource** pour voir ou gérer leur instance d’abonnement SaaS.

![Voir ou gérer votre instance SaaS](media/saas-offer-subscription/saas-go-to-resource.png)

L’utilisateur est averti qu’il doit d’abord configurer le compte dans le service SaaS. La facturation commencera une fois que le service SaaS notifie Azure que la facturation peut commencer, c’est-à-dire lorsque l’utilisateur crée un compte sur le site du service SaaS.

![Configurer votre instance SaaS](media/saas-offer-subscription/saas-configure-account.png)

Lorsque l’utilisateur clique sur **Configurer le compte**, il est redirigé vers votre point de terminaison de service SaaS. Au cours de cette redirection, un jeton est transmis comme paramètre de requête. Par exemple : 

![Jeton de compte SaaS](media/saas-offer-subscription/saas-account-token.png)

Notez la valeur de ce jeton. Ce jeton est de courte durée et doit être résolu afin d’obtenir un identificateur d’abonnement dans Azure.

<a name="creating-a-saas-offer-subscription"></a>Création d’un abonnement à une offre SaaS
----------------------------------

Pour générer cette expérience, deux éléments sont nécessaires :

-   Connectez le site web de votre service SaaS avec les API SaaS de Microsoft. Ce document [SaaS - Vendre via Azure - API](./cloud-partner-portal-saas-subscription-apis.md) explique comment créer cette connexion.  
-   Activez **Vendre via Azure** sur le portail Cloud Partner dans la section **Informations techniques** et indiquez tous les détails de votre configuration.

![Informations techniques sur la nouvelle offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

Voici une explication sur tous les champs obligatoires de la section des **informations techniques** :

|  **Champs de l’offre**                 |  **Description**                                   |
|  ----------------                 |  -------------------------------------             |
| ID d’abonnement pour préversion          | Tous les identificateurs d’abonnement Azure utilisés pour tester votre offre en préversion avant qu’elle ne soit disponible publiquement. |
| Instructions de prise en main      | Instructions à transmettre à vos clients pour les aider à se connecter à votre application SaaS. L’utilisation de code HTML de base est autorisée, par exemple les balises `<p>`, `<h1>`, `<li>`, etc.  |
| URL de la page d’accueil                  | L’URL du site vers lequel vous allez rediriger vos clients après avoir effectué l’achat sur le portail Azure. Cette URL sera également le point de terminaison qui recevra les API de connexion afin de faciliter le commerce avec Microsoft.  |
| Webhook de connexion                | Pour tous les événements asynchrones que Microsoft doit vous envoyer au nom du client (par exemple l’abonnement Azure n’est plus valide), nous vous demandons de nous fournir un webhook de connexion. Si vous ne disposez pas d’un système de webhook, la configuration la plus simple consiste à disposer d’une application de logique de point de terminaison HTTP qui écoute les événements qui sont publiés et les traite de façon appropriée.  Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| ID d’abonné Azure AD et ID d’application     | Dans le portail Azure, vous devez créer une application Active Directory afin que nous puissions confirmer que la connexion entre nos deux services se fait bien dans le cadre d’une communication authentifiée. Pour ces champs, créez une application AD et collez l’ID d’abonné et l’ID d’application nécessaires. |
|  |  |


Enfin, si vous sélectionnez **Vendre via Azure**, une section appelée **Plans** est ajoutée. Les plans sont uniquement nécessaires si l’option Vendre via Azure est sélectionnée. Cette section répertorie les plans et les prix que prend en charge votre application SaaS. Actuellement, nous autorisons uniquement la tarification mensuelle, avec la possibilité d’autoriser 1 à 3 mois d’accès gratuit. Ces plans et ces prix doivent correspondre aux plans et aux prix que vous présentez sur votre site de l’application SaaS.
