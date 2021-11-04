---
title: Concepts de stratégies d’accès Azure Purview
description: Comprendre les stratégies d’accès Azure Purview
author: vlrodrig
ms.author: vlrodrig
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4d3d635b3f38f344ca0ae6cf89e16b0e6714073
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096486"
---
# <a name="concepts-for-azure-purview-data-access-policies"></a>Concepts pour les stratégies d’accès aux données Azure Purview

Cet article décrit les stratégies d’accès aux données Azure Purview qui vous permettent de gérer l’accès à votre patrimoine de données à partir d’Azure Purview.

> [!Note]
> Cette fonctionnalité diffère du contrôle d’accès pour Azure Purview, décrit dans [contrôle d’accès dans Azure Purview](catalog-permissions.md).

> [!IMPORTANT]
> Les stratégies d’accès Azure Purview sont actuellement en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

## <a name="overview"></a>Vue d’ensemble

Les stratégies d’accès dans Azure Purview vous permettent de gérer l’accès à différents systèmes de données dans l’ensemble de votre patrimoine de données. Par exemple :

Un utilisateur a besoin d’un accès en lecture à un compte Stockage Azure inscrit dans Azure Purview. Vous pouvez accorder cet accès directement dans Azure Purview en créant une stratégie d’accès aux données via l’application **Gestion de stratégie** dans Purview Studio.

Des stratégies d’accès aux données peuvent être appliquées via Purview sur des systèmes de données inscrits pour la stratégie.

## <a name="azure-purview-policy-concepts"></a>Concepts de stratégie d’Azure Purview

### <a name="azure-purview-policy"></a>Stratégie Azure Purview

Une **stratégie** est une collection nommée d’instructions de stratégie. Quand une stratégie est publiée sur un ou plusieurs systèmes de données en vertu de la gouvernance de Purview, elle est ensuite appliquée par ces derniers. Une définition de stratégie comprend un nom, une description, ainsi qu’une liste d’une ou plusieurs instructions de stratégie.

### <a name="policy-statement"></a>Instruction de la stratégie

Une **instruction de stratégie** est une instruction contrôlable de visu qui détermine la façon dont la source de données doit gérer une opération de données spécifique. L’instruction de stratégie comprend les informations **Effet**, **Action, Ressource de données** et **Sujet**.

#### <a name="action"></a>Action

Une **action** est une opération autorisée ou refusée dans le cadre de cette stratégie. Par exemple, lecture ou écriture. Ces actions logiques de haut niveau sont mappées à une ou plusieurs actions de données dans le système de données où elles sont appliquées.

#### <a name="effect"></a>Résultat

L’**effet** indique quel doit être l’effet de cette stratégie. Actuellement, la seule valeur prise en charge est **Autoriser**.

#### <a name="data-resource"></a>Ressource de données

La **ressource de données** est le chemin d’accès complet aux ressources de données, auquel une instruction de stratégie est applicable. Son format est le suivant :

*/subscription/\<subscription-id>/resourcegroups/\<resource-group-name>/providers/\<provider-name>/\<data-asset-path>*

Format données-ressource-chemin de Stockage Azure :

*Microsoft.Storage/storageAccounts/\<account-name>/blobservice/default/containers/\<container-name>*

Format données-ressource-chemin de base de données SQL :

*Microsoft.Sql/servers/\<server-name>*

#### <a name="subject"></a>Sujet

Identité d’utilisateur final extraite d’Azure Active Directory (AAD) pour la personne à laquelle cette instruction de stratégie s’applique. Cette identité peut être un principal de service, un utilisateur individuel, un groupe ou une identité de service géré (MSI).

### <a name="example"></a>Exemple

Deny Read on Data Asset: */subscription/finance/resourcegroups/prod/providers/Microsoft.Storage/storageAccounts/finDataLake/blobservice/default/containers/FinData to group Finance-analyst*

Dans l’instruction de stratégie ci-dessus, l’effet est *Deny*, l’action est *Read*, la ressource de données est le conteneur Stockage Azure *FinData*, et le sujet est le groupe AAD *Finance-analyst*. Si un utilisateur appartenant à ce groupe tente de lire des données du conteneur de stockage *FinData*, la demande est refusée.

### <a name="hierarchical-enforcement-of-policies"></a>Application hiérarchique des stratégies

La ressource de données spécifiée dans une instruction de stratégie est hiérarchique par défaut. Cela signifie que l’instruction de stratégie s’applique à l’objet de données et à **tous** les objets enfants qu’il contient. Par exemple, une instruction de stratégie sur un conteneur de stockage Azure s’applique à tous blobs qu’il contient.

### <a name="policy-combining-algorithm"></a>Algorithme de combinaison de stratégies 

Azure Purview peut avoir différentes instructions de stratégie qui font référence à la même ressource de données. Lors de l’évaluation d’une décision d’accès à des données, Azure Purview combine toutes les stratégies applicables et fournit une décision consolidée. La stratégie de combinaison consiste à choisir la stratégie la plus restrictive.
Par exemple, supposons deux stratégies différentes sur un conteneur Stockage Azure *FinData* comme suit :

Stratégie 1 : *Allow Read on Data Asset /subscription/…./containers/FinData To group Finance-analyst*

Stratégie 2 : *Deny Read on Data Asset /subscription/…./containers/FinData To group Finance-contractors*

Supposons ensuite que l’utilisateur « user1 », qui fait partie de deux groupes, *Finance-analyst* et *Finance-contractors*, exécute un appel à l’API de lecture de blob. Les deux stratégies étant applicables, Azure Purview choisit la plus restrictive, à savoir *Deny* of *Read*. Par conséquent, la demande d’accès sera refusée.

## <a name="policy-publishing"></a>Publication de stratégie

Une stratégie nouvellement créée existe à l’état de mode brouillon, uniquement visible dans Azure Purview. L’acte de publication lance l’application d’une stratégie dans les systèmes de données spécifiés. Il s’agit d’une action asynchrone qui peut prendre jusqu’à 2 minutes pour être effective sur les sources de données sous-jacentes.

Une stratégie publiée sur une source de données peut contenir des références à une ressource appartenant à une source de données différente. Ces références seront ignorées, car la ressource en question n’existe pas dans la source de données où la stratégie est appliquée.

## <a name="azure-purview-to-data-source-action-mapping"></a>Mappage d’Azure Purview à une action de source de données

Le tableau suivant illustre la façon dont les actions dans des stratégies de données Azure Purview mappent à des actions spécifiques dans des systèmes de données.

| **Action de stratégie Purview** | **Actions spécifiques de la source de données**                                                                |
|---------------------------|-------------------------------------------------------------------------------------------------|
|||
| *Lire*                      |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read                        |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery                      |
|                           |<sub>Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed                    |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|||
| *Modify*                    |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read                            |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write                           |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action                      |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action                     |
|                           |<sub>Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete                          |
|||
| *Analyseur de performances SQL*   |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerMetadata/rows/select                     |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseMetadata/rows/select         |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerState/rows/select                        |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseState/rows/select            |
|                           |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|||
| *Auditeur SQL*               |<sub>Microsoft.Sql/sqlservers/databases/Connect                                                      |
|                           |<sub>Microsoft.Sql/sqlservers/Connect                                                                |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityState/rows/select                |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityState/rows/select    |
|                           |<sub>Microsoft.Sql/sqlservers/SystemViewsAndFunctions/ServerSecurityMetadata/rows/select             |
|                           |<sub>Microsoft.Sql/sqlservers/databases/SystemViewsAndFunctions/DatabaseSecurityMetadata/rows/select |

## <a name="next-steps"></a>Étapes suivantes
Consultez les didacticiels expliquant comment créer dans Azure Purview des stratégies qui fonctionnent sur des systèmes de données spécifiques tels que le service Stockage Azure :

* [Approvisionnement du jeu de données par le propriétaire des données pour le service Stockage Azure](how-to-access-policies-storage.md)

<!--
[Dataset provisioning by data owner for Azure SQL DB](how-to-access-policies-sql.md)
-->
