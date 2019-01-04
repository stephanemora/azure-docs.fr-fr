---
title: Configurer des alertes sur les métriques pour Azure Database pour PostgreSQL dans le portail Azure
description: Cet article décrit comment configurer des alertes sur les métriques, et y accéder, dans Azure Database pour PostgreSQL à partir du portail Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 26b7e92bf8fa6c42320f604643bc996794ed52ca
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540722"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Utiliser le portail Azure pour configurer des alertes sur les métriques pour Azure Database pour PostgreSQL 

Cet article vous explique comment définir des alertes Azure Database pour PostgreSQL à l’aide du portail Azure. Vous pouvez recevoir une alerte en fonction de métriques de monitoring pour vos services Azure.

L’alerte se déclenche quand la valeur d’une métrique spécifiée dépasse le seuil que vous avez défini. L’alerte se déclenche une première fois lorsque la condition est vérifiée, puis une deuxième fois quand elle cesse de l’être. 

Vous pouvez configurer une alerte pour effectuer les actions suivantes lors de son déclenchement :
* Envoyer des notifications par e-mail à l’administrateur du service et aux coadministrateurs.
* Envoyer un e-mail à d’autres adresses que vous spécifiez.
* Appeler un webhook.

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide des ressources suivantes :
* [Portail Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interface de ligne de commande (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Créer une règle d’alerte sur une métrique à partir du portail Azure
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez le serveur Azure Database pour PostgreSQL à surveiller.

2. Sous la section **Surveillance** de la barre latérale, sélectionnez **Règles d’alerte**, comme indiqué :

   ![Sélectionner Règles d’alerte](./media/howto-alert-on-metric/1-alert-rules.png)

3. Sélectionnez **Ajouter une alerte de mesure** (icône +). 

4. La page **Ajouter une règle** s’ouvre, comme illustré ci-dessous.  Entrez les informations obligatoires :

   ![Formulaire Ajouter une alerte Métrique](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Paramètre | Description  |
   |---------|---------|
   | NOM | Entrez un nom pour la règle d’alerte. Cette valeur est envoyée dans l’e-mail de notification d’alerte. |
   | Description | Entrez une brève description de la règle d’alerte. Cette valeur est envoyée dans l’e-mail de notification d’alerte. |
   | Alerte sur | Choisissez **Métriques** pour ce type d’alerte. |
   | Abonnement | Ce champ est prérempli avec l’abonnement qui héberge votre Azure Database pour PostgreSQL. |
   | Groupe de ressources | Ce champ est prérempli avec le groupe de ressources de votre Azure Database pour PostgreSQL. |
   | Ressource | Ce champ est prérempli avec le nom de votre Azure Database pour PostgreSQL. |
   | Métrique | Sélectionnez la métrique pour laquelle vous souhaitez émettre une alerte. Par exemple, **Pourcentage de stockage**. |
   | Condition | Choisissez la condition avec laquelle comparer la métrique. Par exemple, **Supérieur à**. |
   | Seuil | Valeur de seuil pour la métrique, par exemple 85 (en pourcentage). |
   | Période | Durée pendant laquelle la règle de métrique doit être vérifiée avant que l’alerte se déclenche. Par exemple, **Au cours des 30 dernières minutes**. |

   Dans notre exemple, l’alerte recherche un Pourcentage de stockage supérieur à 85 % pendant une période de 30 minutes. Cette alerte se déclenche lorsque le Pourcentage de stockage moyen a été supérieur à 85 % pendant 30 minutes. Après le premier déclenchement, l’alerte se déclenche à nouveau si le Pourcentage de stockage moyen est inférieur à 85 % pendant 30 minutes.

5. Choisissez la méthode de notification pour la règle d’alerte. 

   Activez l’option **Envoyer des e-mails aux propriétaires, contributeurs et lecteurs** si vous souhaitez que les administrateurs et les coadministrateurs de l’abonnement reçoivent un e-mail lorsque l’alerte se déclenche.

   Si vous souhaitez que d’autres adresses e-mail reçoivent une notification lorsque l’alerte se déclenche, ajoutez-les dans le champ **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)**. Séparez les adresses e-mails par des points-virgules : *email@contoso.com;email2@contoso.com*

   Vous pouvez aussi fournir un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.

6. Sélectionnez **OK** pour créer l’alerte.

   Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="manage-your-alerts"></a>Gérez vos alertes
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et exécuter les actions suivantes :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent concernant cette alerte.
* **Modifiez** ou **Supprimez** la règle d’alerte.
* **Désactivez** ou **Activez** l’alerte, selon que vous voulez arrêter temporairement ou reprendre la réception de notifications.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus en détail la [configuration des webhooks dans les alertes](../azure-monitor/platform/alerts-webhooks.md).
* Consultez une [vue d’ensemble de la collecte des métriques](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
