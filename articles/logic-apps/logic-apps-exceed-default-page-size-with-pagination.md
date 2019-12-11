---
title: Obtenir plus d’éléments ou d’enregistrements à l’aide de la pagination
description: Configurer la pagination de manière à dépasser la limite de taille de page par défaut pour les actions de connecteur dans Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792106"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Obtenir plus de données, d'éléments ou d'enregistrements à l'aide de la pagination dans Azure Logic Apps

Lorsque vous récupérez des données, des éléments ou des enregistrements à l'aide d'une action de connecteur dans [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vous pouvez obtenir des jeux de résultats si volumineux que l'action ne renvoie pas tous les résultats en même temps. Avec certaines actions, le nombre de résultats peut dépasser la taille de page par défaut du connecteur. Dans ce cas, l’action retourne uniquement la première page de résultats. Par exemple, la taille de page par défaut de l'action **Obtenir les lignes** du connecteur SQL Server est de 2 048, mais elle peut varier en fonction d'autres paramètres.

Certaines actions vous permettent d'activer un paramètre de *pagination* afin que votre application logique puisse récupérer davantage de résultats jusqu'à la limite de pagination, mais en les renvoyant sous la forme d'un message unique lorsque l'action se termine. Lorsque vous utilisez la pagination, vous devez spécifier une valeur de *seuil* correspondant au nombre cible de résultats que vous souhaitez que l'action renvoie. L'action récupère les résultats jusqu'à ce que le seuil spécifié soit atteint. Lorsque le nombre total d'éléments est inférieur au seuil spécifié, l'action récupère tous les résultats.

L'activation du paramètre de pagination permet de récupérer les pages de résultats en fonction de la taille de page d'un connecteur. Ce comportement signifie que vous pouvez parfois obtenir plus de résultats que le seuil spécifié. Par exemple, lorsque vous utilisez l'action SQL Server **Obtenir les lignes**, qui prend en charge le paramètre de pagination :

* La taille de page par défaut de l'action est de 2 048 enregistrements par page.
* Supposons que vous ayez 10 000 enregistrements et que vous définissiez 5 000 enregistrements comme minimum.
* La pagination obtient des pages d'enregistrements. Par conséquent, pour obtenir au moins le minimum spécifié, l'action renvoie 6 144 enregistrements (3 pages x 2048 enregistrements), et non 5 000 enregistrements.

La liste suivante répertorie quelques-uns des connecteurs avec lesquels vous pouvez dépasser la taille de page par défaut pour des actions spécifiques :

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

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L'application logique et l'action pour laquelle vous souhaitez activer la pagination. Si vous n'avez pas d'application logique, consultez [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Activer la pagination

Pour déterminer si une action prend en charge la pagination dans le Concepteur d'application logique, vérifiez les paramètres de l'action pour le paramètre **Pagination**. Cet exemple montre comment activer la pagination dans l'action SQL Server **Obtenir les lignes**.

1. En haut à droite de l'action, choisissez le bouton représentant des points de suspension ( **...** ), puis sélectionnez **Paramètres**.

   ![Ouvrir les paramètres de l'action](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Si l'action prend en charge la pagination, le paramètre **Pagination** apparaît.

1. Modifiez le paramètre **Pagination** en remplaçant **Désactivé** par **Activé**. Dans la propriété **Seuil**, spécifiez une valeur entière pour le nombre cible de résultats que vous souhaitez que l'action renvoie.

   ![Spécifiez le nombre minimum de résultats à renvoyer.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Une fois ces opérations effectuées, sélectionnez **Terminé**.

## <a name="workflow-definition---pagination"></a>Définition du flux de travail - pagination

Lorsque vous activez la pagination pour une action qui prend en charge cette fonctionnalité, la définition du flux de travail de votre application logique inclut la propriété `"paginationPolicy"` ainsi que la propriété `"minimumItemCount"` dans la propriété `"runtimeConfiguration"` de cette action ; par exemple :

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
