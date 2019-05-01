---
title: Obtenir des données, des éléments ou des enregistrements avec une pagination - Azure Logic Apps
description: Configurer la pagination de dépasser la limite de taille de page par défaut pour les actions du connecteur dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476540"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obtenir d’autres données, des articles ou des enregistrements à l’aide de la pagination dans Azure Logic Apps

Lorsque vous récupérez des données, des éléments ou des enregistrements à l’aide d’une action de connecteur dans [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vous pouvez obtenir des jeux de résultats tellement important que l’action ne retourne pas tous les résultats en même temps. Avec certaines actions, le nombre de résultats peut dépasser taille de page par défaut du connecteur. Dans ce cas, l’action retourne uniquement la première page de résultats. Par exemple, la taille de page par défaut pour le connecteur SQL Server **obtenir les lignes** action est 2048, mais peut varier en fonction des autres paramètres.

Certaines actions vous permettent d’activer un *la pagination* paramètre afin que votre application logique puisse obtenir davantage de résultats jusqu'à la limite de pagination, mais retourne les résultats en tant qu’un seul message lorsque l’action est terminée. Lorsque vous utilisez la pagination, vous devez spécifier un *seuil* valeur, qui est le nombre cible de résultats que vous souhaitez que l’action à retourner. L’action récupère les résultats jusqu'à atteindre le seuil spécifié. Lorsque votre nombre total d’éléments est inférieur au seuil spécifié, l’action récupère tous les résultats.

Activez le paramètre de la pagination récupère des pages de résultats selon la taille de la page d’un connecteur. Ce comportement signifie que parfois, vous pouvez obtenir plus de résultats que le seuil spécifié. Par exemple, lors de l’utilisation de SQL Server **obtenir les lignes** action, qui prend en charge la pagination paramètre :

* Taille de la page de l’action par défaut est 2048 des enregistrements par page.
* Supposons que vous avez 10 000 enregistrements et que vous spécifiez 5 000 enregistrements en tant que la valeur minimale.
* La pagination Obtient les pages d’enregistrements, par conséquent, à obtenir au moins la valeur minimale spécifiée, l’action retourne des 6144 enregistrements (3 pages x 2048 enregistrements), pas 5 000 enregistrements.

Voici une liste avec quelques-uns des connecteurs où vous pouvez dépasser la taille de page par défaut pour des actions spécifiques :

* [Stockage Blob Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’application logique et l’action où vous souhaitez activer la pagination. Si vous n’avez pas une application logique, consultez [Guide de démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Activer la pagination

Pour déterminer si une action prend en charge la pagination dans le Concepteur d’application logique, vérifiez les paramètres de l’action pour le **la Pagination** paramètre. Cet exemple montre comment activer la pagination dans le serveur SQL Server **obtenir les lignes** action.

1. Dans angle supérieur droit l’action de le, choisissez le bouton de sélection (**...** ) bouton, puis sélectionnez **paramètres**.

   ![Ouvrez les paramètres de l’action](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Si l’action prend en charge la pagination, l’action indique le **la Pagination** paramètre.

1. Modifier le **la Pagination** définition à partir **hors** à **sur**. Dans le **seuil** propriété, spécifiez une valeur entière pour le nombre cible de résultats que vous souhaitez l’action à retourner.

   ![Spécifiez le nombre minimal de résultats à retourner](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Une fois ces opérations effectuées, sélectionnez **Terminé**.

## <a name="workflow-definition---pagination"></a>Définition de flux de travail - la pagination

Lorsque vous activez la pagination pour une action qui prend en charge cette fonctionnalité, la définition de flux de travail de votre application logique inclut le `"paginationPolicy"` propriété avec le `"minimumItemCount"` propriété dans relatives à cette action `"runtimeConfiguration"` propriété, par exemple :

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
