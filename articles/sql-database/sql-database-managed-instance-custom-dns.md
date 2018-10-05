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
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/23/2018
ms.openlocfilehash: f26ea763d48d03fe7e981b7abbbe64e573ec0b3a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224271"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurer un DNS personnalisé pour Azure SQL Database Managed Instance

Une instance gérée Azure SQL Database doit être déployée au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Quelques scénarios, comme les serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride, nécessitent des noms d’hôte privés pour être résolus depuis l’instance gérée. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. Comme Managed Instance utilise le même DNS pour ces tâches internes, la configuration du DNS du réseau virtuel doit être compatible avec Managed Instance. 

Pour rendre une configuration d’un DNS personnalisé compatible avec Managed Instance, vous devez : 
- Configurer un serveur DNS personnalisé afin qu’il puisse résoudre les noms de domaine publics 
- Placez l’adresse IP DNS du programme de résolution récursif d’Azure (168.63.129.16) à la fin de la liste DNS du réseau virtuel 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Paramétrage d’une configuration de serveurs DNS personnalisés

1. Dans le portail Azure, trouvez l’option DNS personnalisé pour votre réseau virtuel.

   ![option DNS personnalisé](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Passez sur Personnalisé et entrez l’adresse IP du serveur de votre DNS personnalisé, ainsi que l’adresse IP des programmes de résolution récursifs d’Azure, 168.63.129.16. 

   ![option DNS personnalisé](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Si vous ne définissez pas le programme de résolution récursif d’Azure dans la liste DNS, l’instance managée peut connaître une défaillance si les serveurs DNS personnalisés ne sont pas disponibles pour une raison quelconque. Pour récupérer de ce statut, vous devez créer une nouvelle instance dans un réseau virtuel avec les stratégies réseau conformes, créer des données de niveau d’instance et restaurer vos bases de données. Si vous définissez le programme de résolution récursif d’Azure comme la dernière entrée de la liste DNS, les noms publics continueront d’être résolus, même en cas de défaillance des serveurs DNS personnalisés. Consultez l’article [Configuration de réseau virtuel](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md).
- Pour plus d’informations sur la configuration d’un réseau virtuel pour une option Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md).
