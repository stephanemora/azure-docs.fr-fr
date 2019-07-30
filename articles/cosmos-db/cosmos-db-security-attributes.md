---
title: Attributs de sécurité pour Azure Cosmos DB
description: Liste de contrôle des attributs de sécurité pour l’évaluation d’Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480467"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Attributs de sécurité pour Azure Cosmos DB

Cet article documente les attributs de sécurité courants intégrés dans Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Chiffrement au repos (comme le chiffrement côté serveur, le chiffrement côté serveur avec clés managées par le client et d’autres fonctions de chiffrement) | Oui | Par défaut, toutes les bases de données et sauvegardes Cosmos DB sont chiffrées (voir [Chiffrement des données dans Azure Cosmos DB](database-encryption-at-rest.md)). Le chiffrement côté serveur avec des clés gérées par le client n’est pas pris en charge. |
| Chiffrement en transit (ExpressRoute, de réseau virtuel et de réseau virtuel à réseau virtuel)| Oui | Toutes les données Azure Cosmos DB sont chiffrées en transit. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Oui | Uniquement dans l’API Table Premium. Certaines API ne prennent pas en charge cette fonctionnalité. Voir [Présentation d’Azure Cosmos DB : API Table](table-introduction.md). |
| Appels d’API chiffrés| Oui | Toutes les connexions à Azure Cosmos DB prennent en charge le protocole HTTPS. Azure Cosmos DB prend également en charge les connexions utilisant le protocole TLS 1.2, mais celui-ci n’est pas encore appliqué. Si des clients désactivent une protocole TLS de niveau inférieur de leur côté, ils peuvent s’assurer de se connecter à Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/Non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| OUI |  |
| Prise en charge de l’injection de réseau virtuel| OUI | Avec un point de terminaison de service de réseau virtuel, vous pouvez configurer un compte Azure Cosmos DB pour n’autoriser l’accès qu’à partir d’un sous-réseau spécifique d’un réseau virtuel. Vous pouvez également combiner un accès à partir d’un réseau virtuel avec des règles de pare-feu.  Voir [Accéder à Azure Cosmos DB à partir de réseaux virtuels](vnet-service-endpoint.md). |
| Prise en charge de l’isolement réseau et de l’installation de pare-feu| OUI | Avec la prise en charge du pare-feu, vous pouvez configurer votre compte Azure Cosmos pour autoriser l’accès uniquement à partir d’un ensemble approuvé d’adresses IP, d’une plage d’adresses IP ou de services cloud. Voir [Configurer un pare-feu IP dans Azure Cosmos DB](how-to-configure-firewall.md).|
| Prise en charge du tunneling forcé | OUI | Peut être configuré côté client sur le réseau virtuel où se trouvent les machines virtuelles.   |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la supervision Azure (Log analytics, App insights, etc.)| OUI | Toutes les demandes envoyées à Azure Cosmos DB sont journalisées. Les journalisations d’[Azure Monitoring](../azure-monitor/overview.md), de Métriques Azure et d’Audit Azure sont prises en charge.  Vous pouvez journaliser des informations correspondant à des demandes de plan de données, des statistiques d’exécution de requête, du texte de requête et des demandes MongoDB. Vous pouvez également configurer des alertes. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Authentication| Oui | Oui au niveau du compte de base de données. Au niveau du plan de données, Cosmos DB utilise un accès à l’aide de jetons de ressource et de clés. |
| Authorization| Oui | Prise en charge au niveau du compte Azure Cosmos avec des clés principales (primaire et secondaire) et des jetons de ressource. Vous pouvez obtenir un accès en lecture/écriture ou en lecture seule aux données avec des clés principales. Les jetons de ressource autorisent un accès temporaire à des ressources telles que des documents et des conteneurs. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| OUI | Journal d’activité Azure pour des opérations au niveau d’un compte : pare-feu, réseaux virtuels, accès aux clés et IAM. |
| Journalisation et audit du plan de données | Oui | Journalisation de la surveillance des diagnostics pour les opérations au niveau conteneur. Par exemple, création de conteneur, approvisionnement de débit, stratégies d’indexation et opérations CRUD sur des documents. |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de la gestion de la configuration (gestion de version de la configuration, etc.)| Non  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Attributs de sécurité supplémentaires pour Cosmos DB

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Partage des ressources cross-origin (CORS) | OUI | Voir [Configurer le partage des ressources cross-origin (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
