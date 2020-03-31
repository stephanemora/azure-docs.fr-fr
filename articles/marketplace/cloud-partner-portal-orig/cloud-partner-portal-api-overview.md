---
title: Référence d’API pour le portail Cloud Partner | Place de marché Azure
description: Description, prérequis et liste des opérations de l’API marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288544"
---
<a name="cloud-partner-portal-api-reference"></a>Référence d’API pour le portail Cloud Partner
==================================

Les API REST du portail Cloud Partner permettent de récupérer de manipuler par programmation des charges de travail, des offres et des profils d’éditeur. Les API utilisent le contrôle d’accès en fonction du rôle (RBAC) pour appliquer les autorisations appropriées au moment du traitement.

Cette référence contient les détails techniques des API REST du portail Cloud Partner. Les exemples de charge utile de ce document sont fournis à titre de référence uniquement et sont susceptibles de changer lors de l’ajout de nouvelles fonctionnalités.


<a name="prerequisites-and-considerations"></a>Prérequis et considérations
-------------------------------

Avant d’utiliser les API, consultez :

- L’article [Prérequis](./cloud-partner-portal-api-prerequisites.md) pour savoir comment ajouter un principal de service à votre compte et obtenir un jeton d’accès Azure Active Directory (Azure AD) pour l’authentification. 
- Les deux stratégies de [contrôle d’accès concurrentiel](./cloud-partner-portal-api-concurrency-control.md)
disponibles pour appeler ces API.
- Les autres [considérations](./cloud-partner-portal-api-considerations.md) relatives aux API, comme le contrôle de version et la gestion des erreurs.


<a name="common-tasks"></a>Tâches courantes
------------
Cette référence décrit en détail les API qui permettent d’effectuer les tâches courantes suivantes.


### <a name="offers"></a>Offres

-   [Récupérer toutes les offres](./cloud-partner-portal-api-retrieve-offers.md)
-   [Récupérer une offre en particulier](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Récupérer l’état de l’offre](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Créer une offre](./cloud-partner-portal-api-creating-offer.md)
-   [Publier une offre](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Opérations

-   [Récupérer des opérations](./cloud-partner-portal-api-retrieve-operations.md)
-   [Annuler des opérations](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publier une application

-   [Démarrer](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Autres tâches

-   [Définir la tarification des offres de machine virtuelle](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Dépannage

-   [Résoudre les erreurs d’authentification](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
