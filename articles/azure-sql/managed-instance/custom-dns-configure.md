---
title: Système DNS personnalisé
titleSuffix: Azure SQL Managed Instance
description: Cette rubrique détaille les options de configuration d’un DNS personnalisé avec Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831499"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Configurer un DNS personnalisé pour Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance doit être déployé au sein d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) Azure. Quelques scénarios (comme le courrier de base de données, les serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride) nécessitent des noms d’hôte privés pour être résolus depuis SQL Managed Instance. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. 

Étant donné que l’instance gérée SQL utilise le même DNS pour ses opérations internes, configurez le serveur DNS personnalisé pour qu’il puisse résoudre les noms de domaine public.

> [!IMPORTANT]
> Utilisez toujours un nom de domaine complet (FQDN) pour les serveurs de messagerie, l’instance SQL Server et les autres services, même s’ils se trouvent dans votre zone DNS privée. Par exemple, utilisez `smtp.contoso.com` comme serveur de messagerie, car `smtp` ne sera pas résolu correctement. La création d’un serveur lié ou d’une réplication qui référence des machines virtuelles SQL Server sur le même réseau virtuel nécessite également un nom de domaine complet et un suffixe DNS par défaut. Par exemple : `SQLVM.internal.cloudapp.net`. Pour plus d’informations, consultez [Résolution de noms utilisant votre propre serveur DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> La mise à jour des serveurs DNS du réseau virtuel n’affectera pas SQL Managed Instance immédiatement. Pour plus de détails, consultez [Synchroniser le paramétrage des serveurs DNS du réseau virtuel sur le cluster virtuel SQL Managed Instance](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation d’Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Pour suivre un didacticiel vous expliquant comment créer une nouvelle instance gérée, consultez [Créer une instance gérée](instance-create-quickstart.md).
- Pour plus d’informations sur la configuration d’un réseau virtuel pour une option Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](connectivity-architecture-overview.md).
