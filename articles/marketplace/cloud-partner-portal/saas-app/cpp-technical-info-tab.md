---
title: Configuration technique d’une offre d’application Azure SaaS | Microsoft Docs
description: Configurez les informations techniques pour une offre d’application SaaS sur la place de marché Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: da67213b708b3ff26fe1ddd926c5cb0bba33458d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788547"
---
# <a name="saas-application-technical-info-tab"></a>Onglet des informations techniques de l’application SaaS

L’onglet Informations techniques fournit le formulaire de configuration technique. Ce formulaire vous permet de choisir le type d’application SaaS créée et de configurer la façon dont votre application est fournie aux clients.

![Formulaire de configuration technique](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>Formulaire de configuration technique

Ce formulaire comporte 2 champs : Produits et Invite à l’action.

### <a name="product-field"></a>Champ Produit

Vous pouvez fournir une application SaaS pour les deux vitrines suivantes :
- Pour un utilisateur professionnel, en sélectionnant l’option **Liste**.
- Pour un utilisateur administrateur, en sélectionnant **Vendre via Microsoft**.
Pour vous aider à choisir le type d’application SaaS que vous créez, lisez l’article [Comprendre la sélection de la vitrine](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).

#### <a name="sell-through-microsoft"></a>Vendre via Microsoft
Pour générer cette expérience, vous devez configurer les éléments suivants :

- Connectez le site web de votre service SaaS avec les API SaaS de Microsoft. L’article [SaaS - Vendre via Azure - API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) vous explique comment créer cette connexion.
- Activez Vendre via Azure sur le portail Cloud Partner dans le formulaire de configuration technique et indiquez les informations nécessaires. Pour plus d’informations sur ce modèle de facturation et la façon dont il est implémenté, consultez l’article [SaaS – Vendre via Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions#overview).

 ![Formulaire Vendre via Microsoft](./media/saas-techinfo-sellthrough-ms.png)

Le tableau suivant décrit les champs requis pour la vente via Microsoft.

|  **Nom du champ**   |  **Description**  |
|  ---------------  |  ---------------  |
|    ID d’abonnement pour préversion               |    Tous les identificateurs d’abonnement Azure utilisés pour tester votre offre en préversion avant qu’elle ne soit disponible publiquement.               |
|     Instructions de prise en main              |   Instructions à transmettre à vos clients pour les aider à se connecter à votre application SaaS. Les balises HTML de base sont autorisées, par exemple : &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;, etc.                |
|    URL de la page d’accueil  |   L’URL du site vers lequel vous allez rediriger vos clients après avoir effectué l’achat sur le portail Azure. Cette URL sera également le point de terminaison qui recevra les API de connexion afin de faciliter le commerce avec Microsoft.                |
|  Webhook de connexion    |  Pour tous les événements asynchrones que Microsoft doit vous envoyer au nom du client (exemple : un abonnement Azure devenu non valide), nous vous demandons de nous fournir un webhook de connexion. Si vous ne disposez pas d’un système de webhook, la configuration la plus simple consiste à disposer d’une application de logique de point de terminaison HTTP qui écoute les événements qui sont publiés et les traite de façon appropriée. Pour plus d’informations, consultez <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">Appeler, déclencher ou imbriquer des workflows via des points de terminaison HTTP dans des applications logiques</a>                |
|  ID d’abonné Azure AD et ID d’application      |   Dans le portail Azure, vous devez créer une application Active Directory afin que nous puissions confirmer que la connexion entre nos deux services se fait bien dans le cadre d’une communication authentifiée. Pour ces champs, créez une application AD et collez l’ID d’abonné et l’ID d’application nécessaires. Notez que l’id d’application est associé à votre publisherID. Par conséquent, assurez-vous qu’il s’agit du même ID d’application, comme dans toutes les offres.             |


Enfin, si vous sélectionnez **Vendre via Microsoft**, il existe un autre onglet Nouvelle offre nommé **Plans**. 

L’[onglet Plans](./cpp-plans-tab.md) répertorie les plans et les prix que prend en charge votre application SaaS. Actuellement, nous autorisons uniquement la tarification mensuelle, avec la possibilité d’autoriser 1 à 3 mois d’accès gratuit. Ces plans et ces prix doivent correspondre aux plans et aux prix que vous présentez sur votre site de l’application SaaS.

>[!NOTE] 
>Les plans sont nécessaires seulement si vous choisissez de vendre via Microsoft.

### <a name="call-to-action-field"></a>Champ Invite à l’action

Le champ Invite à l’action vous permet de choisir le message qui s’affiche sur le bouton d’acquisition de votre offre. Les options suivantes sont disponibles :

- Gratuit – si vous sélectionnez cette option, vous êtes invité à entrer une URL de version d’évaluation grâce à laquelle les clients peuvent accéder à votre application SaaS. Par exemple : https://contoso.com/trial
- Essai gratuit – si vous sélectionnez cette option, vous êtes invité à entrer une URL de version d’évaluation grâce à laquelle les clients peuvent accéder à votre application SaaS. Par exemple : https://contoso.com/trial
- Me contacter

Pour plus d’informations sur les options Appel à l’action , consultez Choisir une option de publication.

## <a name="next-steps"></a>Étapes suivantes

- [Onglet Plans (facultatif)](./cpp-plans-tab.md)
- [Onglet des informations de canal](./cpp-channel-info-tab.md)
