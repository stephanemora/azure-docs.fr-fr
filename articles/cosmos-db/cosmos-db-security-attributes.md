---
title: Attributs de sécurité pour Azure Cosmos DB
description: Une liste de vérification des attributs de sécurité pour l’évaluation d’Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480467"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Attributs de sécurité pour Azure Cosmos DB

Cet article décrit les attributs de sécurité courantes intégrées à Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Préventif

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Chiffrement au repos (par exemple, le chiffrement côté serveur, le chiffrement côté serveur avec des clés gérées par le client et d’autres fonctionnalités de chiffrement) | Oui | Toutes les bases de données Cosmos DB et les sauvegardes sont chiffrées par défaut ; consultez [chiffrement des données dans Azure Cosmos DB](database-encryption-at-rest.md). Chiffrement côté serveur avec des clés gérées par le client n’est pas pris en charge. |
| Chiffrement en Transit (telles que le chiffrement de ExpressRoute, de cryptage de réseau virtuel et le cryptage de réseau)| Oui | Toutes les données d’Azure Cosmos DB sont chiffrées en transit. |
| Gestion des clés de chiffrement (CMK, BYOK, etc.)| Non |  |
| Chiffrement au niveau des colonnes (Azure Data Services)| Oui | Uniquement dans les Tables API Premium. Pas toutes les API prennent en charge cette fonctionnalité. Consultez [présentation d’Azure Cosmos DB : API table](table-introduction.md). |
| Appels d’API chiffrés| Oui | Toutes les connexions à Azure Cosmos DB prend en charge HTTPS. Azure Cosmos DB prend également en charge les connexions TLS 1.2, mais cela n’est pas encore appliqué. Si les clients désactiver TLS de niveau inférieur de leur côté, ils peuvent garantir pour vous connecter à Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentation du réseau

| Attribut de sécurité | Oui/non | Notes |
|---|---|--|
| Prise en charge du point de terminaison de service| Oui |  |
| Prise en charge de l’injection de réseau virtuel| Oui | Avec le point de terminaison du service réseau virtuel, vous pouvez configurer un compte Azure Cosmos DB pour autoriser l’accès uniquement à partir d’un sous-réseau spécifique d’un réseau virtuel (VNet). Vous pouvez également combiner l’accès au réseau virtuel avec des règles de pare-feu.  Consultez [accès Azure Cosmos DB à partir de réseaux virtuels](vnet-service-endpoint.md). |
| Prise en charge de l’isolement réseau et Firewalling| Oui | Avec prise en charge du pare-feu, vous pouvez configurer votre compte Azure Cosmos pour autoriser l’accès uniquement à partir d’un ensemble d’adresses IP, une plage d’adresses IP approuvés et/ou de services cloud. Consultez [pare-feu IP configurer dans Azure Cosmos DB](how-to-configure-firewall.md).|
| Prise en charge du tunneling forcé | Oui | Peut être configuré sur le côté client sur le réseau virtuel où se trouvent les machines virtuelles.   |

## <a name="detection"></a>Détection

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Prise en charge (analytique de journal, application insights, etc.) de surveillance Azure| Oui | Toutes les demandes sont envoyées à Azure Cosmos DB sont enregistrés. [Surveillance Azure](../azure-monitor/overview.md), métriques Azure, l’enregistrement d’Audit Azure sont pris en charge.  Vous pouvez consigner les informations correspondant à des demandes de plan de données, les statistiques d’exécution de requête, le texte de requête, les demandes MongoDB. Vous pouvez également configurer des alertes. |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Authentication| Oui | Oui au niveau du compte de base de données ; au niveau du plan de données, Cosmos DB utilise des jetons de ressource et l’accès aux clés. |
| Authorization| Oui | Prise en charge sur le compte Azure Cosmos avec des clés principales (primaire et secondaire) et les jetons de ressource. Vous pouvez obtenir en lecture/écriture ou l’accès en lecture seule aux données avec des clés principales. Les jetons de ressource autoriser l’accès de durée limitée aux ressources telles que des documents et des conteneurs. |


## <a name="audit-trail"></a>Piste d'audit

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Journalisation et audit du plan de gestion/contrôle| Oui | Journal d’activité Azure pour les opérations au niveau de compte tels que les pare-feux, réseaux virtuels, l’accès aux clés et IAM. |
| Journalisation et audit du plan de données | Oui | Diagnostics de surveillance telles que la journalisation pour les opérations au niveau du conteneur créer conteneur, approvisionner le débit, l’indexation de stratégies et les opérations CRUD sur des documents. |

## <a name="configuration-management"></a>Gestion des configurations

| Attribut de sécurité | Oui/Non | Notes|
|---|---|--|
| Prise en charge de configuration management (gestion des versions de configuration, etc.).| Non  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Attributs de sécurité supplémentaire pour Cosmos DB

| Attribut de sécurité | Oui/non | Notes|
|---|---|--|
| Cross-Origin Resource Sharing (CORS) | Oui | Consultez [configurer Cross-Origin Resource Sharing (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
