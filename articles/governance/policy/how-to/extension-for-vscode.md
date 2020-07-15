---
title: Extension Azure Policy pour Visual Studio Code
description: Découvrez comment utiliser l’extension Azure Policy pour Visual Studio Code afin de rechercher des alias Azure Resource Manager.
ms.date: 06/16/2020
ms.topic: how-to
ms.openlocfilehash: c91d39414a376b410e52c2ba60ce15ed0c5054f6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970754"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Utiliser l’extension Azure Policy pour Visual Studio Code

> S’applique à l’extension Azure Policy **0.0.21** et aux versions ultérieures.

Découvrez comment utiliser l’extension Azure Policy pour Visual Studio Code afin de rechercher des [alias](../concepts/definition-structure.md#aliases) et de vérifier les ressources et les stratégies. Tout d’abord, nous décrirons comment installer l’extension Azure Policy dans Visual Studio Code. Ensuite, nous étudierons comment rechercher des alias.

L’extension Azure Policy pour Visual Studio Code peut être installée sur toutes les plateformes prises en charge par Visual Studio Code : Windows, Linux et macOS.

> [!NOTE]
> Les modifications apportées localement aux stratégies qui s’affichent dans l’extension Azure Policy pour Visual Studio Code ne sont pas synchronisées avec Azure.

## <a name="prerequisites"></a>Prérequis

Les éléments suivants sont requis pour effectuer les étapes décrites dans cet article :

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Installer l’extension Azure Policy

Une fois tous les prérequis remplis, vous pouvez installer l’extension Azure Policy pour Visual Studio Code en suivant les étapes ci-dessous :

1. Ouvrez Visual Studio Code.

1. À partir de la barre de menus, accédez à **Afficher** > **Extensions**.

1. Dans la zone de recherche, entrez **Azure Policy**.

1. Sélectionnez **Azure Policy** dans les résultats de la recherche, puis sélectionnez **Installer**.

1. Sélectionnez **Recharger** si nécessaire.

## <a name="set-the-azure-environment"></a>Configurer l’environnement Azure

Dans le cas d’un utilisateur d’un cloud national, suivez d’abord ces étapes pour configurer l’environnement Azure :

1. Sélectionnez **File\Preferences\Settings**.

1. Recherchez la chaîne suivante : _Azure : Cloud_

1. Sélectionnez le cloud national dans la liste :

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Définir la connexion au cloud Azure par défaut pour Visual Studio Code" border="false":::

## <a name="connect-to-an-azure-account"></a>Se connecter à un compte Azure

Pour pouvoir évaluer des ressources et rechercher des alias, vous devez vous connecter à votre compte Azure. Suivez ces étapes pour vous connecter à Azure à partir de Visual Studio Code :

1. Connectez-vous à Azure à partir de l’extension Azure Policy ou de la palette de commandes.

   - Extension Azure Policy

     À partir de l’extension Azure Policy, sélectionnez **Se connecter à Azure**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Connexion au cloud Azure pour Visual Studio Code à partir de l’extension Azure Policy" border="false":::

   - Palette de commandes

     À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Azure: Se connecter**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Connexion au cloud Azure pour Visual Studio Code à partir de la palette de commandes" border="false":::

1. Suivez les instructions de connexion pour vous connecter à Azure. Une fois que vous êtes connecté, le nom de votre compte Azure s’affiche dans la barre d’état, en bas de la fenêtre Visual Studio Code.

## <a name="select-subscriptions"></a>Sélectionner des abonnements

Lors de la première connexion, seules les stratégies et les ressources d’abonnement par défaut sont chargées par l’extension Azure Policy. Pour ajouter des abonnements aux ressources et aux stratégies qui s’affichent ou en supprimer, suivez les étapes ci-dessous :

1. Démarrez la commande d’abonnement à partir de la palette de commandes ou du pied de page de la fenêtre.

   - Palette de commandes : 

     À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Azure: Sélectionner des abonnements**.

   - Pied de page de la fenêtre

     Dans le pied de page de la fenêtre en bas de l’écran, sélectionnez le segment correspondant à **Azure : \<your account\>** .

1. Utilisez la zone de filtre pour trouver rapidement des abonnements par leur nom. Ensuite, cochez ou décochez les abonnements pour définir ceux qui seront affichés par l’extension Azure Policy. Une fois que vous avez terminé d’ajouter ou de supprimer des abonnements, sélectionnez **OK**.

## <a name="search-for-and-view-resources"></a>Rechercher et afficher des ressources

L’extension Azure Policy liste les ressources des abonnements sélectionnés par fournisseur de ressources et par groupe de ressources dans le volet **Ressources**. L’arborescence comprend les regroupements de ressources suivants au sein de l’abonnement sélectionné ou au niveau de l’abonnement :

- **Fournisseurs de ressources**
  - Chacun des fournisseurs de ressources inscrits avec des ressources et les ressources enfants associées qui possèdent des alias de stratégie
- **Groupes de ressources**
  - Toutes les ressources suivant le groupe de ressources dans lequel elles se trouvent

Par défaut, l’extension filtre la partie « Fournisseur de ressources » sur les ressources existantes et les ressources qui possèdent des alias de stratégie. Modifiez ce comportement dans **Paramètres** > **Extensions** > **Azure Policy** pour voir tous les fournisseurs de ressources sans filtrage.

Les clients disposant de centaines ou de milliers de ressources dans un même abonnement préféreront probablement un moyen de trouver leurs ressources avec possibilité de recherche. L’extension Azure Policy permet de rechercher une ressource spécifique en suivant les étapes suivantes :

1. Lancez l’interface de recherche à partir de l’extension Azure Policy ou de la palette de commandes.

   - Extension Azure Policy

     Dans l’extension Azure Policy, placez le curseur sur le panneau **Ressources** et sélectionnez les points de suspension, puis **Rechercher des ressources**.

   - Palette de commandes :

     Dans la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Ressources : Rechercher des ressources**.

1. Si plusieurs abonnements sont sélectionnés pour l’affichage, utilisez le filtre pour sélectionner celui dans lequel la recherche devra être effectuée.

1. Utilisez le filtre pour sélectionner le groupe de ressources dans lequel la recherche devra être effectuée, au sein de l’abonnement choisi.

1. Utilisez le filtre pour sélectionner la ressource à afficher. Le filtre fonctionne à la fois pour le nom et le type de ressource.

## <a name="discover-aliases-for-resource-properties"></a>Découvrir les alias des propriétés de ressources

Lorsqu’une ressource est sélectionnée, que ce soit par le biais de l’interface de recherche ou dans l’arborescence, l’extension Azure Policy ouvre le fichier JSON représentant cette ressource et toutes ses valeurs de propriété Azure Resource Manager.

Lorsqu’une ressource est ouverte, pointez sur le nom ou la valeur de la propriété Resource Manager pour afficher l’alias Azure Policy s’il en existe un. Dans cet exemple, la ressource a pour type `Microsoft.Compute/virtualMachines` et le curseur est placé sur la propriété **properties.storageProfile.imageReference.offer**, ce qui affiche les alias correspondants.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Affichage par pointage des alias de propriété Resource Manager dans l’extension Azure Policy" border="false":::

> [!NOTE]
> L’extension VS Code expose uniquement les propriétés du mode Resource Manager, et n’affiche pas les propriétés du [mode Fournisseur de ressources](../concepts/definition-structure.md#mode).

## <a name="search-for-and-view-policies-and-assignments"></a>Rechercher et afficher des stratégies et des affectations

L’extension Azure Policy liste les types et les affectations de stratégie sous la forme d’une arborescence pour les abonnements à afficher dans le volet **Stratégies**. Les clients disposant de centaines ou de milliers de stratégies ou d’affectations dans un même abonnement préféreront probablement un moyen de trouver leurs stratégies ou leurs affectations avec possibilité de recherche. L’extension Azure Policy permet de rechercher une stratégie ou une affectation spécifique en suivant les étapes suivantes :

1. Lancez l’interface de recherche à partir de l’extension Azure Policy ou de la palette de commandes.

   - Extension Azure Policy

     Dans l’extension Azure Policy, placez le curseur sur le panneau **Stratégies** et sélectionnez les points de suspension, puis **Rechercher des stratégies**.

   - Palette de commandes :

     Dans la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Stratégies : Rechercher des stratégies**.

1. Si plusieurs abonnements sont sélectionnés pour l’affichage, utilisez le filtre pour sélectionner celui dans lequel la recherche devra être effectuée.

1. Utilisez le filtre pour sélectionner l’affectation ou le type de stratégie dans lequel la recherche devra être effectuée, au sein de l’abonnement choisi.

1. Utilisez le filtre pour sélectionner la stratégie ou l’affectation à afficher. Le filtre fonctionne sur _displayName_ pour la définition ou l’affectation de stratégie.

Lorsqu’une stratégie ou une affectation est sélectionnée, que ce soit par le biais de l’interface de recherche ou dans l’arborescence, l’extension Azure Policy ouvre le fichier JSON représentant la stratégie ou l’affectation et toutes ses valeurs de propriété Resource Manager. L’extension peut valider le schéma JSON Azure Policy ouvert.

## <a name="sign-out"></a>Se déconnecter

À partir de la barre de menus, accédez à **Afficher** > **Palette de commandes**, puis entrez **Azure: Se déconnecter**.

## <a name="next-steps"></a>Étapes suivantes

- Consultez des exemples à la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [corriger des ressources non conformes](remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).