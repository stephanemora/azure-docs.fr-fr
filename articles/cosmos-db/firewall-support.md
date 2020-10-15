---
title: Pare-feu IP pour les comptes Azure Cosmos
description: Découvrez comment sécuriser les données Azure Cosmos DB en utilisant des stratégies de contrôle d’accès IP pour la prise en charge du pare-feu.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "66241077"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Pare-feu IP dans Azure Cosmos DB

Pour sécuriser les données dans votre compte, Azure Cosmos DB assure la prise en charge d’un modèle d’autorisation basé sur une clé secrète, qui utilise un code d’authentification de message basé sur le hachage (HMAC) à forte intégrité. De plus, Azure Cosmos DB prend en charge les contrôles d’accès basés sur IP pour le pare-feu entrant. Ce modèle est semblable aux règles de pare-feu d’un système de base de données classique et renforce la sécurité de votre compte. Avec des pare-feu, vous pouvez configurer un compte Cosmos pour qu’il soit accessible uniquement à partir d’un ensemble d’ordinateurs et/ou de services cloud approuvés. L’accès aux données stockées dans votre base de données Azure Cosmos à partir de ces ensembles d’ordinateurs et de services approuvés nécessite toujours que l’appelant présente un jeton d’autorisation valide.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Vue d’ensemble du contrôle d’accès IP

Par défaut, votre compte Azure Cosmos est accessible depuis Internet, tant que la demande est accompagnée d’un jeton d’autorisation valide. Pour configurer le contrôle d’accès basé sur la stratégie IP, l’utilisateur doit fournir le jeu d’adresses IP ou des plages d’adresses IP au format CIDR (Classless Inter-Domain Routing) pour être ajouté à la liste d’adresses IP clientes autorisées pour un accès à un compte Azure Cosmos donné. Une fois cette configuration appliquée, toutes les demandes provenant d'ordinateurs qui ne figurent pas sur cette liste autorisée reçoivent une réponse 403 (Interdit). Lorsque vous utilisez un pare-feu IP, il est recommandé d’autoriser le portail Azure à accéder à votre compte. L’accès est requis pour autoriser l’utilisation de l’Explorateur de données ainsi que pour récupérer des métriques pour votre compte qui s’affichent sur le portail Azure. Lorsque vous utilisez l’explorateur de données, en plus de permettre au portail Azure d'accéder à votre compte, vous devez mettre à jour vos paramètres de pare-feu pour ajouter votre adresse IP actuelle aux règles de pare-feu. Notez que la propagation des modifications apportées au pare-feu peut prendre jusqu'à 15 minutes. 

Vous pouvez combiner des pare-feu IP avec le contrôle d’accès de réseau virtuel et de sous-réseau. En les combinant, vous pouvez limiter l’accès à n’importe quelle source qui a une adresse IP publique et/ou à partir d’un sous-réseau spécifique au sein du réseau virtuel. Pour en savoir plus sur l’utilisation du contrôle d’accès basé sur le sous-réseau et le réseau virtuel, consultez [Accéder aux ressources Azure Cosmos DB à partir de réseaux virtuels](vnet-service-endpoint.md).

Pour résumer, le jeton d’autorisation est toujours requis pour accéder à un compte Azure Cosmos. Si le pare-feu IP et la liste de contrôle d’accès (ACL) de réseau virtuel ne sont pas configurés, le compte Azure Cosmos est accessible avec le jeton d’autorisation. Une fois que le pare-feu IP ou la liste de contrôle d’accès (ACL) de réseau virtuel ou les deux sont configurés sur le compte Azure Cosmos, seules les demandes provenant des sources que vous avez spécifiées (et avec le jeton d’autorisation) obtiennent des réponses valides. 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite configurer le pare-feu IP ou le point de terminaison de service de réseau virtuel pour votre compte à l’aide de la documentation suivante :

* [Comment configurer le pare-feu IP de votre compte Azure Cosmos](how-to-configure-firewall.md)
* [Accéder aux ressources Azure Cosmos DB à partir de réseaux virtuels](vnet-service-endpoint.md)
* [Configurer un point de terminaison de service de réseau virtuel pour votre compte Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




