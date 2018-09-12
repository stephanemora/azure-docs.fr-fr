---
title: DNS personnalisé d’Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique détaille les options de configuration d’un DNS personnalisé avec Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: be32bc4b3f91fbc71305ee9f16fec17ff472cd4c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049573"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurer un DNS personnalisé pour Azure SQL Database Managed Instance

Une instance Azure SQL Database Managed Instance (préversion) doit être déployée au sein d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md) Azure. Quelques scénarios, comme les serveurs liés à d’autres instances SQL dans votre cloud ou environnement hybride, nécessitent des noms d’hôte privés pour être résolus depuis l’instance gérée. Si tel est le cas, vous devez configurer un DNS personnalisé dans Azure. Comme Managed Instance utilise le même DNS pour ces tâches internes, la configuration du DNS du réseau virtuel doit être compatible avec Managed Instance. 

Pour rendre une configuration d’un DNS personnalisé compatible avec Managed Instance, vous devez : 
- Configurer un serveur DNS personnalisé afin qu’il puisse résoudre les noms de domaine publics 
- Placez l’adresse IP DNS du programme de résolution récursif d’Azure (168.63.129.16) à la fin de la liste DNS du réseau virtuel 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Paramétrage d’une configuration de serveurs DNS personnalisés

1. Dans le portail Azure, trouvez l’option DNS personnalisé pour votre réseau virtuel.

   ![option DNS personnalisé](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Passez sur Personnalisé et entrez l’adresse IP du serveur de votre DNS personnalisé, ainsi que l’adresse IP des programmes de résolution récursifs d’Azure, 168.63.129.16. 

   ![option DNS personnalisé](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Si vous ne renseignez pas le programme de résolution récursif d’Azure dans la liste DNS, Managed Instance renvoie un statut Défectueux. Pour récupérer de ce statut, vous devez créer une nouvelle instance dans un réseau virtuel avec les stratégies réseau conformes, créer des données de niveau d’instance et restaurer vos bases de données. Consultez l’article [Configuration de réseau virtuel](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de l’option Managed Instance](sql-database-managed-instance.md)
- Pour suivre un didacticiel vous expliquant comment créer une option Managed Instance, consultez [Créer une option Managed Instance](sql-database-managed-instance-get-started.md).
- Pour plus d’informations sur la configuration d’un réseau virtuel pour une option Managed Instance, consultez [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md).
