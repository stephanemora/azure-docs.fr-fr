---
title: Étiquetage dans Azure Purview
description: Commencez à utiliser des étiquettes de confidentialité et des classifications pour améliorer vos ressources Purview
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0cc474ba9566737cb0117cc3a0f2bfb079cdf3d9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616509"
---
# <a name="labeling-in-azure-purview"></a>Étiquetage dans Azure Purview

> [!IMPORTANT]
> Les étiquettes de confidentialité Azure Purview sont actuellement disponibles en PRÉVERSION. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.
>

Dans le cadre de leur travail, les membres de votre organisation collaborent avec d’autres personnes en interne ou non. Les données ne restent pas toujours dans votre cloud : elles sont souvent en itinérance sur les différents appareils, applications et services. L’itinérance des données doit être sécurisée et conforme aux stratégies de votre organisation.</br>

L’application d’étiquettes de confidentialité à votre contenu vous permet de protéger vos données en indiquant le niveau de confidentialité de certaines données dans votre organisation. Ce procédé extrait par ailleurs les données elles-mêmes. Vous utilisez ainsi des étiquettes pour suivre le type des données, sans exposer les données sensibles sur une autre plateforme.</br>

Par exemple, l’application d’une étiquette de confidentialité « Hautement confidentiel » à un document qui inclut des numéros de sécurité sociale et de carte bancaire vous permet d’identifier le niveau de confidentialité du document sans connaître les données réelles dans le document.

## <a name="benefits-of-labeling-in-azure-purview"></a>Avantages de l’étiquetage dans Azure Purview

Azure Purview vous permet d’appliquer des étiquettes de confidentialité à des ressources et, ce faisant, classer et protéger vos données.

* **L’étiquette reste associée aux données :** les étiquettes de confidentialité utilisées dans Purview sont actuellement reconnues dans Microsoft 365, SharePoint, Teams, Power BI et SQL. Si une étiquette est appliquée à une ressource dans Purview et que vos données transitent vers une autre plateforme telle que Power BI ou Office, l’étiquette suit également les données. De même, si une étiquette est appliquée à un document Word, puis analysée par Purview, elle sera transmise à Purview.
* **Vue d’ensemble de votre patrimoine de données :** Purview fournit des informations sur vos données via des rapports prédéfinis. Lorsque vous analysez des données dans Purview, nous alimentons les rapports avec des informations sur les ressources que vous avez, l’historique des analyses, les classifications figurant dans vos données, les étiquettes appliquées, les termes de glossaire, etc.
* **Étiquetage automatique :** les étiquettes peuvent être appliquées automatiquement en fonction du niveau de confidentialité des données. Lorsqu’une ressource est analysée afin d’identifier la présence de données confidentielles, les règles d’étiquetage automatique sont utilisées pour déterminer l’étiquette de confidentialité à appliquer. Vous pouvez créer des règles d’étiquetage automatique pour chaque étiquette de confidentialité, en définissant le type de classification ou d’informations sensibles constituant une étiquette.
* **Appliquez des étiquettes aux fichiers et aux colonnes de base de données :** les étiquettes peuvent être appliquées aux fichiers inclus dans un stockage tel qu’Azure Data Lake, Azure Files, etc., ainsi qu’aux données schématisées telles que les colonnes dans Azure SQL DB, Cosmos DB, etc.

Les étiquettes de confidentialité sont des balises que vous pouvez appliquer aux ressources pour classer et protéger vos données. En savoir plus sur les [étiquettes de confidentialité ici](/microsoft-365/compliance/create-sensitivity-labels.md).

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>Appliquer des étiquettes aux ressources dans Azure Purview

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="Flux d’application d’étiquettes aux ressources dans Purview : création d’étiquettes, inscription d’une ressource, analyse d’une ressource, détection des classifications et application des étiquettes.":::

Pour pouvoir appliquer des étiquettes à votre ressource dans Azure Purview, vous devez procéder comme suit :

1. [Créez ou étendez des étiquettes de confidentialité existantes à Azure Purview](how-to-automatically-label-your-content.md) dans le Centre de conformité Microsoft 365. La création d’étiquettes de confidentialité inclut des règles d’étiquetage automatique qui indiquent les étiquettes à appliquer en fonction des classifications détectées dans vos données.
1. [Inscrivez et analysez votre ressource](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically) dans Azure Purview.
1. Azure Purview applique des classifications : lorsque vous planifiez une analyse sur une ressource, Azure Purview analyse le type de données dans votre ressource et y applique des classifications dans le catalogue de données. L’application de classifications est effectuée automatiquement par Azure Purview. Aucune action n’est requise de votre part.
1. Azure Purview applique des étiquettes : une fois les classifications détectées sur une ressource, Azure Purview applique des étiquettes aux ressources en fonction des règles d’étiquetage automatique. L’application des étiquettes est effectuée automatiquement par Azure Purview. Aucune action n’est requise de votre part dès lors que vous avez créé des étiquettes avec des règles d’étiquetage automatique lors de la première étape.

> [!NOTE]
> Les règles d’étiquetage automatique sont des conditions indiquant quand une étiquette particulière doit être appliquée. Lorsque ces conditions sont réunies, l’étiquette est attribuée automatiquement aux données. Lorsque vous créez vos étiquettes, veillez à définir des règles d’étiquetage automatique pour les fichiers et pour les colonnes de base de données pour appliquer automatiquement les étiquettes à chaque analyse.
>

## <a name="supported-data-sources"></a>Sources de données prises en charge

Les étiquettes de confidentialité sont prises en charge dans Azure Purview pour les sources de données suivantes :

|Type de données  |Sources  |
|---------|---------|
|Étiquetage automatique des fichiers     |    - Stockage Blob Azure</br>- Azure Files</br>- Azure Data Lake Storage Gen1 et Gen2</br>- Amazon S3|
|Étiquetage automatique des colonnes de base de données     |  - SQL Server</br>- Azure SQL Database</br>- Azure SQL Database Managed Instance</br>- Espaces de travail Azure Synapse Analytics</br>- Azure Cosmos Database (API SQL)</br> - Azure Database pour MySQL</br> - Azure Database pour PostgreSQL</br> - Azure Data Explorer</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>Étiquetage des bases de données SQL

Outre l’étiquetage Purview pour les colonnes de base de données, Microsoft prend également en charge l’étiquetage pour les colonnes de base de données SQL utilisant la classification des données SQL dans [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Alors que Purview utilise les [étiquettes de confidentialité](/microsoft-365/compliance/sensitivity-labels) globales, SSMS utilise uniquement les étiquettes définies localement.

L’étiquetage dans Purview et l’étiquetage dans SSMS sont des processus distincts qui n’interagissent pas l’un avec l’autre actuellement. Par conséquent, **les étiquettes appliquées dans SSMS ne sont pas affichées dans Purview et inversement**. Nous recommandons Azure Purview pour l’étiquetage des bases de données SQL, car Purview utilise des étiquettes MIP globales qui peuvent être appliquées sur de nombreuses plateformes.

Pour plus d’informations, consultez la [documentation Découverte et classification des données SQL](/sql/relational-databases/security/sql-data-discovery-and-classification). </br></br>

> [!div class="nextstepaction"]
> [Étiqueter automatiquement votre contenu](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [Insights relatifs aux étiquettes de confidentialité](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Forum aux questions sur l’étiquetage](sensitivity-labels-frequently-asked-questions.yml)
