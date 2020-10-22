---
title: Paramétrer les services liés dans Azure Data Factory
description: Découvrez comment paramétrer des services liés dans Azure Data Factory et transmettre des valeurs dynamiques au moment de l’exécution.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/16/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 1bb523c1b5aa595a714930962bbbe4e7e04de517
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131392"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Paramétrer les services liés dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez désormais paramétrer un service lié et transmettre des valeurs dynamiques au moment de l’exécution. Par exemple, si vous souhaitez vous connecter à différentes bases de données sur le même serveur SQL logique, vous pouvez désormais paramétrer le nom de la base de données dans la définition du service lié. Ceci vous évite d’avoir à créer un service lié pour chaque base de données sur le serveur SQL logique. Vous pouvez également paramétrer les autres propriétés de la définition du service lié : par exemple, *Nom d’utilisateur.*

Vous pouvez utiliser l’interface utilisateur de Data Factory dans le portail Azure ou une interface de programmation pour paramétrer les services liés.

> [!TIP]
> Nous vous recommandons de ne pas paramétrer les mots de passe ou les secrets. Au lieu de cela, conservez toutes les chaînes de connexion dans Azure Key Vault et paramétrez le *Nom du secret*.

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Magasins de données pris en charge

Vous pouvez paramétrer n’importe quel type de service lié.
Lors de la création d’un service lié sur l’interface utilisateur, Data Factory fournit une expérience de paramétrage intégrée pour les types de connecteurs suivants. Dans le panneau de création/modification de service lié, vous pouvez trouver des options pour les nouveaux paramètres et ajouter du contenu dynamique.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (API SQL)
- Azure Database pour MySQL
- Azure SQL Database
- Azure Synapse Analytics (anciennement SQL DW)
- MySQL
- Oracle
- SQL Server
- HTTP générique
- REST générique

Pour les autres types, vous pouvez paramétrer le service lié en modifiant le JSON sur l’interface utilisateur :

- Dans le panneau de création/modification du service lié -> développez « Avancé » en bas -> activez la case à cocher « Spécifier un contenu dynamique au format JSON » -> spécifiez la charge utile du JSON du service lié. 
- Ou bien, après avoir créé un service lié sans paramétrage, dans [Hub de gestion](author-visually.md#management-hub) -> Services liés-> recherchez le service lié spécifique -> cliquez sur « Code » (bouton « {} ») pour modifier le JSON. 

Pour ajouter une section ` parameters` afin de définir des paramètres et de référencer le paramètre à l’aide de ` @{linkedService().paraName} `, reportez-vous à l’[exemple de JSON](#json).

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
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
