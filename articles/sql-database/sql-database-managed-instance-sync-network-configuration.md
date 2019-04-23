---
title: Azure App Service - synchroniser la configuration de réseau | Microsoft Docs
description: Cet article explique comment synchroniser la configuration de votre réseau pour le plan d’hébergement Azure App Service.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797269"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synchroniser la configuration de mise en réseau pour le plan d’hébergement Azure App Service

Il peut arriver que, bien que [votre application soit intégrée à un réseau virtuel Azure](../app-service/web-sites-integrate-with-vnet.md), vous ne pouvez pas établir de connexion vers une Instance gérée. Vous pouvez par exemple essayer d’actualiser la configuration de mise en réseau pour votre plan de service.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synchroniser la configuration de réseau pour le plan d’hébergement Azure App Service

Pour ce faire, procédez comme suit :  

1. Accédez à votre plan App Service des applications Web.

   ![plan app service](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Cliquez sur **Mise en réseau** puis cliquez sur **Cliquez ici pour gérer**.

   ![gérer le plan de service](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Sélectionnez votre **Réseau virtuel** et cliquez sur **Synchroniser le réseau**.

   ![synchroniser le réseau](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Attendez la fin de la synchronisation.
  
   ![synchronisation terminée](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Vous êtes maintenant prêt à tenter de rétablir votre connexion vers votre Instance gérée.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la configuration de votre réseau virtuel pour Managed Instance, consultez [Architecture de réseau virtuel Managed Instance](sql-database-managed-instance-connectivity-architecture.md) et [Guide pratique pour configurer un réseau virtuel existant](sql-database-managed-instance-configure-vnet-subnet.md).
