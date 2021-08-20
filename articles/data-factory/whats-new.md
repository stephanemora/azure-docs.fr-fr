---
title: Nouveautés dans Azure Data Factory
description: Cette page sur les nouveautés présente les nouvelles fonctionnalités et améliorations dans Azure Data Factory.
author: pennyzhou-msft
ms.author: xupzhou
ms.reviewer: xupzhou
ms.service: data-factory
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: fe4c5fb72ce3cd32e14bad211683e01ac6edf3ab
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341934"
---
# <a name="whats-new-in-azure-data-factory"></a>Nouveautés dans Azure Data Factory

Azure Data Factory bénéficie d’améliorations en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées
- Modifications planifiées

Cette page est mise à jour tous les mois. Consultez-la régulièrement. 

## <a name="june-2021"></a>Juin 2021
<br>
<table>
<tr><td><b>Catégorie de service</b></td><td><b>Améliorations du service</b></td><td><b>Détails</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>Déplacement des données</b></td><td>Nouvelle expérience utilisateur avec l’outil Copier des données d’Azure Data Factory</td><td>L’outil remanié Copier des données est désormais disponible avec une expérience d’ingestion des données améliorée.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">En savoir plus</a></td></tr>
<tr><td>MongoDB et MongoDB Atlas sont pris en charge à la fois comme source et récepteur</td><td>Cette amélioration prend en charge la copie de données entre tous les magasins de données pris en charge et les bases de données MongoDB ou MongoDB Atlas.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">En savoir plus</a></td></tr>
<tr><td>Always Encrypted est pris en charge pour les connecteurs Azure SQL Database, Azure SQL Managed Instance et SQL Server en tant que source et récepteur</td><td>Always Encrypted est disponible dans Azure Data Factory pour les connecteurs Azure SQL Database, Azure SQL Managed Instance et SQL Server pour l’activité de copie.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">En savoir plus</a></td></tr>
<tr><td>La définition des métadonnées personnalisées est prise en charge dans l’activité de copie lors de la réception sur ADLS Gen2 ou un objet blob Azure</td><td>Lors de l’écriture dans ADLS Gen2 ou un objet blob Azure, l’activité de copie prend en charge la définition de métadonnées personnalisées ou le stockage des informations sur les dernières modifications du fichier source en tant que métadonnées.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">En savoir plus</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>Flux de données</b></td><td>SQL Server est désormais pris en charge en tant que source et récepteur dans les flux de données</td><td>SQL Server est désormais pris en charge en tant que source et récepteur dans les flux de données. Suivez le lien pour obtenir des instructions sur la configuration de votre mise en réseau à l’aide de la fonctionnalité de VNET managé Azure Integration Runtime en vue de communiquer avec vos instances SQL Server locales et cloud basées sur une machine virtuelle.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">En savoir plus</a></td></tr>
<tr><td>La réutilisation rapide du cluster de flux de données est désormais activée par défaut pour tous les nouveaux Azure Integration Runtime</td><td>ADF est heureux d’annoncer la disponibilité générale de la fonctionnalité populaire de réutilisation rapide du flux de données. La réutilisation rapide est désormais activée par défaut pour tous les nouveaux Azure Integration Runtime.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">En savoir plus</a></td></tr>
<tr><td>Activité Power Query dans ADF en préversion publique</td><td>Vous pouvez désormais générer des mappages de champs complexes sur votre récepteur Power Query à l’aide du data wrangling dans Azure Data Factory. Le récepteur est maintenant configuré dans le pipeline dans l’activité Power Query (préversion) pour s’adapter à cette mise à jour.<br><a href="wrangling-tutorial.md">En savoir plus</a></td></tr>
<tr><td>Interface de supervision des flux de données mise à jour dans Azure Data Factory</td><td>Azure Data Factory dispose d’une nouvelle mise à jour de l’interface utilisateur de supervision pour faciliter l’affichage de vos exécutions de travaux ETL de flux de données et identifier rapidement les zones pour le réglage des performances.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">En savoir plus</a></td></tr>
<tr><td><b>SQL Server Integration Services (SSIS)</b></td><td>Exécutez n’importe quelle requête SQL en 3 étapes simples à l’aide du service SSIS dans Azure Data Factory</td><td>Ce billet indique 3 étapes simples pour exécuter des instructions/scripts SQL n’importe où avec le service SSIS dans Azure Data Factory.<ol><li>Préparez votre runtime d’intégration auto-hébergé/SSIS Integration Runtime.</li><li>Préparez une activité Exécuter le package SSIS dans le pipeline Azure Data Factory.</li><li>Exécutez l’activité Exécuter le package SSIS sur runtime d’intégration auto-hébergé/SSIS Integration Runtime.</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">En savoir plus</a></td></tr>
</table>

## <a name="more-information"></a>Autres informations

- [Blog – Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [Vidéos](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)





