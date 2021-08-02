---
title: Comment créer l’instance Azure Web PubSub
description: Présentation des options de création d’une instance Azure Web PubSub et de la manière de procéder.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/17/2021
ms.openlocfilehash: f3ed823d989abcb32ad7680e902fc5d52200e726
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951032"
---
# <a name="how-to-create-azure-web-pubsub-instance"></a>Comment créer une instance Azure Web PubSub

Pour créer une application avec le service Azure Web PubSub, vous devez créer l’instance Web PubSub, puis connecter vos clients et vos serveurs. Ce guide pratique vous présente les options permettant de créer une instance Azure Web PubSub.

## <a name="create-azure-web-pubsub-instance-with-azure-portal"></a>Créer une instance Azure Web PubSub avec Portail Azure 

Le [portail Azure](../azure-portal/index.yml) est une console web unifiée qui propose une alternative aux outils en ligne de commande. Vous pouvez gérer votre abonnement Azure avec le portail Azure. Créez, gérez et supervisez tout ce que vous voulez, de simples applications web à des déploiements cloud complexes. Vous pouvez également créer une instance de service Azure Web PubSub avec Portail Azure.

1. Cliquez sur le bouton Nouveau dans le coin supérieur gauche du portail Azure. À l’écran Nouveau, saisissez *Web PubSub* dans la zone de recherche et appuyez sur Entrée. (Vous pouvez également effectuer une recherche dans Azure Web PubSub à partir de la catégorie `Web`.)

:::image type="content" source="media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="Capture d’écran d’une recherche dans Azure Web PubSub depuis le portail.":::

2. Sélectionnez **Web PubSub** dans les résultats de la recherche, puis **Créer**.

3. Entrez les paramètres suivants.

    | Paramètre      | Description                                        |
    | ------------ | -------------------------------------------------- |
    | **Nom de la ressource** | Nom unique au monde qui identifie votre nouvelle instance de service Web PubSub. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Abonnement Azure sous lequel cette instance de service Web PubSub est créée. | 
    | **[Groupe de ressources](../azure-resource-manager/management/overview.md)** |  Nom du groupe de ressources, nouveau ou existant, dans lequel créer l’instance de service Web PubSub. | 
    | **Lieu** | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. |
    | **Niveau tarifaire** | En savoir plus sur les [niveaux tarifaires du service Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub/). |
    | **Nombre d’unité** |  Le nombre d’unité spécifie le nombre de connexions que votre instance de service Web PubSub peut accepter. Chaque unité prend en charge 1 000 connexions simultanées au maximum. Ceci n’est configurable que dans le niveau Standard. |

:::image type="content" source="media/howto-develop-create-instance/create-web-pubsub-instance-in-portal.png" alt-text="Capture d’écran illustrant la création d’une instance Azure Web PubSub dans le portail.":::

4. Sélectionnez **Créer** pour commencer le déploiement de l’instance de service Web PubSub.

## <a name="create-azure-web-pubsub-instance-with-azure-cli"></a>Créer une instance Azure Web PubSub avec Azure CLI

L’[interface de ligne de commande Azure (Azure CLI)](/cli/azure) est un ensemble de commandes utilisées pour créer et gérer des ressources Azure. L’interface Azure CLI est disponible dans les services Azure et est conçu pour vous permettre d’utiliser rapidement Azure en mettant l’accent sur l’automatisation. Vous pouvez également créer une instance de service Azure Web PubSub avec Azure CLI après la disponibilité générale.