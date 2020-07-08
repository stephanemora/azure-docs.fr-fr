---
title: Synchroniser la configuration réseau pour Azure App Service
titleSuffix: Azure SQL Managed Instance
description: Cet article explique comment synchroniser la configuration de votre réseau pour le plan d’hébergement Azure App Service avec votre instance Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695451"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Synchroniser la configuration de mise en réseau pour le plan d’hébergement Azure App Service avec Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Il peut arriver que, bien que [votre application soit intégrée à un réseau virtuel Azure](../../app-service/web-sites-integrate-with-vnet.md), vous ne pouvez pas établir de connexion vers SQL Managed Instance. L’actualisation, ou synchronisation, de la configuration de mise en réseau de votre plan de service peut résoudre ce problème. 

## <a name="sync-network-configuration"></a>Synchroniser la configuration du réseau 

Pour ce faire, procédez comme suit :  

1. Accédez à votre plan App Service des applications Web.

   ![Capture d’écran du plan App Service](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Sélectionnez **Mise en réseau**, puis **Cliquez ici pour gérer**.

   ![Capture d’écran de la gestion du plan de service](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Sélectionnez votre **Réseau virtuel** et cliquez sur **Synchroniser le réseau**.

   ![Capture d’écran de la synchronisation du réseau](./media/azure-app-sync-network-configuration/sync.png)

4. Attendez la fin de la synchronisation.
  
   ![Capture d’écran de la synchronisation terminée](./media/azure-app-sync-network-configuration/sync-done.png)

Vous êtes maintenant prêt à tenter de rétablir votre connexion vers votre instance SQL Managed Instance.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la configuration de votre réseau virtuel pour SQL Managed Instance, consultez [Architecture de réseau virtuel SQL Managed Instance](connectivity-architecture-overview.md) et [Guide pratique pour configurer un réseau virtuel existant](vnet-existing-add-subnet.md).
