---
title: Système DNS personnalisé d’instance gérée
description: Cette rubrique détaille les options de configuration d’un DNS personnalisé avec Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247076"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurer un DNS personnalisé pour Azure SQL Database Managed Instance

Une instance managée Azure SQL Database doit être déployée au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Quelques scénarios (comme le courrier de base de données,des serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride) nécessitent des noms d’hôte privés pour être résolus depuis Managed Instance. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. 

Comme Managed Instance utilise le même DNS pour ses opérations internes, configurez le serveur DNS personnalisé pour qu’il puisse résoudre les noms de domaine public.

> [!IMPORTANT]
> Utilisez toujours un nom de domaine complet (FQDN) pour les serveurs de messagerie, l’instance SQL Server et les autres services, même s’ils se trouvent dans votre zone DNS privée. Par exemple, utilisez `smtp.contoso.com` comme serveur de messagerie, car `smtp` ne sera pas résolu correctement. La création d’un serveur lié ou d’une réplication qui référence des machines virtuelles SQL sur le même réseau virtuel nécessite également un nom de domaine complet et un suffixe DNS par défaut. Par exemple : `SQLVM.internal.cloudapp.net`. Pour plus d’informations, consultez [Résolution de noms utilisant votre propre serveur DNS](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> La mise à jour des serveurs DNS du réseau virtuel n’affectera pas Managed Instance immédiatement. La configuration DNS de Managed Instance est mise à jour après l’expiration du bail DHCP ou après la mise à niveau de la plateforme, selon l’événement qui se produit en premier. **Les utilisateurs sont invités à définir la configuration DNS de leur réseau virtuel avant de créer leur première instance Managed Instance.**

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md).
- Pour plus d’informations sur la configuration d’un réseau virtuel pour une option Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
