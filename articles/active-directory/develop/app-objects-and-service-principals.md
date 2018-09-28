---
title: Objets application et principal du service dans Azure Active Directory
description: Découvrez la relation entre les objets application et principal du service dans Azure Active Directory.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: f73c4f7f606f264f899aeb6405ac7bfae71e518d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948047"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objets application et principal du service dans Azure Active Directory

Parfois, la signification du terme « application » peut être mal comprise lorsqu’il est utilisé dans le contexte d’Azure Active Directory (Azure AD). Cet article clarifie les aspects conceptuels et concrets de l’intégration des applications dans Azure AD, en donnant un exemple d’inscription et de consentement pour une [application multi-locataire](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Vue d’ensemble

Une application qui a été intégrée à Azure AD a des effets qui vont au-delà de l’aspect logiciel. Le terme « Application » est souvent utilisé comme concept, faisant référence non seulement au programme d’application, mais également à son inscription Azure AD et à son rôle lors des « conversations » d’authentification/autorisation au moment de l’exécution.

Par définition, une application peut fonctionner dans les rôles suivants :

- Rôle [Client](developer-glossary.md#client-application) (qui utilise une ressource)
- Rôle [Serveur de ressources](developer-glossary.md#resource-server) (qui expose les API aux clients)
- Rôle Client et rôle Serveur de ressources

Un [flux d’octroi d’autorisation OAuth 2.0](developer-glossary.md#authorization-grant) définit le protocole de conversation, ce qui permet au client et à la ressource d’accéder aux données d’une ressource et de les protéger.

Dans les sections suivantes, vous allez examiner la manière dont un modèle d’application Azure AD représente une application au moment de la conception et au moment de l’exécution.

## <a name="application-registration"></a>Inscription de l’application

Lorsque vous inscrivez une application Azure AD dans le [portail Azure][AZURE-Portal], deux objets sont créés dans votre client Azure AD :

- un objet application et
- un objet principal du service.

### <a name="application-object"></a>Objet application

Une application Azure AD est définie par son seul et unique objet application, qui réside dans le client Azure AD dans lequel l’application a été inscrite, appelé client « de base » de l’application. [L’entité Application][AAD-Graph-App-Entity] d’Azure AD Graph définit le schéma pour les propriétés d’un objet application.

### <a name="service-principal-object"></a>Objet principal du service

Pour accéder aux ressources qui sont sécurisées par un locataire Azure AD, l’entité qui nécessite l’accès doit être représentée par un principal de sécurité. Ceci est valable aussi bien pour les utilisateurs (principal d’utilisateur) que pour les applications (principal de service).

Le principal de sécurité définit la stratégie d’accès et les autorisations pour l’utilisateur ou l’application du locataire Azure AD. Cela rend possibles les fonctionnalités de base, telles que l’authentification de l’application ou de l’utilisateur lors de la connexion, et l’autorisation lors de l’accès aux ressources.

Lorsqu’une application reçoit l’autorisation d’accéder aux ressources d’un locataire (après inscription ou [consentement](developer-glossary.md#consent)), un objet de principal de service est créé. [L’entité ServicePrincipal][AAD-Graph-Sp-Entity] d’Azure AD Graph définit le schéma pour les propriétés d’un objet de principal de service.

### <a name="application-and-service-principal-relationship"></a>Relation entre l’application et le principal de service

Considérez l’objet application comme la représentation *globale* de votre application pour une utilisation sur tous les clients et le principal du service comme la représentation *locale* pour une utilisation sur un client spécifique.

L’objet application fait office de modèle à partir duquel les propriétés communes et par défaut sont *dérivées* pour une utilisation visant à créer des objets de principal de service correspondants. Un objet application présente donc une relation un-à-un avec l’application logicielle et une relation un-à-plusieurs avec chaque objet de principal de service correspondant.

Un principal de service doit être créé dans chaque locataire sur lequel l’application est utilisée, ce qui lui permet d’établir une identité pour la connexion et/ou l’accès aux ressources sécurisées par le locataire. Une application à locataire unique n’a qu’un seul principal de service (dans son locataire de base), créé et pouvant être utilisé pendant l’inscription de l’application. Une application web/API multi-locataire a également un principal de service créé dans chaque locataire où un utilisateur de ce locataire a consenti à son utilisation. 

> [!NOTE]
> Toute modification apportée à l’objet application de votre application est également répercutée dans son objet principal du service, uniquement dans le client de base de l’application (le client où elle a été inscrite). Pour l’accès mutualisé, les modifications apportées à l’objet application ne sont pas répercutées dans les objets principal de service des clients consommateurs jusqu’à ce que l’accès soit supprimé via le [volet d’accès à l’application](https://myapps.microsoft.com) et à nouveau octroyé.
>
> Notez également que les applications natives sont enregistrées en tant que mutualisées par défaut.

## <a name="example"></a>Exemples

Le schéma suivant illustre la relation entre un objet application d’une application et les objets principal du service correspondants dans le contexte d’un exemple d’application mutualisée appelée **RH**. Il existe trois clients Azure AD dans cet exemple de scénario :

- **Adatum** : le client utilisé par la société qui a développé **l’application RH** ;
- **Contoso** : le client utilisé par l’entreprise Contoso, qui est un consommateur de **l’application RH** ;
- **Fabrikam** : le client utilisé par l’entreprise Fabrikam, qui est également un consommateur de **l’application RH**.

![Relation entre un objet application et un objet principal du service](./media/app-objects-and-service-principals/application-objects-relationship.png)

Dans cet exemple de scénario :

| Étape | Description |
|------|-------------|
| 1    | Correspond au processus de création des objets application et principal du service dans le client de base de l’application. |
| 2    | Lorsque les administrateurs de Contoso et Fabrikam accordent leur consentement, un objet principal du service est créé dans le client Azure AD de leur entreprise et se voit attribuer les autorisations accordées par l’administrateur. Notez également que l’application RH peut être configurée/conçue de manière à autoriser le consentement par les utilisateurs à des fins d’utilisation individuelle. |
| 3    | Les clients consommateurs de l’application RH (Contoso et Fabrikam) ont chacun leur propre objet principal de service. Chacun représente leur utilisation d’une instance de l’application lors de l’exécution, régie par les autorisations consenties par l’administrateur respectif. |

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez utiliser [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) pour interroger à la fois les objets d’application et de principal de service.
- Vous pouvez accéder à l’objet application d’une application via l’API Graph Azure AD, l’éditeur de manifeste d’application du [portail Azure][AZURE-Portal] ou les [applets de commande Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), tels que représentés par son entité [Application OData][AAD-Graph-App-Entity].
- Vous pouvez accéder à l’objet principal de service d’une application via l’API Graph Azure AD ou les [applets de commande Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), tels que représentés par son [entité ServicePrincipal OData][AAD-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
