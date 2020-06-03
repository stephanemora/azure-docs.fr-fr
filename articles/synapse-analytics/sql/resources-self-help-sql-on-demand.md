---
title: Aide autonome relative à la préversion de SQL à la demande
description: Cette section contient des informations qui peuvent vous aider à résoudre les problèmes liés à SQL à la demande (préversion).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8b2a9b6c5324240d71a80cde904057757d6ef421
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658879"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Aide autonome relative à SQL à la demande (préversion)

Cet article contient des informations sur la façon de résoudre les problèmes les plus fréquents concernant SQL à la demande (préversion) dans Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL à la demande est grisé dans Synapse Studio

Si Synapse Studio ne peut pas établir de connexion à SQL à la demande, vous verrez que SQL à la demande est grisé ou que son état est « hors connexion ». En règle générale, ce problème se produit quand l’un des cas suivants se produit :

1) Votre réseau empêche la communication avec le back-end Azure Synapse. Le cas le plus fréquent est que le port 1443 est bloqué. Pour que SQL à la demande fonctionne, débloquez ce port. D’autres problèmes peuvent également empêcher le fonctionnement de SQL à la demande. Pour plus d’informations, consultez le [guide de dépannage complet](../troubleshoot/troubleshoot-synapse-studio.md).
2) Vous n’avez pas les autorisations nécessaires pour vous connecter à SQL à la demande. Pour obtenir l’accès, l’un des administrateurs de l’espace de travail Azure Synapse doit vous ajouter comme administrateur de l’espace de travail ou vous attribuer le rôle Administrateur SQL. [Pour plus d’informations, consultez le guide complet sur le contrôle d’accès](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>La requête échoue car le fichier ne peut pas être ouvert

Si votre requête échoue avec une erreur indiquant que le fichier ne peut pas être ouvert parce qu’il n’existe pas ou qu’il est utilisé par un autre processus, et que vous êtes sûr que le fichier existe et qu’il n’est pas utilisé par un autre processus, cela signifie que SQL à la demande ne peut pas accéder au fichier. Ce problème se produit généralement parce que votre identité Azure Active Directory ne dispose pas des droits d’accès au fichier. Par défaut, SQL à la demande tente d’accéder au fichier à l’aide de votre identité Azure Active Directory. Pour résoudre ce problème, vous devez disposer des droits nécessaires pour accéder au fichier. Le moyen le plus simple consiste à vous accorder le rôle « Contributeur aux données Blob du stockage » pour le compte de stockage que vous tentez d’interroger. [Pour plus d’informations, consultez le guide complet sur le contrôle d’accès Azure Active Directory pour le stockage](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>La requête échoue, car elle ne peut pas être exécutée en raison de contraintes de ressources 

Si votre requête échoue avec le message d’erreur « Cette requête ne peut pas être exécutée en raison de contraintes de ressources », cela signifie que SQL à la demande ne peut pas l’exécuter pour le moment en raison de contraintes de ressources : 

- Veillez à utiliser des types de données de taille raisonnable. Spécifiez également un schéma pour les colonnes de type chaîne des fichiers Parquet, car elles auront la valeur VARCHAR(8000) par défaut. 

- Si votre requête cible des fichiers CSV, envisagez de [créer des statistiques](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Pour optimiser les requêtes, consultez les [bonnes pratiques concernant les performances de SQL à la demande](best-practices-sql-on-demand.md).  

## <a name="create-statement-is-not-supported-in-master-database"></a>L’instruction CREATE 'STATEMENT' n'est pas prise en charge dans la base de données master

Si votre requête échoue avec le message d’erreur :

> L'exécution de la requête a échoué. Erreur : CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT is not supported in master database.' (Instruction CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT non prise en charge dans la base de données master). 

Cela signifie que la base de données master dans SQL à la demande ne prend pas en charge la création des éléments suivants :
  - Tables externes
  - Sources de données externes
  - Informations d'identification limitées à la base de données
  - Formats de fichier externe

Solution :

  1. Créer une base de données utilisateur :

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Exécutez l’instruction CREATE dans le contexte de <DATABASE_NAME> qui a échoué précédemment pour la base de données master. 
  
  Exemple pour la création d’un format de fichier externe :
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de SQL à la demande, consultez les articles suivants :

- [Interroger un fichier CSV](query-single-csv-file.md)

- [Interroger des dossiers et plusieurs fichiers CSV](query-folders-multiple-csv-files.md)

- [Interroger des fichiers spécifiques](query-specific-files.md)

- [Interroger des fichiers Parquet](query-parquet-files.md)

- [Interroger des types imbriqués Parquet](query-parquet-nested-types.md)

- [Interroger des fichiers JSON](query-json-files.md)

- [Créer et utiliser des vues](create-use-views.md)

- [Créer et utiliser des tables externes](create-use-external-tables.md)

- [Stocker les résultats de la requête dans le stockage](create-external-table-as-select.md)
