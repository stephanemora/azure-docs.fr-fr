---
title: DNS personnalisé d’Azure SQL Database Managed Instance | Microsoft Docs
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
ms.openlocfilehash: 588fac1fc48396584188eec44f21a7005dc8ed96
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567560"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurer un DNS personnalisé pour Azure SQL Database Managed Instance

Une instance managée Azure SQL Database doit être déployée au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Quelques scénarios (comme le courrier de base de données,des serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride) nécessitent des noms d’hôte privés pour être résolus depuis Managed Instance. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. 

Comme Managed Instance utilise le même DNS pour ses opérations internes, vous devez configurer le serveur DNS personnalisé pour qu’il puisse résoudre les noms de domaine public.

   > [!IMPORTANT]
   > Utilisez toujours des noms de domaine complets (FQDN) pour les serveurs de messagerie, les serveurs SQL et les autres services même s’ils se trouvent dans votre zone DNS privée. Par exemple, utilisez `smtp.contoso.com` pour le serveur de messagerie, car le simple nom `smtp` ne sera pas correctement résolu.

   > [!IMPORTANT]
   > La mise à jour des serveurs DNS du réseau virtuel n’affecte pas Managed Instance immédiatement. La configuration DNS de Managed Instance est mise à jour après l’expiration du bail DHCP ou après la mise à niveau de la plateforme, selon l’événement qui se produit en premier. **Les utilisateurs sont invités à définir la configuration DNS de leur réseau virtuel avant de créer leur première instance Managed Instance.**

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md).
- Pour plus d’informations sur la configuration d’un réseau virtuel pour une option Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
