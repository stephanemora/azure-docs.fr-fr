---
title: Autoriser les applications multitenants – Azure Digital Twins | Microsoft Docs
description: Comment configurer des applications Azure Active Directory mutualisées pour Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264931"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Applications multilocataires avec Azure Digital Twins

Les développeurs de solutions qui créent sur Azure Digital Twins peuvent vouloir prendre en charge plusieurs clients avec un service ou une solution uniques. En fait, les applications *mutualisées* font partie des configurations Azure Digital Twins les plus courantes.

Ce document décrit comment configurer une application Azure Digital Twins pour prendre en charge plusieurs locataires et clients Azure Active Directory.

## <a name="multitenancy"></a>Mutualisation

Une ressource *mutualisée* est une instance approvisionnée qui prend en charge plusieurs clients. Chaque client possède ses propres données et privilèges. L’expérience de chaque client est isolée de celles des autres de sorte que l’« affichage » de son application est distinct.

Pour en savoir plus sur la mutualisation, voir [Applications mutualisées dans Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Scénario du problème

Dans ce scénario, imaginez un développeur qui crée une solution Azure Digital Twins (**DÉVELOPPEUR**) et un client qui utilise cette solution (**CLIENT**) :

- **DÉVELOPPEUR** dispose d’un abonnement Azure avec un locataire Azure Active Directory.
- **DÉVELOPPEUR** déploie une instance Azure Digital Twins dans son abonnement Azure. Azure Active Directory a créé automatiquement un principal du service dans le locataire Azure Active Directory de **DÉVELOPPEUR**.
- Les utilisateurs au sein du locataire Azure Active Directory de **DÉVELOPPEUR** peuvent ensuite [acquérir des jetons OAuth 2.0](./security-authenticating-apis.md) à partir du service Azure Digital Twins.
- **DÉVELOPPEUR** crée à présent une application mobile qui s’intègre directement avec les API de gestion d’Azure Digital Twins.
- **DÉVELOPPEUR** autorise **CLIENT** à utiliser l’application mobile.
- **CLIENT** doit être autorisé à utiliser l’API de gestion Azure Digital Twins au sein de l’application de **DÉVELOPPEUR**.

Problème :

- Lorsque **CLIENT** se connecte à l’application de **DÉVELOPPEUR**, l’application ne peut pas acquérir de jetons permettant aux utilisateurs de **CLIENT** de s’authentifier auprès des API de gestion d’Azure Digital Twins.
- Une exception est levée dans Azure Active Directory, indiquant qu’Azure Digital Twins n’est pas reconnu l’annuaire de **CLIENT**.

## <a name="problem-solution"></a>Solution au problème

Pour résoudre le scénario de problème précédent, les actions suivantes sont nécessaires pour créer un principal du service Azure Digital Twins dans le locataire Azure Active Directory de **CLIENT** :

- Si **CLIENT** n’a pas encore d’abonnement Azure avec un locataire Azure Active Directory :

  - L’administrateur de locataires Azure Active Directory de **CLIENT** doit acquérir un [abonnement Azure avec paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - L’administrateur de locataires Azure Active Directory de **CLIENT** doit ensuite [lier son locataire au nouvel abonnement](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Dans le [portail Azure](https://portal.azure.com), l’administrateur de locataires Azure Active Directory de **CLIENT** effectue ensuite les opérations suivantes :

  1. Recherchez **Abonnements** dans le champ recherche Azure situé en haut. Sélectionnez **Abonnements**.
  1. Sélectionner l’abonnement qui comprend le locataire Azure Active Directory à utiliser dans l’application de **DÉVELOPPEUR**.

     [![Abonnements Azure Active Directory](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Sélectionner les **Fournisseurs de ressources**
  1. Rechercher **Microsoft.IoTSpaces**
  1. Sélectionnez **Inscription**.

     [![Fournisseurs de ressources Azure Active Directory](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’utilisation de fonctions définies par l’utilisateur avec Azure Digital Twins, voir [Guide pratique pour créer des fonctions définies par l’utilisateur dans Azure Digital Twins](./how-to-user-defined-functions.md).

- Pour savoir comment utiliser le contrôle d’accès en fonction du rôle dans le but de renforcer la sécurité de l’application avec des attributions de rôles, voir [Créer et gérer des attributions de rôle dans Azure Digital Twins](./security-create-manage-role-assignments.md).
