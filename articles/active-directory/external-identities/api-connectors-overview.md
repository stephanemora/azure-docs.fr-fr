---
title: À propos des connecteurs d’API dans les flux d’inscription en libre-service - Azure AD
description: Utilisez les connecteurs d’API Azure Active Directory (Azure AD) pour personnaliser et étendre vos flux d’utilisateurs d’inscription en libre-service à l’aide d’API web.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a5563ff1f57f6b3684834a2488fc0665ac5eddd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610040"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Utiliser des connecteurs d’API pour personnaliser et étendre l’inscription en libre-service 

## <a name="overview"></a>Vue d’ensemble 
En tant que développeur ou administrateur informatique, vous pouvez utiliser des connecteurs d’API pour intégrer vos [flux d’utilisateur d’inscription en libre-service](self-service-sign-up-overview.md) avec des API web pour personnaliser l’inscription et intégrer des systèmes externes. Par exemple, avec les connecteurs d’API, vous pouvez :

- [**Intégrer à un flux de travail d’approbation personnalisé**](self-service-sign-up-add-approvals.md). Connectez-vous à un système d’approbation personnalisé pour gérer et limiter la création de comptes.
- [**Exécuter une vérification d’identité**](code-samples-self-service-sign-up.md#identity-verification). Utilisez un service de vérification d’identité pour ajouter un niveau supplémentaire de sécurité aux décisions de création de comptes.
- **Valider des données d’entrée utilisateur**. Validez par rapport à des données utilisateur incorrectes ou non valides. Par exemple, vous pouvez valider les données fournies par l’utilisateur par rapport à des données existantes dans une banque de données externe ou une liste de valeurs autorisées. En cas d’invalidité, vous pouvez demander à un utilisateur de fournir des données valides ou empêcher l’utilisateur de continuer le processus d’inscription.
- **Remplacer des attributs utilisateur**. Reformatez ou attribuez une valeur à un attribut collecté auprès de l’utilisateur. Par exemple, si un utilisateur entre le prénom entier en lettres minuscules ou majuscules, vous pouvez modifier sa mise en forme en utilisant une majuscule uniquement pour la première lettre. 
- **Exécuter une logique métier personnalisée**. Vous pouvez déclencher des événements en aval dans vos systèmes ce cloud pour envoyer des notifications Push, mettre à jour des bases de données d’entreprise, gérer des autorisations, auditer des bases de données et effectuer d’autres actions personnalisées.

Un connecteur d’API fournit à Azure Active Directory les informations nécessaires pour appeler le point de terminaison d’API en définissant l’URL de point de terminaison HTTP et l’authentification pour l’appel d’API. Une fois que vous avez configuré un connecteur d’API, vous pouvez l’activer pour une étape spécifique dans un flux utilisateur. Quand un utilisateur atteint cette étape dans le flux d’inscription, le connecteur d’API est appelé et se matérialise en tant que requête HTTP POST à votre API, en envoyant des informations utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. La réponse d’API peut affecter l’exécution du flux utilisateur. Par exemple, la réponse d’API peut empêcher un utilisateur de s’inscrire, demander à l’utilisateur d’entre à nouveau des informations ou ajouter les attributs de l’utilisateur.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Où activer un connecteur d’API dans un flux utilisateur

Il existe deux emplacements dans un flux utilisateur où vous pouvez activer un connecteur d’API :

- après la connexion avec un fournisseur d’identité
- avant la création de l’utilisateur

> [!IMPORTANT]
> Dans ces deux cas, les connecteurs d’API sont appelés lors de l’**inscription** de l’utilisateur et non de la connexion.

### <a name="after-signing-in-with-an-identity-provider"></a>après la connexion avec un fournisseur d’identité

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur. Cette étape n’est pas appelée si un utilisateur s’inscrit auprès d’un compte local. Voici quelques exemples de scénarios de connecteur d’API que vous pouvez activer à cette étape :

- Utilisez l’adresse e-mail ou l’identité fédérée que l’utilisateur a fournie pour rechercher des revendications dans un système existant. Retournez ces revendications à partir du système existant, complétez la page de collection d’attributs, puis rendez-les disponibles pour retourner dans le jeton.
- Implémentez une liste d’autorisation ou de blocage en fonction de l’identité sociale.

### <a name="before-creating-the-user"></a>avant la création de l’utilisateur

Un connecteur d’API à cette étape du processus d’inscription est appelé après la page de collection d’attributs, si elle est inclue. Cette étape est toujours appelée avant qu’un compte d’utilisateur ne soit créé. Voici quelques exemples de scénarios que vous pouvez activer à cette étape au cours de l’inscription :

- Validez les données d’entrée utilisateur et demandez à un utilisateur de renvoyer des données.
- Bloquer l’inscription utilisateur en fonction des données entrées par l’utilisateur.
- Exécuter une vérification d’identité.
- Interrogez des systèmes externes pour obtenir des données existantes sur l’utilisateur afin de les retourner dans le jeton d’application ou de les stocker dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [ajouter un connecteur d’API à un flux d’utilisateur](self-service-sign-up-add-api-connector.md)
- Découvrez comment [ajouter un système d’approbation personnalisé à l’inscription en libre-service](self-service-sign-up-add-approvals.md)