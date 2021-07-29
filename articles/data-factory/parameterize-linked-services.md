---
title: Paramétrer les services liés dans Azure Data Factory
description: Découvrez comment paramétrer des services liés dans Azure Data Factory et transmettre des valeurs dynamiques au moment de l’exécution.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/01/2021
author: chez-charlie
ms.author: chez
ms.openlocfilehash: 277f3d9e9d82edf9e93d41808a351528a94f85d7
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793692"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Paramétrer les services liés dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez désormais paramétrer un service lié et transmettre des valeurs dynamiques au moment de l’exécution. Par exemple, si vous souhaitez vous connecter à différentes bases de données sur le même serveur SQL logique, vous pouvez désormais paramétrer le nom de la base de données dans la définition du service lié. Ceci vous évite d’avoir à créer un service lié pour chaque base de données sur le serveur SQL logique. Vous pouvez également paramétrer les autres propriétés de la définition du service lié : par exemple, *Nom d’utilisateur.*

Vous pouvez utiliser l’interface utilisateur de Data Factory dans le portail Azure ou une interface de programmation pour paramétrer les services liés.

> [!TIP]
> Nous vous recommandons de ne pas paramétrer les mots de passe ou les secrets. Au lieu de cela, conservez toutes les chaînes de connexion dans Azure Key Vault et paramétrez le *Nom du secret*.

> [!Note]
> Il existe un bogue ouvert en cas d’utilisation du caractère « - » dans les noms de paramètres. Nous vous recommandons d’utiliser des noms sans « - » tant que le bogue n’a pas été résolu.

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Types de services liés pris en charge

Tous les types de service liés sont pris en charge pour le paramétrage.

**Pris en charge nativement par l’IU ADF :** Lors de la création d'un service lié sur l'interface utilisateur, Data Factory fournit une expérience de paramétrage intégrée pour les types de services liés suivants. Dans le panneau de création/modification de service lié, vous pouvez trouver des options pour les nouveaux paramètres et ajouter du contenu dynamique. Consultez [Expérience IU Data Factory](#data-factory-ui).

- Amazon Redshift
- Amazon S3
- Stockage compatible Amazon S3
- Stockage Blob Azure
- Azure Cosmos DB (API SQL)
- Azure Data Lake Storage Gen2
- Azure Database pour MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Synapse Analytics 
- Stockage de table Azure
- HTTP générique
- REST générique
- MySQL
- Oracle
- Oracle Cloud Storage
- SQL Server

**Création avancée :** Pour les autres types de services liés qui ne figurent pas dans la liste ci-dessus, le paramétrage peut être effectué en modifiant le JSON sur l'interface utilisateur :

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
