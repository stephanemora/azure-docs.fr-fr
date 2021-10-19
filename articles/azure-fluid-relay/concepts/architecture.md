---
title: Vue d’ensemble de l’architecture de Relais Azure Fluid
description: Vue d’ensemble de l’architecture de Relais Azure Fluid
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/overview/
ms.openlocfilehash: 6b58c90692a7d4eba404e4d5da84b175d229f343
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661840"
---
# <a name="overview-of-azure-fluid-relay-architecture"></a>Vue d’ensemble de l’architecture de Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Il existe trois concepts principaux à bien comprendre lors de la création d’une application avec Fluid.

- Service
- Conteneur
- Objets partagés

## <a name="service"></a>Service

Les clients Fluid requièrent un service centralisé que tous les clients connectés utilisent pour envoyer et recevoir des opérations. Lorsque vous utilisez Fluid dans une application, vous devez faire usage du package approprié correspondant au service sous-jacent auquel vous vous connectez.

Pour le service Relais Azure Fluid, ce package est **@fluidframework/azure-client** . Ce package permet de créer et de charger des conteneurs Fluid hébergés sur Azure via un Relais Azure Fluid.

## <a name="container"></a>Conteneur

Le **conteneur** est l’unité principale d’encapsulation dans Fluid. Il se compose d’une collection d’objets partagés et d’API de prise en charge pour gérer le cycle de vie du conteneur et des objets qu’il contient.

La création de conteneurs est une action pilotée par le client, et les durées de vie des conteneurs sont liées aux données stockées sur le serveur de prise en charge. Lors de l’obtention de conteneurs existants, il est important de prendre en compte l’état précédent du conteneur.

Pour plus d’informations sur les conteneurs, consultez [conteneurs](https://fluidframework.com/docs/build/containers/) sur fluidframework.com.

## <a name="shared-objects"></a>Objets partagés

Un **objet partagé** est un type d’objet qui optimise des données collaboratives en exposant une API spécifique. De nombreux objets partagés peuvent exister dans le contexte d’un conteneur, qui peuvent être créés de manière statique ou dynamique. Les deux types d’objets partagés sont les **structures de données distribuées (DDSes)** et les **DataObjects**.

Pour plus d’informations, consultez [Modélisation des données](https://fluidframework.com/docs/build/data-modeling/) sur fluidframework.com.

## <a name="package-structure"></a>Structure de package

Il existe deux **packages** principaux que vous allez utiliser lors de la génération avec Fluid. Le package **fluid-framework** et un package client spécifique du service tels qu’**azure-client**.

Pour plus d’informations, consultez [Packages](https://fluidframework.com/docs/build/packages/) sur fluidframework.com.

### <a name="the-fluid-framework-package"></a>Package fluid-framework

Le package **fluid-framework** est une collection d’API Fluid de base qui facilitent la création et l’utilisation d’applications. Ce package contient toutes les définitions de type communes, ainsi que tous les objets partagés primitifs.

### <a name="the-fluidframeworkazure-client-package"></a>Package @fluidframework/azure-client

Le package **@fluidframework/azure-client** fournit une API pour la connexion aux instances de service Relais Azure Fluid afin de créer et de charger des conteneurs Fluid. Pour plus d’informations sur la manière d’utiliser cette API, consultez [Guide pratique : Se connecter à un service Relais Azure Fluid](../how-tos/connect-fluid-azure-service.md).
