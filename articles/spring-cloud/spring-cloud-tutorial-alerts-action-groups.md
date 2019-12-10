---
title: 'Didacticiel : Superviser les ressources Azure Spring Cloud avec des alertes et des groupes d’actions | Microsoft Docs'
description: Découvrez comment utiliser les alertes Spring Cloud.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: 2be21b20c394ae8505ad18f2c411db7aab06215f
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689564"
---
# <a name="tutorial-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Didacticiel : Superviser les ressources Spring Cloud avec des alertes et des groupes d’actions

Les alertes Azure Spring Cloud prennent en charge la supervision des ressources en fonction de conditions telles que le stockage disponible, le taux de requêtes ou l’utilisation des données. Une alerte envoie une notification quand les taux ou les conditions répondent aux spécifications définies.

Deux étapes sont nécessaires pour configurer un pipeline d’alerte : 
1. Configurez un groupe d’actions avec les actions à entreprendre quand une alerte se déclenche, par exemple un e-mail, un SMS, un runbook ou un Webhook. Les groupes d’actions peuvent être réutilisés entre différentes alertes.
2. Configurez des règles d’alerte. Les règles lient les modèles de métrique aux groupes d’actions en fonction de l’élément cible : ressource, métrique, condition, agrégation de temps, etc.

## <a name="prerequisites"></a>Prérequis
En plus des éléments requis pour Azure Spring, ce tutoriel dépend des ressources suivantes.

* Une instance Azure Spring Cloud déployée.  Suivez notre [guide de démarrage rapide](spring-cloud-quickstart-launch-app-cli.md) pour commencer

* Une ressource Azure à superviser, par exemple la base de données implémentée dans cet article : [Guide pratique pour utiliser l’API Apache Cassandra de Spring Data avec Azure Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
 
Les procédures suivantes initialisent le **groupe d’actions** et l’**alerte** à partir de l’option **Alertes** dans le volet de navigation gauche d’une instance de Spring Cloud. (La procédure peut également démarrer à partir de la page **Vue d’ensemble d’Azure Monitor** du portail Azure.) 

Accédez à partir d’un groupe de ressources à votre instance de Spring Cloud. Sélectionnez **Alertes** dans le volet gauche, puis sélectionnez **Gérer les actions** :

![Capture d’écran de la page du groupe de ressources du portail](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Configurer un groupe d’actions

Pour commencer la procédure d’initialisation d’un nouveau **groupe d’actions**, sélectionnez **+ Ajouter un groupe d’actions**.

![Capture d’écran d’ajout d’un groupe d’actions dans le portail](media/alerts-action-groups/action-1.png)

Dans la page **Ajouter un groupe d’actions** :

 1. Spécifiez le **Nom du groupe d’actions** et le **Nom court**.

 1. Spécifiez l’**Abonnement** et le **Groupe de ressources**.

 1. Spécifiez le **Nom de l’action**.

 1. Sélectionnez le **Type d’action**.  Cela entraîne l’ouverture d’un autre volet sur la droite, qui permet de définir l’action à entreprendre au moment de l’activation.

 1. Définissez l’action à l’aide des options du volet droit.  Dans le cas présent, il s’agit d’utiliser une notification par e-mail.

 1. Cliquez sur **OK** dans le volet Actions approprié.

 1. Cliquez sur **OK** dans la boîte de dialogue **Ajouter un groupe d’actions**. 

  ![Capture d’écran de définition d’action dans le portail](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Configurer une alerte 

Les étapes précédentes ont permis de créer un **Groupe d’actions** qui utilise l’e-mail. Vous pouvez également utiliser les notifications par téléphone, les Webhooks, les fonctions Azure, etc.  

Pour configurer une **alerte**, revenez à la page **Alertes**, puis cliquez sur **Gérer les règles d’alerte**.

  ![Capture d’écran de définition d’alerte dans le portail](media/alerts-action-groups/alerts-2.png)

1. Sélectionnez la **Ressource** nécessaire pour l’alerte.

1. Cliquez sur **+ Nouvelle règle d’alerte**.

  ![Capture d’écran d’ajout d’une nouvelle règle d’alerte dans le portail](media/alerts-action-groups/alerts-3.png)

1. Dans la page **Créer une règle**, spécifiez les valeurs appropriées pour **RESSOURCE**, **CONDITION** et **ACTIONS**.  Dans le volet **ACTIONS**, sélectionnez le **Groupe d’actions** que vous avez défini.

1. Sous **DÉTAILS DE L’ALERTE**, nommez la règle d’alerte.

1. Cliquez sur **Créer une règle d'alerte**.

  ![Capture d’écran d’ajout d’une nouvelle règle d’alerte dans le portail](media/alerts-action-groups/alerts-4.png)

Vérifiez que la nouvelle règle d’alerte est activée.

  ![Capture d’écran d’ajout d’une nouvelle règle d’alerte dans le portail](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Étapes suivantes
* [Créer et gérer des groupes d’actions dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* [Comportement des alertes par SMS dans les groupes d’actions](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
* [Tutoriel : Utilisation du traçage distribué avec Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
