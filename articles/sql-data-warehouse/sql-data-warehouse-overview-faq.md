---
title: Questions fréquentes (FAQ) sur Azure Synapse Analytics (anciennement SQL DW)
description: Cet article répertorie les questions fréquemment posées sur Azure Synapse Analytics (anciennement SQL Data Warehouse) par les clients et les développeurs
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9e64d184ef0fd120d1bc64ce274ee882b7938df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708645"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Forum aux questions sur Azure Synapse Analytics (anciennement SQL DW)

## <a name="general"></a>Généralités

Q. Qu’est-ce qu’Azure Synapse ?

R. Azure Synapse est un service d’analytique illimité qui regroupe l’entreposage des données et l’analytique de Big Data. Il vous donne la possibilité d’interroger les données avec votre propre vocabulaire, en utilisant des ressources serverless à la demande ou des ressources provisionnées, le tout à grande échelle. Azure Synapse rassemble ces deux mondes avec une expérience unifiée pour la réception, la préparation, la gestion et la remise de données pour les besoins immédiats d’apprentissage automatique et décisionnels. Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Qu’est-il arrivé à Azure SQL Data Warehouse ?

R. Azure Synapse est une évolution d’Azure SQL Data Warehouse (SQL DW). Nous avons utilisé le même entrepôt de données leader du secteur pour atteindre un tout nouveau niveau de performances et de capacités. Vous pouvez continuer à exécuter vos charges de travail Data Warehouse existantes en production aujourd’hui avec Azure Synapse et tirer automatiquement parti des nouvelles fonctionnalités de la préversion. Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Qu’est-ce que SQL Analytics ?

R. SQL Analytics fait référence aux fonctionnalités d’entreposage de données d’entreprise qui sont généralement disponibles avec Azure Synapse. Pour plus d’informations, consultez l’article [Qu’est-ce qu’Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Comment prendre en main Azure Synapse ?

R. Pour plus d’informations, vous pouvez commencer à utiliser un [compte Azure gratuit](https://azure.microsoft.com/free/sql-data-warehouse/) ou [contacter le service commercial](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

Q. Quelles sont les solutions offertes par Azure Synapse en matière de sécurité des données ?

R. Azure Synapse propose plusieurs solutions de protection des données, comme le chiffrement TDE et les audits. Pour plus d’informations, consultez [Sécurité].

Q. Où puis-je trouver les normes juridiques et commerciales auxquelles Azure Synapse est conforme ?

R. Consultez la page [Conformité Microsoft] pour connaître les différentes offres de conformité par produit, notamment SOC et ISO. Tout d’abord, sélectionnez le titre de conformité de votre choix, puis développez Azure dans la section des services cloud qui figurent dans le champ d’application de Microsoft, sur le côté droit de la page, pour voir quels sont les services conformes Azure Synapse.

Q. Puis-je me connecter à Power BI ?

R. Oui. Bien que Power BI prenne en charge les requêtes directes avec Azure Synapse, il n’est pas destiné à traiter un grand nombre d’utilisateurs ou de données en temps réel. Pour optimiser davantage les performances de Power BI, pensez à utiliser Power BI en complément d’Azure Analysis Services ou d’Analysis Service IaaS.

Q. Quelles sont les limites de capacité de SQL Analytics ?

R. Consultez notre page [Limites de capacité] actuelle. 

Q. Pourquoi ma Mise à l’échelle/Pause/Reprise est-elle si longue ?

R. Un certain nombre de facteurs peuvent influencer la durée des opérations de gestion du calcul. Une cause fréquente des opérations avec une durée d’exécution longue est l’annulation de transactions. Quand une opération de mise à l’échelle ou de pause est lancée, toutes les sessions entrantes sont bloquées et les requêtes sont vidées. Afin de laisser le système dans un état stable, les transactions doivent être annulées avant qu’une opération puisse commencer. Plus le nombre et la taille du journal des transactions sont importants, plus longtemps l’opération sera bloquée en attente de la restauration du système à un état stable.

## <a name="user-support"></a>Service client

Q. J’ai une suggestion de fonctionnalité, où dois-je l’envoyer ?

R. Si vous souhaitez faire une demande de fonctionnalité, envoyez-la sur notre page [UserVoice].

Q. Comment faire x ?

R. Pour obtenir de l’aide concernant le développement avec Azure Synapse, vous pouvez poser des questions sur notre page [Stack Overflow]. 

Q. Comment envoyer un ticket de support ?

R. Les [tickets de support] peuvent être déposés sur le Portail Azure.

## <a name="sql-languagefeature-support"></a>Prise en charge de fonctionnalités / langages SQL 

Q. Quels types de données sont pris en charge ?

R. Consultez [Types de données].

Q. Quelles fonctionnalités de table prenez-vous en charge ?

R. De nombreuses fonctionnalités sont prises en charge ; celles qui ne le sont pas sont documentées dans [Fonctionnalités de table non prises en charge].

## <a name="tooling-and-administration"></a>Outils et administration

Q. SQL Analytics prend-il en charge les API REST ?

R. Oui. La plupart des fonctionnalités REST utilisables avec SQL Database sont également disponibles avec SQL Analytics. Vous trouverez des informations sur les API sur les pages de documentation REST ou sur [MSDN].


## <a name="loading"></a>Chargement

Q : Quels pilotes clients prenez-vous en charge ?

R. La prise en charge des pilotes pour DW est détaillée sur la page [Chaînes de connexion].

Q : Quels sont les formats de fichiers pris en charge par PolyBase ?

R : Orc, RC, Parquet et le texte plat délimité

Q : À quelles sources de données puis-je me connecter à l’aide de Polybase ? 

R : [Azure Data Lake Store] et [Azure Storage Blobs]

Q : Les automates à pile sont-ils possibles lors de la connexion à Azure Storage Blobs ou à ADLS ? 

R : Non, PolyBase interagit uniquement avec les composants de stockage. 

Q : Puis-je me connecter à HDI ?

R : HDI peut utiliser ADLS ou WASB comme couche HDFS. Si vous avez l’un des deux comme couche HDFS, vous pouvez charger ces données dans SQL DW. Toutefois, vous ne peut pas générer d’automate à pile vers l’instance HDI. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur Azure Synapse dans son ensemble, consultez notre page [Vue d'ensemble].


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Chaînes de connexion]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Tickets de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Sécurité]: ./sql-data-warehouse-overview-manage-security.md
[Conformité Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[Limites de capacité]: ./sql-data-warehouse-service-capacity-limits.md
[types de données]: ./sql-data-warehouse-tables-data-types.md
[Fonctionnalités de table non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Database projects feature request]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Vue d'ensemble]: ./sql-data-warehouse-overview-faq.md
