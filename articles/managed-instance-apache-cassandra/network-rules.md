---
title: Règles de réseau sortant requises pour Azure Managed Instance pour Apache Cassandra
description: En savoir plus sur les règles de réseau sortant requises et les noms de domaine complets pour Azure Managed Instance pour Apache Cassandra.
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 05/21/2021
ms.author: chrande
ms.openlocfilehash: 7b9e7463811d4bd5cd092828759487557bab50b0
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122563483"
---
# <a name="required-outbound-network-rules"></a>Règles de trafic réseau sortant requises

> [!IMPORTANT]
> Azure Managed Instance pour Apache Cassandra est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le service Azure Managed Instance pour Apache Cassandra requiert certaines règles de réseau pour gérer correctement le service. En vous assurant que les règles appropriées sont exposées, vous pouvez sécuriser votre service et prévenir les problèmes opérationnels.

## <a name="virtual-network-service-tags"></a>Balises de service du réseau virtuel

Si vous utilisez le pare-feu Azure pour limiter l’accès sortant, nous vous recommandons vivement d’utiliser des [étiquettes de service de réseau virtuel](../virtual-network/service-tags-overview.md). Voici les étiquettes requises pour qu’Azure Managed Instance pour Apache Cassandra fonctionne correctement.

| Étiquette du service de destination                                                             | Protocol | Port    | Utilisation  |
|----------------------------------------------------------------------------------|----------|---------|------|
| Stockage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle|
| AzureKeyVault | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Azure Key Vault. Les certificats et les clés sont utilisés pour sécuriser la communication à l’intérieur du cluster|
| Event Hub | HTTPS | 443 | Requis pour transférer des journaux vers Azure |
| AzureMonitor | HTTPS | 443 | Requis pour transférer des métriques vers Azure |
| AzureActiveDirectory| HTTPS | 443 | Obligatoire pour l’authentification Azure Active Directory.|
| GuestAndHybridManagement | HTTPS | 443 |  Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer) |
| ApiManagement  | HTTPS | 443 | Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer) |
| `Storage.<Region>`  | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle **Vous avez besoin d’une entrée pour chaque région dans laquelle vous avez déployé un centre de données.** |


## <a name="azure-global-required-network-rules"></a>Règles de réseau requises pour Azure Global

Si vous n’utilisez pas le pare-feu Azure, les règles de réseau et les dépendances d’adresse IP requises sont les suivantes :

| Point de terminaison de destination                                                             | Protocol | Port    | Utilisation  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap`<region>`.blob.core.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Storage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle|
|*.store.core.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Storage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour la communication et la configuration du plan de contrôle|
|*.blob.core.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Storage | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Stockage Azure pour stocker les sauvegardes. *La fonctionnalité de sauvegarde est en cours de révision et le nom du stockage suivra un modèle par disponibilité générale*|
|vmc-p-`<region>`.vault.azure.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) - Azure KeyVault | HTTPS | 443 | Requis pour une communication sécurisée entre les nœuds et Azure Key Vault. Les certificats et les clés sont utilisés pour sécuriser la communication à l’intérieur du cluster|
|management.azure.com:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Virtual Machine Scale Sets/API de gestion Azure | HTTPS | 443 | Requis pour collecter des informations sur les nœuds Cassandra et les gérer (par exemple, redémarrer)|
|*.servicebus.windows.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) - Azure EventHub | HTTPS | 443 | Requis pour transférer des journaux vers Azure|
|jarvis-west.dc.ad.msft.net:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure Monitor | HTTPS | 443 | Requis pour transférer des métriques Azure |
|login.microsoftonline.com:443</br> ou</br> [Étiquette de service](../virtual-network/service-tags-overview.md#available-service-tags) : Azure AD | HTTPS | 443 | Obligatoire pour l’authentification Azure Active Directory.|
| packages.microsoft.com | HTTPS | 443 | Requis pour les mises à jour des définitions et des signatures d’analyseur de la sécurité Azure |

## <a name="managed-instance-for-apache-cassandra-internal-port-usage"></a>Utilisation des ports internes de Managed Instance pour Apache Cassandra

Les ports suivants sont accessibles uniquement au sein du réseau virtuel (ou réseaux virtuels associés/connexions ExpressRoute). Les instances Managed Instance pour Apache Cassandra n’ont pas d’IP publique et ne doivent pas être rendues accessibles sur Internet.

| Port | Utilisation |
| ---- | --- |
| 8443 | Interne |
| 9443 | Interne |
| 7001 | Gossip. Utilisé par les nœuds Cassandra pour communiquer entre eux |
| 9042 | Cassandra. Utilisé par les clients pour se connecter à Cassandra |
| 7199 | Interne |



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les règles de réseau pour gérer correctement le service. Explorez plus en détail Azure Managed Instance pour Apache Cassandra avec les articles suivants :

* [Vue d’ensemble d’Azure Managed Instance pour Apache Cassandra](introduction.md)
* [Gérer les ressources Azure Managed Instance pour Apache Cassandra à l’aide d’Azure CLI](manage-resources-cli.md)