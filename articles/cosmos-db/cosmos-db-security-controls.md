---
title: Contrôles de sécurité pour Azure Cosmos DB
description: Obtenir une check-list des contrôles de sécurité tels que le réseau, la supervision, l’identité et la protection des données pour évaluer Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: 1ac4d12f58977497642cdb0706ab7e85e9a4db64
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913091"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Contrôles de sécurité pour Azure Cosmos DB

Cet article décrit les contrôles de sécurité intégrés à Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Réseau

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge de l’injection de réseau virtuel| Oui | Avec un point de terminaison de service de réseau virtuel, vous pouvez configurer un compte Azure Cosmos DB pour n’autoriser l’accès qu’à partir d’un sous-réseau spécifique d’un réseau virtuel. Vous pouvez également combiner un accès à partir d’un réseau virtuel avec des règles de pare-feu. Pour en savoir plus, consultez [Accéder à Azure Cosmos DB à partir de réseaux virtuels](VNet-service-endpoint.md). |
| Prise en charge de l’isolement et du pare-feu réseau| Oui | Avec la prise en charge du pare-feu, vous pouvez configurer votre compte Azure Cosmos pour autoriser l’accès uniquement à partir d’un ensemble approuvé d’adresses IP, d’une plage d’adresses IP ou de services cloud. Pour en savoir plus, consultez [Configurer un pare-feu IP dans Azure Cosmos DB](how-to-configure-firewall.md).|
| Prise en charge du tunneling forcé| Oui | Peut être configuré côté client sur le réseau virtuel où se trouvent les machines virtuelles.   |

## <a name="monitoring--logging"></a>Supervision et journalisation

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| Oui | Toutes les demandes envoyées à Azure Cosmos DB sont journalisées. Les journalisations d’[Azure Monitoring](../azure-monitor/overview.md), de Métriques Azure et d’Audit Azure sont prises en charge.  Vous pouvez journaliser des informations correspondant à des demandes de plan de données, des statistiques d’exécution de requête, du texte de requête et des demandes MongoDB. Vous pouvez également configurer des alertes. |
| Journalisation et audit du plan de gestion et de contrôle| Oui | Journal d’activité Azure pour des opérations au niveau d’un compte : pare-feu, réseaux virtuels, accès aux clés et IAM. |
| Journalisation et audit du plan de données | Oui | Journalisation de la surveillance des diagnostics pour les opérations au niveau conteneur. Par exemple, création de conteneur, approvisionnement de débit, stratégies d’indexation et opérations CRUD sur des documents. |

## <a name="identity"></a>Identité

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Oui au niveau du compte de base de données. Au niveau du plan de données, Cosmos DB utilise un accès à l’aide de jetons de ressource et de clés. |
| Autorisation| Oui | Prise en charge au niveau du compte Azure Cosmos avec des clés principales (primaire et secondaire) et des jetons de ressource. Vous pouvez obtenir un accès en lecture/écriture ou en lecture seule aux données avec des clés principales. Les jetons de ressource autorisent un accès temporaire à des ressources telles que des documents et des conteneurs. |

## <a name="data-protection"></a>Protection de données

| Contrôle de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement côté serveur au repos : Clés managées par Microsoft | Oui | Par défaut, toutes les bases de données et sauvegardes Azure Cosmos sont chiffrées (voir [Chiffrement des données dans Azure Cosmos DB](database-encryption-at-rest.md)). |
| Chiffrement côté serveur au repos : clés gérées par le client (BYOK) | Oui | Consultez [Configurer des clés gérées par le client pour votre compte Azure Cosmos DB](how-to-setup-cmk.md)  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Oui | Uniquement dans l’API Table Premium. Certaines API ne prennent pas en charge cette fonctionnalité. Voir [Présentation d’Azure Cosmos DB : API Table](table-introduction.md). |
| Le chiffrement en transit (tel que le chiffrement ExpressRoute, le chiffrement dans un réseau virtuel, et le chiffrement de réseau virtuel à réseau virtuel)| Oui | Toutes les données Azure Cosmos DB sont chiffrées en transit. |
| Appels d’API chiffrés| Oui | Toutes les connexions à Azure Cosmos DB prennent en charge le protocole HTTPS. Azure Cosmos DB prend également en charge le protocole TLS 1.2.<br>Il est possible d’appliquer une version TLS minimale côté serveur. Pour ce faire, veuillez contacter [cosmosdbpm@microsoft.com](maito:cosmosdbpm@microsoft.com]). |

## <a name="configuration-management"></a>Gestion des configurations

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Non  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Contrôles de sécurité supplémentaires pour Cosmos DB

| Contrôle de sécurité | Oui/Non | Notes|
|---|---|--|
| Partage des ressources cross-origin (CORS) | Oui | Voir [Configurer le partage des ressources cross-origin (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en plus sur les [contrôles de sécurité intégrés des services Azure](../security/fundamentals/security-controls.md).