---
title: Services de base de données Azure Germany | Microsoft Docs
description: Fournit une comparaison des services de base de données pour Azure Germany
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 45e058d887ae79164196ab310f93270accbfb389
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033496"
---
# <a name="azure-germany-database-services"></a>Services de base de données Azure Germany
## <a name="sql-database"></a>Base de données SQL
aZURE SQL Database V12 est mis à la disposition générale dans Azure Germany. Pour d’autre conseils sur la configuration de visibilité des métadonnées, ainsi que des bonnes pratiques de protection, consultez le [Centre de sécurité Microsoft pour le moteur de base de données SQL ](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database) et la [documentation publique d’Azure SQL Database](../sql-database/index.yml).

### <a name="variations"></a>Variantes
L’adresse de SQL Database dans Azure Germany est différente de celle dans Azure global :

| Type de service | Azure global | Azure Germany |
| --- | --- | --- |
| Base de données SQL | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Cache Azure pour Redis
Pour plus d’informations sur le Cache Azure pour Redis et comment l’utiliser, consultez [Documentation générale du cache Azure pour Redis](../azure-cache-for-redis/index.md).

### <a name="variations"></a>Variantes
Les URL d’accès et de gestion du Cache Azure pour Redis dans Azure Germany sont différents des URL dans Azure global :

| Type de service | Azure global | Azure Germany |
| --- | --- | --- |
| Point de terminaison du cache | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Portail Azure | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> Tous vos scripts et codes doivent prendre en compte les points de terminaison et environnements appropriés. Pour plus d’informations, consultez « Se connecter à Microsoft Azure Germany » dans [Gérer le Cache Azure pour Redis avec Azure PowerShell](../azure-cache-for-redis/cache-howto-manage-redis-cache-powershell.md).
>
>


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, abonnez-vous au [blog Azure Germany](https://blogs.msdn.microsoft.com/azuregermany/).
