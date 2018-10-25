---
title: Paramétrer les services liés dans Azure Data Factory | Microsoft Docs
description: Découvrez comment paramétrer des services liés dans Azure Data Factory et transmettre des valeurs dynamiques au moment de l’exécution.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: 287dcdedede5cab575aa0b9a73ec3e122556dc93
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900721"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Paramétrer les services liés dans Azure Data Factory

Vous pouvez désormais paramétrer un service lié et transmettre des valeurs dynamiques au moment de l’exécution. Par exemple, si vous souhaitez vous connecter à différentes bases de données sur le même serveur Azure SQL Database, vous pouvez désormais paramétrer le nom de la base de données dans la définition du service lié. Cela vous évite d’avoir à créer un service lié pour chaque base de données sur le serveur Azure SQL Database. Vous pouvez également paramétrer les autres propriétés de la définition du service lié : par exemple, *Nom d’utilisateur.*

Vous pouvez utiliser l’interface utilisateur de Data Factory dans le portail Azure ou une interface de programmation pour paramétrer les services liés.

> [!TIP]
> Nous vous recommandons de ne pas paramétrer les mots de passe ou les secrets. Au lieu de cela, conservez toutes les chaînes de connexion dans Azure Key Vault et paramétrez le *Nom du secret*.

## <a name="supported-data-stores"></a>Magasins de données pris en charge

À ce stade, le paramétrage de service lié est pris en charge dans l’interface utilisateur de Data Factory dans le portail Azure pour les magasins de données suivants. Pour tous les autres magasins de données, vous pouvez paramétrer le service lié en sélectionnant l’icône **Code** dans l’onglet du pipeline et à l’aide de l’éditeur JSON.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database pour MySQL

## <a name="data-factory-ui"></a>IU de la fabrique de données

![Ajouter du contenu dynamique à la définition du service lié](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Créer un paramètre](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
