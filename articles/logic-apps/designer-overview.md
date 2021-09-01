---
title: À propos du concepteur de workflow monolocataire
description: Découvrez comment le concepteur dans Azure Logic Apps monolocataire vous aide à créer visuellement des workflows par le biais du portail Azure. Découvrez les avantages et les fonctionnalités de cette dernière version.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: ad280e8c052b14ce64734012c3677f8fd2ce5096
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129202"
---
# <a name="about-the-workflow-designer-in-single-tenant-azure-logic-apps"></a>À propos du concepteur de workflow dans Azure Logic Apps monolocataire

Lorsque vous travaillez avec Azure Logic Apps dans le portail Azure, vous pouvez modifier vos [*workflows*](logic-apps-overview.md#workflow) visuellement ou par programmation. Après avoir ouvert une [ressource d’*application logique*](logic-apps-overview.md#logic-app) dans le portail, dans le menu des ressources sous **Développeur**, vous pouvez choisir entre la [vue **Code**](#code-view) et la vue **Concepteur**. Lorsque vous souhaitez développer, modifier et exécuter visuellement votre workflow, sélectionnez le mode concepteur. Vous pouvez basculer entre le mode concepteur et le mode code à tout moment.

> [!IMPORTANT]
> Actuellement, la dernière version du concepteur n’est disponible que pour les ressources d’application logique *Standard*, qui s’exécutent dans l’environnement *à locataire unique* Azure Logic Apps. Pour plus d’informations sur les différents types de ressources et d’environnements d’exécution dans Logic Apps, consultez [Architecture monolocataire ou multilocataire et environnement de service d’intégration pour Azure Logic Apps](single-tenant-overview-compare.md).

:::image type="content" source="./media/designer-overview/choose-developer-view.png" alt-text="Capture d’écran d’une page de ressources d’application logique dans le portail Azure, montrant les options de barre latérale pour afficher un workflow en mode Code ou Concepteur.":::

Lorsque vous sélectionnez la vue **Concepteur**, votre workflow s’ouvre dans le concepteur de workflow.

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="Capture d’écran montrant le concepteur de workflow et un exemple de workflow d’entreprise utilisant des commutateurs et des conditions." lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

## <a name="prerequisites"></a>Prérequis

- Un compte et un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).
- Une ressource d’application logique *Standard* dans un [environnement à locataire unique](single-tenant-overview-compare.md). Pour plus d’informations, consultez [Créer un workflow d’intégration avec Azure Logic Apps monolocataire (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md).
- Un workflow pour votre application logique monolocataire.

## <a name="latest-version-features"></a>Fonctionnalités de la dernière version

Le dernier concepteur de workflow offre une nouvelle expérience avec des fonctionnalités et des avantages remarquables, par exemple :

- Nouveau moteur de disposition qui prend en charge des workflows plus complexes. 
- Vous pouvez créer et afficher des workflows compliqués correctement et facilement, grâce au nouveau moteur de disposition, à un canevas plus compact et à la mise à jour de la disposition basée sur une carte.
- Ajoutez et modifiez des étapes à l’aide de panneaux distincts de la disposition du workflow. Cette modification vous donne un canevas plus clair et propre pour afficher la disposition de votre workflow. Pour plus d’informations, consultez [Ajouter des étapes aux workflows](#add-steps-to-workflows).
- Naviguez entre les étapes de votre workflow sur le concepteur à l’aide de la navigation au clavier.
  - Se déplacer vers la carte suivante : **Ctrl** + **Flèche bas (&darr;)**
  - Se déplacer vers la carte précédente : **Ctrl** + **Flèche haut (&uarr;)**

## <a name="add-steps-to-workflows"></a>Ajouter des étapes aux workflows

Le concepteur de workflow fournit un moyen visuel d’ajouter, de modifier et de supprimer des étapes dans votre workflow. Comme première étape de votre workflow, ajoutez toujours un [*déclencheur*](logic-apps-overview.md#trigger). Ensuite, remplissez votre workflow en ajoutant une ou plusieurs [*actions*](logic-apps-overview.md#action).

Pour ajouter le déclencheur ou une action à votre workflow, procédez comme suit :

1. Ouvrez votre workflow dans le concepteur.
1. Sur le concepteur, sélectionnez **Choisir une opération**, ce qui ouvre un volet nommé **Ajouter un déclencheur** ou **Ajouter une action**. 
1. Dans le volet ouvert, recherchez une opération en filtrant la liste des manières suivantes :
    1. Entrez un service, un connecteur ou une catégorie dans la barre de recherche pour afficher les opérations associées.  Par exemple, `Azure Cosmos DB` ou `Data Operations`. 
    1. Si vous connaissez l’opération spécifique que vous souhaitez utiliser, entrez le nom dans la barre de recherche.  Par exemple, `Call an Azure function` ou `When an HTTP request is received`.
    1. Sélectionnez l’onglet **Intégré** pour afficher uniquement les catégories d’[*opérations intégrées*](logic-apps-overview.md#built-in-operations). Ou sélectionnez l’onglet **Azure** pour afficher d’autres catégories d’opérations disponibles via Azure.
    1. Vous pouvez visualiser uniquement les déclencheurs ou les actions en sélectionnant l’onglet **Déclencheurs** ou **Actions**. Cependant, vous ne pouvez ajouter un déclencheur qu’en première étape et une action comme étape suivante. En fonction de la catégorie d’opération, seuls des déclencheurs ou des actions peuvent être disponibles.
    :::image type="content" source="./media/designer-overview/designer-add-operation.png" alt-text="Capture d’écran du concepteur Logic Apps dans le portail Azure, montrant un workflow en cours de modification pour ajouter une nouvelle opération." lightbox="./media/designer-overview/designer-add-operation.png":::
1. Cliquez sur l’opération que vous souhaitez utiliser. 
    :::image type="content" source="./media/designer-overview/designer-filter-operations.png" alt-text="Capture d’écran du concepteur Logic Apps montrant un volet des opérations possibles qui peuvent être filtrées par service ou par nom." lightbox="./media/designer-overview/designer-filter-operations.png":::
1. Configurez votre déclencheur ou action en fonction des besoins.
    1. Les champs obligatoires ont un astérisque rouge (&ast;) devant le nom.
    1. Certains déclencheurs et actions peuvent vous obliger à créer une connexion à un autre service. Vous devrez peut-être vous connecter à un compte ou entrer des informations d’identification pour un service. Par exemple, si vous souhaitez utiliser le connecteur Office 365 Outlook pour envoyer un e-mail, vous devez autoriser votre compte de messagerie Outlook.
    1. Certains déclencheurs et actions utilisent du contenu dynamique, où vous pouvez sélectionner des variables au lieu d’entrer des informations ou des expressions.
1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer vos modifications. Cette étape vérifie également que votre workflow est valide. 

## <a name="code-view"></a>Mode code

La vue **Code** vous permet de modifier directement le fichier de définition de workflow au format JSON. Assurez-vous de sélectionner **Enregistrer** pour enregistrer les modifications que vous apportez dans cette vue. 

> [!TIP]
> La vue **Code** est également un moyen facile de trouver et de copier la définition du workflow, au lieu d’utiliser l’interface de ligne de commande Azure (Azure CLI) ou d’autres méthodes.

:::image type="content" source="./media/designer-overview/code-view.png" alt-text="Capture d’écran d’un workflow Logic Apps en mode Code, montrant la définition de workflow JSON en cours de modification dans le portail Azure.":::


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un workflow d’intégration avec Azure Logic Apps monolocataire (Standard) dans le portail Azure](create-single-tenant-workflows-azure-portal.md)
