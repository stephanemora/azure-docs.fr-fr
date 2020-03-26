---
title: Tutoriel - Configurer l’extension Azure Terraform Visual Studio Code
description: Découvrez comment installer et utiliser l’extension Azure Terraform dans Visual Studio Code.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: fcb47024fd26f061ca4475b01d00f1ae13303b61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472279"
---
# <a name="tutorial-configure-the-azure-terraform-visual-studio-code-extension"></a>Tutoriel : Configurer l’extension Azure Terraform Visual Studio Code

L’extension Azure Terraform Visual Studio Code vous permet de travailler avec Terraform à partir de l’éditeur. Avec cette extension, vous pouvez créer, tester et exécuter des configurations Terraform. L’extension prend également en charge la visualisation des graphes de ressources.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Automatiser le provisionnement des services Azure à l’aide de Terraform.
> * Installer et utiliser l’extension Terraform Visual Studio Code pour les services Azure.
> * Utiliser Visual Studio Code pour écrire, planifier et exécuter des plans Terraform.

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

- **Terraform** : [Installez et configurez Terraform](terraform-install-configure.md).

- **Visual Studio Code** : Installez la version de [Visual Studio Code](https://code.visualstudio.com/download) adaptée à votre environnement.

## <a name="prepare-your-dev-environment"></a>Préparer votre environnement de développement

### <a name="install-git"></a>Installer Git

Pour effectuer les exercices de l’article, vous devez [installer Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Installer HashiCorp Terraform

Suivez les instructions de la page [Install Terraform](https://www.terraform.io/intro/getting-started/install.html) (en anglais) sur le site web de HashiCorp, qui aborde les sujets suivants :

- Le téléchargement de Terraform
- Installation de Terraform
- La vérification de l’installation de Terraform

>[!Tip]
>Suivez les instructions concernant la définition de la variable système PATH.

### <a name="install-nodejs"></a>Installer Node.js

Pour utiliser Terraform dans Cloud Shell, vous devez [installer Node.js](https://nodejs.org/) 6.0 +.

>[!NOTE]
>Pour vérifier si Node.js est installé, ouvrez une fenêtre de terminal et tapez `node -v`.

### <a name="install-graphviz"></a>Installer GraphViz

Pour utiliser la fonction de visualisation de Terraform, vous devez [installer GraphViz](https://graphviz.org/).

>[!NOTE]
>Pour vérifier si GraphViz est installé, ouvrez une fenêtre de terminal et tapez `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Installer l’extension Azure Terraform Visual Studio Code

1. Lancez Visual Studio Code.

1. Sélectionnez **Extensions**.

    ![Bouton Extensions](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Utilisez la zone de texte **Rechercher des extensions dans la Place de marché** pour rechercher l’extension Azure Terraform :

    ![Rechercher les extensions Visual Studio Code dans la Place de marché](media/terraform-vscode-extension/tf-search-extensions.png)

1. Sélectionnez **Installer**.

    >[!NOTE]
    >Lorsque vous sélectionnez **Installer** pour l’extension Azure Terraform, Visual Studio Code installe automatiquement l’extension Compte Azure. L’extension Compte Azure est un fichier de dépendance que l’extension Azure Terraform utilise pour authentifier les abonnements Azure et créer des extensions de code liées à Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Vérifier que l’extension Terraform est installée dans Visual Studio Code

1. Sélectionnez **Extensions**.

1. Tapez `@installed` dans la zone de recherche.

    ![Extensions installées](media/terraform-vscode-extension/tf-installed-extensions.png)

L’extension Azure Terraform s’affiche dans la liste des extensions installées.

![Extensions Terraform installées](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Vous pouvez maintenant exécuter toutes les commandes Terraform prises en charge dans votre environnement Cloud Shell, à partir de Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Exercice 1 : Procédure de base concernant les commandes Terraform

Dans cet exercice, vous allez créer et exécuter un fichier de configuration Terraform de base, qui provisionne un nouveau groupe de ressources Azure.

### <a name="prepare-a-test-plan-file"></a>Préparer un fichier de plan de test

1. Dans Visual Studio Code, sélectionnez **Fichier > Nouveau fichier** dans la barre de menus.

1. Dans votre navigateur, accédez à la [page Terraform azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#), puis copiez le code dans le bloc de code **Exemple d’utilisation** :

    ![Exemple d’utilisation](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Collez le code copié dans le fichier que vous venez de créer dans Visual Studio Code.

    ![Coller le code de l’exemple d’utilisation](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Vous pouvez modifier la valeur **name** du groupe de ressources. Cependant, il doit être unique dans l’ensemble de votre abonnement Azure.

1. Dans la barre de menus, sélectionnez **Fichier > Enregistrer sous**.

1. Dans la boîte de dialogue **Enregistrer sous**, accédez à l’emplacement de votre choix, puis sélectionnez **Nouveau dossier**. (modifiez le nom du nouveau dossier pour qu’il soit plus parlant que *nouveau dossier*).

    >[!NOTE]
    >Dans cet exemple, le dossier se nomme TERRAFORM-TEST-PLAN.

1. Vérifiez que le nouveau dossier est mis en surbrillance (sélectionné), puis sélectionnez **Ouvrir**.

1. Dans la boîte de dialogue **Enregistrer sous**, remplacez le nom par défaut du fichier par *main.tf*.

    ![Enregistrer le fichier sous main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Sélectionnez **Enregistrer**.
1. Dans la barre de menus, sélectionnez **Fichier > Ouvrir le dossier**. Localisez, puis sélectionnez le dossier que vous venez de créer.

### <a name="run-terraform-init-command"></a>Exécuter la commande *init* de Terraform

1. Lancez Visual Studio Code.

1. Dans la barre de menus Visual Studio Code, sélectionnez **Fichier > Ouvrir le dossier...** , puis localisez et sélectionnez votre fichier *main.tf*.

    ![Fichier main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. Dans la barre de menus, sélectionnez **Afficher > Palette de commandes... > Azure Terraform : Init**.

1. Lorsque la confirmation s’affiche, sélectionnez **OK**.

    ![Message « Voulez-vous ouvrir Cloud Shell ? »](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. La première fois que vous lancez Cloud Shell à partir d’un nouveau dossier, vous êtes invité à créer une application web. Sélectionnez **Ouvrir**.

    ![Premier lancement de Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. La page Bienvenue dans Azure Cloud Shell s’ouvre. Sélectionnez Bash ou PowerShell.

    ![Bienvenue dans Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Dans cet exemple, Bash (Linux) a été sélectionné.

1. Si vous n’avez pas déjà configuré un compte de stockage Azure, l’écran suivant s’affiche. Sélectionnez **Créer le stockage**.

    ![Vous n’avez aucun stockage monté](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell démarre dans l’interpréteur de commandes précédemment sélectionné, et affiche des informations concernant le lecteur cloud qu’il vient de créer pour vous.

    ![Votre service cloud a été créé.](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Vous pouvez maintenant quitter Cloud Shell.

1. Dans la barre de menus, sélectionnez **Afficher** > **Palette de commandes** > **Azure Terraform : init**.

    ![Terraform a été initialisé.](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualiser le plan

Précédemment dans ce tutoriel, vous avez installé GraphViz. Terraform peut utiliser GraphViz pour générer une représentation visuelle d’une configuration ou d’un plan d’exécution. L’extension Azure Terraform Visual Studio Code implémente cette fonctionnalité via la commande *visualize*.

- Dans la barre de menus, sélectionnez **Afficher > Palette de commandes > Azure Terraform : Visualiser**.

    ![Visualiser le plan](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Exécuter la commande *plan* de Terraform

La commande *plan* de Terraform est utilisée pour vérifier si le plan d’exécution d’un ensemble de modifications va avoir le résultat souhaité.

>[!NOTE]
>La commande *plan* de Terraform n’apporte pas de modifications à vos ressources Azure. Pour apporter les modifications contenues dans votre plan, utilisez la commande *apply* de Terraform.

- Dans la barre de menus, sélectionnez **Afficher** > **Palette de commandes** > **Azure Terraform : plan**.

    ![plan Terraform](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Exécuter la commande *apply* de Terraform

Lorsque vous êtes satisfait des résultats de l’exécution de *plan*, vous pouvez exécuter la commande *apply*.

1. Dans la barre de menus, sélectionnez **Afficher** > **Palette de commandes** > **Azure Terraform : apply**.

    ![Terraform apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Entrez `yes`.

    ![Commande apply de Terraform : yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Vérifier que votre plan Terraform a été exécuté

Pour voir si votre nouveau groupe de ressources Azure a été correctement créé :

1. Ouvrez le portail Azure.

1. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.

    ![Vérifier votre nouvelle ressource](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Votre nouveau groupe de ressources doit être répertorié dans la colonne **NOM**.

>[!NOTE]
>Vous pouvez laisser la fenêtre du portail Azure ouverte, car nous allons l’utiliser à l’étape suivante.

### <a name="run-terraform-destroy-command"></a>Exécuter la commande *destroy* de Terraform

1. Dans la barre de menus, sélectionnez **Afficher** > **Palette de commandes** > **Azure Terraform : destroy**.

    ![Commande destroy de Terraform](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Entrez *yes*.

    ![Commande destroy de Terraform : yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Vérifier que votre groupe de ressources a été détruit

Pour vérifier que Terraform a bien détruit votre nouveau groupe de ressources :

1. Sélectionnez **Actualiser** dans la page **Groupes de ressources** du portail Azure.

1. Votre groupe de ressources ne sera plus répertorié.

    ![Vérifier que le groupe de ressources a été détruit](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Exercice 2 : Module *compute* de Terraform

Dans cet exercice, vous allez apprendre à charger le module *compute* de Terraform dans l’environnement Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Cloner le module terraform-azurerm-compute

1. Utilisez [ce lien](https://github.com/Azure/terraform-azurerm-compute) pour accéder au module Terraform Azure Rm Compute sur GitHub.

1. Sélectionnez **Cloner ou télécharger**.

    ![Cloner ou télécharger](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Dans cet exemple, notre dossier a été nommé *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Ouvrir le dossier dans Visual Studio Code

1. Lancez Visual Studio Code.

1. Dans la barre de menus, sélectionnez **Fichier > Ouvrir le dossier**, puis localisez et sélectionnez le dossier que vous avez créé à l’étape précédente.

    ![Dossier terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Initialiser Terraform

Avant de commencer à utiliser les commandes Terraform dans Visual Studio Code, vous devez télécharger les plug-ins de deux fournisseurs Azure : random et azurerm.

1. Dans le volet Terminal de l’IDE Visual Studio Code, tapez `terraform init`.

    ![Commande init de Terraform](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Tapez `az login`, appuyez sur **Entrée**, puis suivez les instructions à l’écran.

### <a name="module-test-lint"></a>Test du module : *lint*

1. Dans la barre de menus, sélectionnez **Afficher > Palette de commandes > Azure Terraform : Exécuter le test**.

1. Dans la liste des options de type de test, sélectionnez **lint**.

    ![Sélectionner le type de test](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Lorsque la confirmation s’affiche, sélectionnez **OK**, puis suivez les instructions à l’écran.

    ![Message « Voulez-vous ouvrir Cloud Shell ? »](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Lorsque vous exécutez le test **lint** ou **end to end**, Azure utilise un service conteneur pour provisionner une machine de test dans le but d’exécuter le test. Pour cette raison, les résultats des tests peuvent prendre plusieurs minutes à s’afficher.

Après quelques instants, une liste s’affiche dans le volet Terminal, comme dans cet exemple :

![Résultats du test Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="test-the-module"></a>Tester le module

1. Dans la barre de menus, sélectionnez **Afficher > Palette de commandes > Azure Terraform : Exécuter le test**.

1. Dans la liste des options de type de test, sélectionnez **end to end**.

    ![Sélectionner le type de test](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Lorsque la confirmation s’affiche, sélectionnez **OK**, puis suivez les instructions à l’écran.

    ![Message « Voulez-vous ouvrir Cloud Shell ? »](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Lorsque vous exécutez le test **lint** ou **end to end**, Azure utilise un service conteneur pour provisionner une machine de test dans le but d’exécuter le test. Pour cette raison, les résultats des tests peuvent prendre plusieurs minutes à s’afficher.

Après quelques instants, une liste s’affiche dans le volet Terminal, comme dans cet exemple :

![Résultats du test](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Liste des modules Terraform disponibles pour Azure (et pour les autres fournisseurs pris en charge)](https://registry.terraform.io/)