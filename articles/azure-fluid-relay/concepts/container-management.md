---
title: Gestion des conteneurs Fluid
description: Vue d’ensemble de la gestion des conteneurs dans le service Relais Azure Fluid.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 934122f4cb952a4be915c55c2555e336705a71c2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710363"
---
# <a name="managing-fluid-containers"></a>Gestion des conteneurs Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Un conteneur est l’unité atomique de stockage dans le service Azure Fluid Relay et représente les données stockées à partir d’une session Fluid, y compris les opérations et les instantanés. Le runtime Fluid utilise le conteneur pour réalimenter l’état d’une session Fluid lorsqu’un utilisateur rejoint la session pour la première fois, ou s’il la rejoint après l’avoir quittée.

Lors de la création d’une application avec l’infrastructure Fluid, vous devez tenir compte de plusieurs choses pour la création et la gestion d’un conteneur, comme indiqué dans ce diagramme.

:::image type="content" source="../images/fluid-service-architecture-ownership.jpg" alt-text="Illustration de l’architecture d’un service Fluid et des parties détenues par les développeurs et Microsoft.":::

## <a name="key-concepts"></a>Concepts clés

### <a name="container-permissions"></a>Autorisations du conteneur 

Dans la plupart des cas, les développeurs doivent gérer un inventaire des conteneurs et des autorisations de conteneur. Cela inclut des informations sur les personnes qui ont accès aux conteneurs, ainsi que des métadonnées telles que le nom convivial du conteneur.

### <a name="accessing-containers"></a>Accès aux conteneurs

Les conteneurs sont référencés par ID de conteneur. Pour qu’un utilisateur puisse créer ou ouvrir un conteneur, il doit demander un JWT que le runtime fluide utilisera lors de la communication avec le service Relais Azure Fluid. Tout processus avec un jeton JWT valide peut accéder à un conteneur. Il incombe au développeur de générer des jetons JWT pour l’accès au conteneur, ce qui leur permet de maîtriser la logique métier afin de contrôler l’accès en fonction de leur scénario. Le service Relais Azure Fluid n’a aucune connaissance des utilisateurs qui doivent avoir accès à un conteneur. Pour plus d’informations sur cette rubrique, consultez [Contrat de jeton Relais Azure Fluid](../how-tos/fluid-json-web-token.md)

> [!NOTE]
> Le champ JWT **documentID** correspond à l’ID de conteneur Fluid.

### <a name="container-naming"></a>Attribution de noms aux conteneurs

Les conteneurs sont nommés par le service Relais Azure Fluid au moment de la création du conteneur. L’action Créer retourne un nom de conteneur sous la forme d’un GUID qui doit être utilisé ultérieurement pour ouvrir le conteneur. Dans la plupart des cas, les développeurs souhaitent stocker ce GUID d’ID de conteneur, ainsi qu’un nom convivial, dans leur propre magasin de données pour faciliter les flux de détection de conteneur. 

### <a name="container-discovery"></a>Détection de conteneur

Les développeurs sont responsables des expériences et de la logique métier liées à la détection par les utilisateurs des conteneurs existants. Cela peut prendre la forme d’une liste navigable de conteneurs en fonction de la participation de l’utilisateur à la session Fluid, du partage direct des conteneurs entre les utilisateurs ou de l’affectation programmatique de conteneurs à des artefacts ou des processus existants.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de l’architecture de Relais Azure Fluid](architecture.md)
- [Structures de données distribuées](data-structures.md)
- [Contrat de jeton Relais Azure Fluid](../how-tos/fluid-json-web-token.md)
