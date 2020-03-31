---
title: Découvrir le point de terminaison de gestion des instances managées
description: Découvrir la façon d’obtenir l’adresse IP publique du point de terminaison de gestion Azure SQL Database Managed Instance et de vérifier la protection de son pare-feu intégré
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825714"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Déterminer l’adresse IP du point de terminaison de gestion

Le cluster virtuel Azure SQL Database Managed Instance contient un point de terminaison de gestion que Microsoft utilise pour les opérations de gestion. Le point de terminaison de gestion est protégé par un pare-feu intégré au niveau du réseau et par la vérification de certificat mutuelle au niveau de l’application. Vous pouvez déterminer l’adresse IP du point de terminaison de gestion, mais vous ne pouvez pas accéder à ce dernier.

Pour déterminer l’adresse IP de gestion, effectuez une recherche DNS sur le nom de domaine complet de votre instance gérée : `mi-name.zone_id.database.windows.net`. Cette opération retourne une entrée DNS similaire à `trx.region-a.worker.vnet.database.windows.net`. Vous pouvez ensuite effectuer une recherche DNS sur ce nom de domaine complet avec la partie « . vnet » en moins. L’adresse IP de gestion est alors retournée. 

Cette commande PowerShell le fera pour vous si vous remplacez \<le nom de domaine complet de l’instance gérée\> par l’entrée DNS de votre instance gérée : `mi-name.zone_id.database.windows.net`.
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Pour plus d’information sur les instances managées et la connectivité, consultez [Architecture de la connectivité d’Azure SQL Database Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
