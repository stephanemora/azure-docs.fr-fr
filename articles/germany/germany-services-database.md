---
title: Services de base de données Azure Germany | Microsoft Docs
description: Cet article fournit une comparaison des services de base de données SQL pour Azure Allemagne.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 6316c381a601d1a28c3f6ecf529b3d31526bfd45
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2020
ms.locfileid: "122531342"
---
# <a name="azure-germany-database-services"></a>Services de base de données Azure Germany

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="sql-database"></a>Base de données SQL
Azure SQL Database et Azure SQL Managed Instance v12 sont généralement disponibles dans la région Azure Allemagne. Pour obtenir de l’aide sur la configuration de visibilité des métadonnées, ainsi que les meilleures pratiques de protection, consultez le [Centre de sécurité Microsoft pour le moteur de base de données SQL ](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database) ainsi que la [documentation publique d’Azure SQL Database](../azure-sql/database/index.yml) et la [documentation publique de SQL Managed Instance](../azure-sql/managed-instance/index.yml).

### <a name="variations"></a>Variantes
L’adresse de SQL Database dans Azure Germany est différente de celle dans Azure global :

| Type de service | Azure global | Azure Allemagne |
| --- | --- | --- |
| Base de données SQL | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Cache Azure pour Redis
Pour plus d’informations sur le Cache Azure pour Redis et comment l’utiliser, consultez [Documentation générale du cache Azure pour Redis](../azure-cache-for-redis/index.yml).

### <a name="variations"></a>Variantes
Les URL d’accès et de gestion du Cache Azure pour Redis dans Azure Germany sont différents des URL dans Azure global :

| Type de service | Azure global | Azure Allemagne |
| --- | --- | --- |
| Point de terminaison du cache | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Portail Azure | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> Tous vos scripts et codes doivent prendre en compte les points de terminaison et environnements appropriés. Pour plus d’informations, consultez « Se connecter à Microsoft Azure Germany » dans [Gérer le Cache Azure pour Redis avec Azure PowerShell](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md).
>
>


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, abonnez-vous au [blog Azure Allemagne](/archive/blogs/azuregermany/).