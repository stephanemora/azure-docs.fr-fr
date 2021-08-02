---
title: Liste des ports pour Azure FXT Edge Filer
description: Liste des ports TCP/UDP utilisés par les environnements de cluster FXT
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 5067e60a45025ae6c37af63df4e1392e9ae52709
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111358468"
---
# <a name="required-network-ports"></a>Ports réseau requis

Cette liste affiche les ports TCP/UDP requis par les environnements de cluster FXT. Configurez les pare-feu que vous utilisez pour vous assurer que ces ports sont accessibles.

Les exigences spécifiques de votre système varient en fonction du type de stockage principal que vous utilisez.

Pour plus d’informations, contactez le Support technique de Microsoft.

## <a name="api-ports"></a>Ports d’API

| Sens | Type | Numéro de port | Protocol |
|-----------|------|-------------|----------|
| Trafic entrant   | TCP  | 22          | SSH      |
| Règle de trafic sortant  | TCP  | 80          | HTTP     |
| Entrant et sortant  | TCP  | 443         | HTTPS    |

## <a name="nfs-ports"></a>Ports NFS

Ports NFS entrants :

| Type    | Numéro de port | Service  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |
| TCP/UDP | 4045        | NLOCKMGR |
| TCP/UDP | 4046        | MOUNTD   |
| TCP/UDP | 4047        | STATUT   |

Ports NFS sortants :

| Type    | Numéro de port | Service  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |

Le trafic des ports NFS sortants varie selon le type de stockage utilisé comme système de stockage principal. (Certains systèmes n’utilisent pas le port 2049, bien qu’il soit assez courant pour être répertorié ici. Tous les clusters ont besoin d’un accès sur le port 111.) Consultez la documentation de vos systèmes de stockage ou utilisez la technique de requête décrite ci-dessous dans la section [Exigences supplémentaires relatives aux ports](#additional-port-requirements).

Certains trafics NFS sortants des nœuds FXT utilisent des ports éphémères. Le trafic FXT sortant au-dessus des ports connus ne doit pas être limité au niveau du transport.

## <a name="smb-ports"></a>Ports SMB

| Sens | Type | Numéro de port | Protocol |
|-----------|------|-------------|----------|
| Entrant et sortant  | UDP  | 137         | NetBIOS  |
| Trafic entrant   | UDP  | 138         | NetBIOS  |
| Entrant et sortant  | TCP  | 139         | SMB      |
| Entrant et sortant  | TCP  | 445         | SMB      |

<!--| Outbound  | UDP  | 137         | NetBIOS  | 
| Outbound  | TCP  | 139         | SMB      |
| Outbound  | TCP  | 445         | SMB      |
-->

## <a name="general-traffic-ports"></a>Ports de trafic généraux

| Sens | Type    | Numéro de port | Protocol |
|-----------|---------|-------------|----------|
| Règle de trafic sortant  | TCP/UDP | 53          | DNS      |
| Règle de trafic sortant  | TCP/UDP | 88          | Kerberos |
| Règle de trafic sortant  | UDP     | 123         | NTP      |
| Règle de trafic sortant  | TCP/UDP | 389         | LDAP     |
| Règle de trafic sortant  | TCP     | 686         | LDAPS    |

## <a name="additional-port-requirements"></a>Exigences supplémentaires relatives aux ports

Vos systèmes de stockage principaux peuvent nécessiter l’accès à des ports supplémentaires. Cette exigence varie selon le type de stockage utilisé.

Vous pouvez utiliser la commande `rpcinfo` pour savoir quels ports sont utilisés par un serveur particulier. Exécutez cette commande à partir d’un système client qui n’est pas associé à un pare-feu :

`rpcinfo -p <server_IP_address>`
