---
title: Aide autonome relative à la préversion de SQL à la demande
description: Cette section contient des informations qui peuvent vous aider à résoudre les problèmes liés à SQL à la demande (préversion).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421193"
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

Si votre requête échoue avec le message d’erreur « This query cannot be executed due to current resource constraints » (Cette requête ne peut pas être exécutée en raison de contraintes de ressources), cela signifie que SQL OD ne peut pas l’exécuter pour le moment en raison de contraintes de ressources : 

- Veillez à utiliser des types de données de taille raisonnable. Spécifiez également un schéma pour les colonnes de type chaîne des fichiers Parquet, car elles auront la valeur VARCHAR(8000) par défaut. 

- Si votre requête cible des fichiers CSV, envisagez de [créer des statistiques](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Pour optimiser les requêtes, consultez les [bonnes pratiques concernant les performances de SQL à la demande](best-practices-sql-on-demand.md).  

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
