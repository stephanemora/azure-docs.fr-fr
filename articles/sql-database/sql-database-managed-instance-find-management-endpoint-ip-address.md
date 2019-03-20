---
title: Découvrir le point de terminaison de gestion Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrir la façon d’obtenir l’adresse IP publique du point de terminaison de gestion Azure SQL Database Managed Instance et de vérifier la protection de son pare-feu intégré
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 542db0200229b4fea4ac67ad19c468f2b9dc67d1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772777"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Déterminer l’adresse IP du point de terminaison de gestion

Le cluster virtuel Azure SQL Database Managed Instance contient un point de terminaison de gestion que Microsoft utilise pour les opérations de gestion. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau et par la vérification de certificat mutuelle au niveau de l’application. Vous pouvez déterminer l’adresse IP du point de terminaison de gestion, mais vous ne pouvez pas accéder à ce dernier.

## <a name="determine-ip-address"></a>Déterminer l’adresse IP

Supposons que l’hôte Managed Instance est `mi-demo.xxxxxx.database.windows.net`. Exécutez `nslookup` en utilisant le nom d’hôte.

![Résolution du nom d’hôte interne](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

À présent, effectuez un autre `nslookup` pour le nom mis en évidence en supprimant le segment `.vnet.`. Vous obtenez l’adresse IP publique lorsque vous exécutez cette commande.

![Résolution de l’adresse IP publique](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’information sur les instances gérées et la connectivité, consultez [Architecture de la connectivité d’Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
