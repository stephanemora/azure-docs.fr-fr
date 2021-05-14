---
title: Gérer les applications logiques dans le portail Azure
description: Modifiez, activez, désactivez ou supprimez des applications logiques à l’aide du portail Azure.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.custom: mvc
ms.date: 04/23/2021
ms.openlocfilehash: 4c4a3b1e5d165681e921d2fadeadc5dea9633d41
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162902"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Gérer les applications logiques dans le portail Azure

Vous pouvez gérer les applications logiques à l’aide du [portail Azure](https://portal.azure.com) ou de [Visual Studio](manage-logic-apps-with-visual-studio.md). Cet article explique comment modifier, désactiver, activer ou supprimer des applications logiques dans le portail Azure. Si vous débutez avec Azure Logic Apps, consultez [Qu’est-ce qu’Azure Logic Apps ?](logic-apps-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Une application logique existante. Pour en savoir plus sur la création d’une application logique par le biais du portail Azure, consultez [Démarrage rapide : Créer votre premier workflow à l’aide d’Azure Logic Apps - Portail Azure](quickstart-create-first-logic-app-workflow.md).

<a name="find-logic-app"></a>

## <a name="find-and-open-a-logic-app"></a>Recherche et ouverture d’une application logique

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte Azure.

1. Dans la zone de recherche du portail, entrez `logic apps`, puis sélectionnez **Applications logiques**.

1. Dans la liste des applications logiques, recherchez la vôtre en parcourant ou en filtrant la liste.

1. Pour ouvrir votre application logique, sélectionnez l’application que vous souhaitez gérer.

## <a name="view-logic-app-properties"></a>Afficher les propriétés de l’application logique

1. Dans le portail Azure, [recherchez et ouvrez votre application logique](#find-logic-app).

1. Dans le menu de votre application logique, sous **Paramètres**, sélectionnez **Propriétés**.

1. Dans le volet **Propriétés**, vous pouvez afficher et copier les informations suivantes sur votre application logique :

   * **Nom**
   * **ID de ressource**
   * **Groupe de ressources**
   * **Lieu**
   * **Type** 
   * **Nom d’abonnement**
   * **Identifiant d’abonnement**
   * **Point de terminaison de l’accès**
   * **Adresses IP sortantes du runtime**
   * **Accéder aux adresses IP du point de terminaison**
   * **Adresses IP sortantes du connecteur**

<a name="disable-enable-logic-apps"></a>

## <a name="disable-or-enable-logic-apps"></a>Activer ou désactiver des applications logiques

Pour désactiver le déclencheur la prochaine fois que la condition de déclenchement est remplie, désactivez votre application logique. Sur le Portail Azure, il est possible d’activer et de désactiver une [application logique spécifique](#disable-enable-single-logic-app) ou [plusieurs applications logiques en même temps](#disable-or-enable-multiple-logic-apps). La désactivation d’une application logique affecte les instances de workflow de différentes manières :

* Le service Logic Apps continue toutes les exécutions en cours et en attente jusqu’à ce qu’elles se terminent. En fonction du volume ou du backlog, ce processus peut prendre du temps.

* Le service Logic Apps ne crée pas ni n’exécute de nouvelles instances de workflow.

* Le déclencheur ne se déclenche pas la prochaine fois que ses conditions sont remplies. Toutefois, l’état du déclencheur mémorise le point auquel l’application logique a été arrêtée. Ainsi, si vous la réactivez, le déclencheur se met en œuvre pour tous les éléments non traités depuis la dernière exécution.

  Pour empêcher le déclencheur de se mettre en œuvre pour les éléments non traités depuis la dernière exécution, effacez l’état du déclencheur avant de réactiver l’application logique :

  1. Dans l’application logique, modifiez n’importe quelle partie du déclencheur du workflow.
  1. Enregistrez vos modifications. Cette étape réinitialise l’état actuel de votre déclencheur.
  1. [Réactivez votre application logique](#disable-enable-single-logic-app).

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-a-single-logic-app"></a>Activation et désactivation d’une application logique spécifique

1. Dans le portail Azure, [recherchez et ouvrez votre application logique](#find-logic-app).

1. Dans le menu de votre application logique, sélectionnez **Vue d’ensemble**.

   * Pour désactiver votre application logique, sélectionnez **Désactiver** dans la barre d’outils du volet Vue d’ensemble.
   * Pour activer votre application logique, sélectionnez **Activer** dans la barre d’outils du volet Vue d’ensemble.

     > [!NOTE]
     > Si votre application logique est déjà désactivée, vous ne voyez que l’option **Activer**.
     > Si votre application logique est déjà activée, vous ne voyez que l’option **Désactiver**.

1. Pour savoir si l’opération a réussi ou échoué, ouvrez la liste **Notifications** (icône représentant une cloche) dans la barre d’outils Azure principale.

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Activer ou désactiver plusieurs applications logiques

1. Dans la zone de recherche principale du Portail Azure, entrez `logic apps`, puis sélectionnez **Applications logiques**.

1. Sur la page **Applications logiques**, passez en revue la colonne **État** de l’application logique.

1. Dans la colonne des cases à cocher, sélectionnez les applications logiques que vous souhaitez arrêter ou démarrer.

   * Pour arrêter les applications logiques en cours d’exécution sélectionnées, sélectionnez **Désactiver/arrêter** dans la barre d’outils du volet Vue d’ensemble. Confirmez votre sélection.
   * Pour démarrer les applications logiques arrêtées sélectionnées, sélectionnez **Activer/démarrer** dans la barre d’outils du volet Vue d’ensemble.

1. Pour savoir si l’opération a réussi ou échoué, ouvrez la liste **Notifications** (icône représentant une cloche) dans la barre d’outils Azure principale.

   > [!TIP]
   > Si l’état ne s’affiche pas, actualisez la page.

<a name="delete-logic-apps"></a>

## <a name="delete-logic-apps"></a>Supprimer des applications logiques

Il est possible de supprimer une application logique spécifique ou plusieurs applications logiques en même temps. La suppression d’une application logique affecte les instances de workflow de différentes manières :

* Le service Logic Apps met tout en œuvre pour annuler les exécutions en cours et en attente.

  Même avec un gros volume ou un backlog important, la plupart des exécutions sont annulées avant qu’elles ne finissent ou ne démarrent. Toutefois, le processus d’annulation peut prendre du temps. Il peut arriver que certaines exécutions soient lancées dans l’intervalle.

* Le service Logic Apps ne crée pas ni n’exécute de nouvelles instances de workflow.

* Si, après avoir supprimé un workflow, vous recréez le même, les métadonnées de ce dernier sont différentes de celles du workflow supprimé. Vous devez enregistrer de nouveau les workflows qui ont appelé le workflow supprimé. L’appelant obtient ainsi les bonnes informations sur le workflow recréé. Dans le cas contraire, les appels au workflow recréé échouent avec une erreur `Unauthorized`. Ce comportement s’applique aussi aux workflows qui utilisent des artefacts dans les comptes d’intégration et à ceux qui appellent des fonctions Azure.

1. Dans la zone de recherche principale du Portail Azure, entrez `logic apps`, puis sélectionnez **Applications logiques**.

1. Dans la colonne des cases à cocher de la liste **Applications logiques**, sélectionnez une ou plusieurs applications logiques à supprimer. Dans la barre d’outils, sélectionnez **Supprimer**.

1. Quand la boîte de confirmation s’affiche, entrez `yes`, puis sélectionnez **Supprimer**.

1. Pour savoir si l’opération a réussi ou échoué, ouvrez la liste **Notifications** (icône représentant une cloche) dans la barre d’outils Azure principale.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Gestion des versions des applications logiques

Il est possible d’utiliser le Portail Azure pour la gestion de version des applications logiques. Vous y trouverez l’historique des versions de votre application logique et pourrez promouvoir les versions précédentes.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Recherche et affichage des versions précédentes

1. Dans le portail Azure, [recherchez et ouvrez votre application logique](#find-logic-app).

1. Dans le menu de l’application logique, sélectionnez **Versions** sous **Outils de développement**.

   ![Capture d’écran de l’application logique dans le portail Azure, montrant une sélection de la page Versions située sous Outils de développement.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Sélectionnez la **Version** de votre application logique à gérer dans la liste. Vous pouvez entrer l’identificateur de **Version** dans la barre de recherche pour filtrer la liste.

1. Sur la page **Version de l’historique** apparaissent les détails de la version précédente en mode lecture seule. Vous pouvez choisir entre les modes **Concepteur** et **Code** de Logic Apps.

   ![Capture d’écran de la page Version de l’historique de Logic Apps, montrant les options de mode Code et de mode Création.](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Promotion de versions précédentes

1. Dans l’historique des versions de l’application logique, [recherchez et sélectionnez la version que vous souhaitez promouvoir](#find-version-history).

1. Sur la page **Version de l’historique**, sélectionnez **Promouvoir**.

   ![Capture d’écran de la version de l’historique de l’application logique, avec le bouton pour promouvoir une version précédente.](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. Sur la page **Concepteur d’applications logiques** qui s’ouvre, modifiez la version que vous promouvez en fonction des besoins. Vous pouvez basculer entre les modes **Concepteur** et **Code**. Il est également possible de mettre à jour **Paramètres**, **Modèles** et **Connecteurs**.

   ![Capture d’écran du Concepteur Logic Apps, avec le bouton pour promouvoir une version précédente d’une application logique.](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. Pour enregistrer les mises à jour et terminer la promotion de la version précédente, sélectionnez **Enregistrer**. (Pour annuler vos modifications, sélectionnez **Ignorer**.) 

   Quand vous [affichez à nouveau l’historique des versions de votre application logique](#find-version-history), la version promue apparaît en haut de la liste, avec un nouvel identificateur.

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller les applications logiques](monitor-logic-apps.md)
