---
title: Tutoriel - Créer et gérer des budgets Azure | Microsoft Docs
description: Ce tutoriel vous aide à planifier et à prendre en compte les coûts de services Azure que vous consommez.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 704aefd68f35ca20f72a2a0c46bf11912c139e65
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469486"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutoriel : Créer et gérer des budgets Azure

Les budgets dans Cost Management vous aident à planifier et à suivre la comptabilité de l’organisation. Avec les budgets, vous pouvez prendre en compte les services Azure que vous consommez ou auxquels vous vous abonnez pendant une période spécifique. Ils vous permettent d’informer les autres utilisateurs de leurs dépenses pour gérer les coûts de manière proactive, ainsi que pour superviser la progression des dépenses. En cas de dépassement des seuils budgétaires que vous avez créés, seules des notifications sont déclenchées. Aucune de vos ressources n’est affectée et votre consommation n’est pas arrêtée. Vous pouvez utiliser des budgets pour comparer et suivre les dépenses lors de l’analyse des coûts.

Les budgets mensuels sont évalués par rapport aux dépenses effectuées toutes les quatre heures. Toutefois, les données et les notifications associées aux ressources consommées sont mises à disposition dans un délai de huit heures.  

Les budgets sont automatiquement réinitialisés à la fin d’une période (mensuelle, trimestrielle ou annuelle) pour le même montant lorsque vous sélectionnez une date d’expiration ultérieure. Étant donné qu’ils sont réinitialisés avec le même montant de budget, vous devez créer des budgets distincts quand les montants budgétisés diffèrent pour des périodes ultérieures.

Les exemples de ce tutoriel expliquent comment créer et modifier un budget pour un abonnement Azure Contrat Entreprise (EA).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Modifier un budget

## <a name="prerequisites"></a>Conditions préalables

Les budgets sont pris en charge pour divers types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, consultez [Comprendre les données Cost Management](understand-cost-mgt-data.md). Pour afficher les budgets, vous devez au minimum disposer d'un accès en lecture à votre compte Azure.

 Dans le cadre des abonnements Azure EA, vous devez disposer d'un accès en lecture pour afficher les budgets. Pour créer et gérer des budgets, vous devez disposer d’une autorisation de contributeur. Vous pouvez créer des budgets individuels pour les abonnements EA et les groupes de ressources. En revanche, il n’est pas possible d’en créer pour les comptes de facturation EA.

Les autorisations Azure suivantes ou les étendues, sont pris en charge par abonnement pour les budgets par utilisateur et groupe. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

- Propriétaire : peut créer, modifier ou supprimer des budgets pour un abonnement.
- Contributeur et Contributeur Cost Management : peut créer, modifier ou supprimer ses propres budgets. Peut modifier le montant des budgets créés par d’autres utilisateurs.
- Lecteur et Lecteur Cost Management : peut afficher les budgets pour lesquels ils disposent des autorisations adéquates.

Pour plus d’informations sur l’affectation d’une autorisation d’accès aux données Cost Management, consultez [Affecter une autorisation d’accès aux données Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Créez un budget dans le portail Azure

Vous pouvez créer un budget d’abonnement Azure pour un mois, un trimestre ou un an. Votre contenu de navigation dans le portail Azure détermine si vous créez un budget pour un abonnement ou pour un groupe d’administration.

Pour créer ou afficher un budget, ouvrez l’étendue souhaitée dans le portail Azure et sélectionnez **Budgets** dans le menu. Par exemple, accédez à **abonnements**, sélectionnez un abonnement à partir de la liste, puis **Budgets** dans le menu. Utilisez le **étendue** pilule pour basculer vers une portée différente, comme un groupe d’administration, dans les Budgets. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

Une fois des budgets créés, ils affichent une vue simple de vos dépenses actuelles par rapport à ces budgets.

Cliquez sur **Add**.

![Budgets Cost Management affichés dans le Portail Azure](./media/tutorial-acm-create-budgets/budgets01.png)

Dans la fenêtre **Créer un budget**, entrez un nom de budget et un montant de budget. Choisissez ensuite une période mensuelle, trimestrielle ou annuelle. Ensuite, sélectionnez une date de fin. Les budgets nécessitent au moins un seuil de coût (% du budget) et une adresse e-mail correspondante. Si vous le souhaitez, vous pouvez inclure jusqu’à cinq seuils et cinq adresses e-mail dans un seul budget. Lorsqu’un seuil de budget est atteint, des notifications par e-mail sont normalement reçues en moins de huit heures. Pour plus d'informations sur les notifications, consultez [Utiliser les alertes de coût](cost-mgt-alerts-monitor-usage-spending.md).

Si vous avez un abonnement de paiement à l’utilisation, MSDN ou Visual Studio, votre période de facturation de facture ne peut pas s’aligner au mois calendaire. Pour ces types d’abonnements et groupes de ressources, vous pouvez créer un budget qui est aligné pour votre période de facturation ou pour les mois calendaires. Pour créer un budget aligné sur votre période de facturation, sélectionnez une période de réinitialisation du mois de facturation, facturation trimestre ou année de facturation. Pour créer un budget aligné pour le mois calendaire, sélectionnez une période de réinitialisation de mensuelle, trimestrielle ou annuelle.

Voici un exemple de création de budget mensuel de 4 500 $. Une alerte par e-mail est générée quand 90 % du budget est atteint.

![Exemple d’informations affichées dans la zone de création du budget](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quand vous créez un budget trimestriel, il fonctionne de la même façon qu’un budget mensuel. La différence est que le montant du budget pour le trimestre est divisé de manière équitable entre les trois mois du trimestre. Comme vous pouvez l’imaginer, un montant d’un budget annuel est divisé de manière équitable entre les 12 mois de l’année civile.

Les dépenses actuelles par rapport aux budgets sont mises à jour chaque fois que Cost Management reçoit des données de facturation mises à jour. En général, tous les jours.

![Exemple d’informations indiquant la dépense actuelle par rapport aux budgets](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Après avoir créé un budget, il est indiqué dans l’analyse des coûts. L’affichage de votre budget par rapport à la tendance de vos dépenses est l’une des premières étapes quand vous commencez à [analyser vos coûts et vos dépenses](quick-acm-cost-analysis.md).

![Exemple de budget et de dépenses affichés dans l’analyse des coûts](./media/tutorial-acm-create-budgets/cost-analysis.png)

Dans l’exemple précédent, vous avez créé un budget pour un abonnement. Toutefois, vous pouvez également créer un budget pour un groupe de ressources. Si vous voulez créer un budget pour un groupe de ressources, accédez à **Gestion des coûts + facturation** &gt; **Abonnements** &gt; sélectionnez un abonnement > **Groupes de ressource** > sélectionnez un groupe de ressources > **Budgets** > puis **Ajouter** un budget.

## <a name="edit-a-budget"></a>Modifier un budget

Selon le niveau d’accès dont vous disposez, vous pouvez modifier un budget pour changer ses propriétés. Dans l’exemple suivant, certaines des propriétés sont en lecture seule, car l’utilisateur dispose uniquement de l’autorisation Contributeur sur l’abonnement. Actuellement, la **Date d’expiration** est désactivée et ne peut pas être modifiée une fois définie.

![Exemple de modification de plusieurs propriétés du budget](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Un groupe d’actions de déclencheur

Lorsque vous créez ou modifiez un budget pour un abonnement ou un groupe de ressources, vous pouvez le configurer pour appeler un groupe d’actions. Le groupe d’actions peut effectuer diverses actions différentes quand votre budget de seuil est atteint. Pour plus d’informations sur les groupes d’actions, consultez [créer et gérer des groupes d’actions dans le portail Azure](../azure-monitor/platform/action-groups.md). Pour plus d’informations sur l’utilisation d’une automatisation basée sur le budget avec des groupes d’actions, consultez [gérer les coûts des budgets Azure](../billing/billing-cost-management-budget-scenario.md).

Pour créer ou mettre à jour des groupes d’actions, cliquez sur **gérer des groupes d’actions** pendant que vous créez ou modifiez un budget.

![Exemple de création d’un budget pour afficher les groupes d’actions de gestion](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Ensuite, cliquez sur **ajouter un groupe d’action** et créer le groupe d’actions.


![Image de la zone de groupe d’action Ajouter](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Après l’action de groupe est créé, fermez la boîte pour revenir à votre budget.

Configurer votre budget pour utiliser votre groupe d’actions lorsqu’un seuil individuel est remplie. Jusqu'à cinq des seuils différents sont pris en charge.

![Exemple illustrant la sélection de groupe d’action pour une condition d’alerte](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

L’exemple suivant montre les seuils budgétaires définies sur 50 %, 75 % et 100 %. Chacun est configuré pour déclencher les actions spécifiées dans le groupe d’actions désigné.

![Exemple illustrant les conditions d’alerte configurées avec différents groupes d’actions et le type d’actions](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Modifier un budget

Passez au tutoriel suivant pour créer une exportation récurrente de vos données de gestion des coûts.

> [!div class="nextstepaction"]
> [Créer et gérer des données exportées](tutorial-export-acm-data.md)
