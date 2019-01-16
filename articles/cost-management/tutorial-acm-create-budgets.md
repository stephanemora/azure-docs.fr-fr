---
title: Tutoriel - Créer et gérer des budgets Azure | Microsoft Docs
description: Ce tutoriel vous aide à planifier et à prendre en compte les coûts de services Azure que vous consommez.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 7ffceaf761d459667e4118e3e10b733898ea2710
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053974"
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

## <a name="prerequisites"></a>Prérequis

Les budgets sont disponibles pour tous les clients Azure EA. Vous devez disposer d’un accès en lecture à un abonnement Azure EA pour visualiser les budgets. Pour créer et gérer des budgets, vous devez disposer d’une autorisation de contributeur. Vous pouvez créer des budgets individuels pour les abonnements EA et les groupes de ressources. En revanche, il n’est pas possible d’en créer pour les comptes de facturation EA.

Les autorisations Azure suivantes sont prises en charge par abonnement aux budgets par utilisateur et par groupe :

- Propriétaire : peut créer, modifier ou supprimer des budgets pour un abonnement.
- Contributeur et Contributeur Cost Management : peut créer, modifier ou supprimer ses propres budgets. Peut modifier le montant des budgets créés par d’autres utilisateurs.
- Lecteur et Lecteur Cost Management : peut afficher les budgets pour lesquels ils disposent des autorisations adéquates.

Pour plus d’informations sur l’affectation d’une autorisation d’accès aux données Cost Management, consultez [Affecter une autorisation d’accès aux données Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Créez un budget dans le portail Azure

Vous pouvez créer un budget d’abonnement Azure pour un mois, un trimestre ou un an. Votre navigation dans le portail Azure détermine si vous créez un budget pour un abonnement ou pour un groupe de ressources. Par exemple, dans le portail Azure, accédez à **Abonnements** &gt; sélectionnez un abonnement &gt; **Budgets**. Dans cet exemple, le budget que vous créez est pour l’abonnement que vous avez sélectionné. Si vous voulez créer un budget pour un groupe de ressources, accédez à **Groupes de ressources** > sélectionnez un groupe de ressources > **Budgets**.

Une fois des budgets créés, ils affichent une vue simple de vos dépenses actuelles par rapport à ces budgets.

Cliquez sur **Add**.

![Budgets Cost Management affichés dans le Portail Azure](./media/tutorial-acm-create-budgets/budgets01.png)

Dans la fenêtre **Créer un budget**, entrez un nom de budget et un montant de budget. Choisissez ensuite une période mensuelle, trimestrielle ou annuelle. Ensuite, sélectionnez une date de fin. Les budgets nécessitent au moins un seuil de coût (% du budget) et une adresse e-mail correspondante. Si vous le souhaitez, vous pouvez inclure jusqu’à cinq seuils et cinq adresses e-mail dans un seul budget. Lorsqu’un seuil de budget est atteint, des notifications par e-mail sont normalement reçues en moins de huit heures.

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


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créez un budget dans le portail Azure
> * Modifier un budget

Passez au tutoriel suivant pour créer une exportation récurrente de vos données de gestion des coûts.

> [!div class="nextstepaction"]
> [Créer et gérer des données exportées](tutorial-export-acm-data.md)
