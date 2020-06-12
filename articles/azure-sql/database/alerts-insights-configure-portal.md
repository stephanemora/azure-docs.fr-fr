---
title: Configurer des alertes et des notifications (Portail Azure)
description: Utilisez le Portail Azure pour créer des alertes qui peuvent déclencher des notifications ou l’automation lorsque les conditions spécifiées sont remplies.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 0e9b8f7bc4129c07145130f198b738839b0c7b55
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84035130"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-using-azure-portal"></a>Créer des alertes pour Azure SQL Database et Azure Synapse à l'aide du Portail Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Vue d’ensemble

Cet article explique comment configurer des alertes pour des bases de données dans Azure SQL Database et Azure Synapse (anciennement Azure SQL Data Warehouse) à l’aide du Portail Azure. Les alertes peuvent vous envoyer un e-mail ou appeler un webhook lorsqu'une métrique (taille de la base de données ou utilisation du processeur, par exemple) atteint le seuil. Il présente également les meilleures pratiques à adopter pour définir les périodes d’alerte.

> [!NOTE]
> Pour obtenir des instructions spécifiques sur Azure SQL Managed Instance, consultez [Créer des alertes pour Azure SQL Managed Instance](../managed-instance/alerts-create.md).

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou uniquement lorsqu’un certain nombre d’événements se produisent.

Vous pouvez configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer des notifications par e-mail à l’administrateur du service et aux coadministrateurs
* Envoyer un e-mail à d’autres adresses que vous spécifiez
* Appeler un webhook

Vous pouvez configurer et obtenir des informations sur les règles d’alerte avec

* [Azure portal](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [interface de ligne de commande (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une métrique avec le Portail Azure

1. Sur le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller et sélectionnez-la.
2. Sélectionnez **Alertes** dans la section Surveillance. Le texte et l’icône peuvent varier légèrement pour les différentes ressources.  

   ![Surveillance](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Sélectionnez le bouton **Nouvelle règle d’alerte** pour ouvrir la page **Créer une règle**.
  ![Créer une règle](./media/alerts-insights-configure-portal/create-rule.png)

4. Dans la section **Condition**, cliquez sur **Ajouter**.
  ![Définir la condition](./media/alerts-insights-configure-portal/create-rule.png)
5. Sur la page **Configurer la logique du signal**, sélectionnez un signal.
  ![Sélectionnez un signal](./media/alerts-insights-configure-portal/select-signal.png).
6. Une fois que vous avez sélectionné un signal, par exemple **Pourcentage UC**, la page **Configurer la logique du signal** s’affiche.
  ![Configurer la logique du signal](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Sur cette page, configurez ce type de seuil, l’opérateur, le type d’agrégation, la valeur de seuil, la granularité d’agrégation et la fréquence d’évaluation. Cliquez ensuite sur **Terminé**.
8. Sur la page **Créer une règle**, sélectionnez un **groupe d'actions** existant, ou créez un nouveau groupe. Un groupe d’actions vous permet de définir l’action à exécuter lorsqu’une condition d’alerte se produit.
  ![Définir un groupe d’actions](./media/alerts-insights-configure-portal/action-group.png)

9. Définissez le nom de la règle, fournissez une description facultative, choisissez un niveau de gravité pour la règle, indiquez si vous souhaitez activer la règle lors de la création de la règle, puis cliquez sur **Créer une alerte de règle** pour créer l’alerte de règle de la métrique.

Dans un délai de 10 minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus en détail la [configuration des webhooks dans les alertes](../../azure-monitor/platform/alerts-webhooks.md).
