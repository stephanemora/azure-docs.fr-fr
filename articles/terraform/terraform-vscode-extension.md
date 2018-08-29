---
title: Extension Azure Terraform VS Code | Microsoft Docs
description: Dans cet article, vous allez voir comment installer et utiliser l’extension Terraform dans Visual Studio Code.
keywords: terraform, devops, machine virtuelle, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190809"
---
# <a name="azure-terraform-vs-code-extension"></a>Extension Azure Terraform VS Code

L’extension Microsoft Azure Terraform Visual Studio Code (VS Code) a été conçue pour accroître la productivité des développeurs lorsqu’ils créent, testent et utilisent Terraform avec Azure. L’extension fournit la prise en charge des commandes Terraform, la visualisation des graphes de ressources, ainsi que l’intégration de Cloud Shell dans Visual Studio Code.

## <a name="what-you-do"></a>Procédure

- Installez l’exécutable open source de HashiCorp Terraform sur votre ordinateur.
- Installez l’extension Terraform VS Code pour Azure sur votre installation locale de VS Code.

## <a name="what-you-learn"></a>Contenu

Dans ce tutoriel, vous allez voir comment :

- Terraform peut automatiser et simplifier le provisionnement des services Azure
- Installer et utiliser l’extension Microsoft Terraform VS Code pour les services Azure
- Utiliser VS Code pour écrire, planifier et exécuter des plans Terraform

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Un ordinateur exécutant Windows 10, Linux ou macOS 10.10+
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US).
- Un abonnement Azure actif. [Activez un compte Microsoft Azure pour un essai gratuit de 30 jours](https://azure.microsoft.com/free/).
- Une installation de l’outil open source [Terraform](https://www.terraform.io/) sur votre ordinateur local
  
## <a name="prepare-your-dev-environment"></a>Préparer votre environnement de développement

### <a name="install-git"></a>Installer Git

Pour effectuer les exercices de l’article, vous devez [installer Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Installer HashiCorp Terraform

Suivez les instructions de la page [Install Terraform](https://www.terraform.io/intro/getting-started/install.html) (en anglais) sur le site web de HashiCorp, qui aborde les sujets suivants :

- Le téléchargement de Terraform
- L’installation de Terraform
- La vérification de l’installation de Terraform

>[!Tip]
>Suivez les instructions concernant la définition de la variable système PATH.

### <a name="install-nodejs"></a>Installer Node.js

Pour utiliser Terraform dans Cloud Shell, vous devez [installer Node.js](https://nodejs.org/) 6.0 +.

>[!NOTE]
>Pour vérifier si Node.js est installé, ouvrez une fenêtre de terminal et tapez : `node -v`

### <a name="install-graphviz"></a>Installer GraphViz

Pour utiliser la fonction de visualisation de Terraform, vous devez [installer GraphViz](http://graphviz.org/).

>[!NOTE]
>Pour vérifier si GraphViz est installé, ouvrez une fenêtre de terminal et tapez : `dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>Installez l’extension Azure Terraform VS Code :

1. Lancez VS Code.
2. Cliquez sur l’icône **Extensions**.

    ![Bouton Extensions](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Utilisez la zone de texte **Rechercher des extensions dans la Place de marché** pour rechercher l’extension Azure Terraform :

    ![Rechercher les extensions VS Code dans la Place de marché](media/terraform-vscode-extension/tf-search-extensions.png)

4. Cliquez sur **Installer**.

    >[!NOTE]
    >Lorsque vous cliquez sur **Installer** pour l’extension Azure Terraform, VS Code installe automatiquement l’extension Compte Azure. L’extension Compte Azure est un fichier de dépendance que l’extension Azure Terraform utilise pour authentifier les abonnements Azure et créer des extensions de code liées à Azure.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Vérifier que l’extension Terraform est installée dans Visual Studio Code

1. Cliquez sur l’icône Extensions.
2. Tapez `@installed` dans la zone de recherche.

    ![Extensions installées](media/terraform-vscode-extension/tf-installed-extensions.png)

L’extension Azure Terraform s’affiche dans la liste des extensions installées.

![Extensions Terraform installées](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Vous pouvez maintenant exécuter toutes les commandes prises en charge de Terraform dans votre environnement Cloud Shell, à partir de VS code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Exercice 1 : Procédure de base concernant les commandes Terraform

Dans cet exercice, vous allez créer et exécuter un fichier de configuration Terraform de base, qui provisionne un nouveau groupe de ressources Azure.

### <a name="prepare-a-test-plan-file"></a>Préparer un fichier de plan de test

1. Dans VS Code, sélectionnez **Fichier > Nouveau fichier** dans la barre de menus.
2. Accédez à [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#), puis copiez le code dans le bloc de code **Example Usage** :
3. Collez le code copié dans le fichier que vous venez de créer dans VS Code.

    ![Coller le code de l’exemple d’utilisation](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Vous pouvez modifier la valeur **name** du groupe de ressources. Cependant, il doit être unique dans l’ensemble de votre abonnement Azure.

4. Dans la barre de menus, sélectionnez **Fichier > Enregistrer sous**.
5. Dans la boîte de dialogue **Enregistrer sous**, accédez à l’emplacement de votre choix, puis cliquez sur **Nouveau dossier** (modifiez le nom du nouveau dossier pour qu’il soit plus parlant que *nouveau dossier*).

    >[!NOTE]
    >Dans cet exemple, le dossier se nomme TERRAFORM-TEST-PLAN.

6. Vérifiez que le nouveau dossier est mis en surbrillance (sélectionné), puis cliquez sur **Ouvrir**.
7. Dans la boîte de dialogue **Enregistrer sous**, remplacez le nom par défaut du fichier par *main.tf*.

    ![Enregistrer le fichier sous main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Cliquez sur **Enregistrer**.
- Dans la barre de menus, sélectionnez **Fichier > Ouvrir le dossier**. Localisez, puis sélectionnez le dossier que vous venez de créer.

### <a name="run-terraform-init-command"></a>Exécuter la commande *init* de Terraform

1. Lancez Visual Studio Code.
2. Dans la barre de menus Visual Studio Code, sélectionnez **Fichier > Ouvrir le dossier...**, puis localisez et sélectionnez votre fichier *main.tf*.

    ![Fichier main.tf](media/terraform-vscode-extension/tf-main-tf.png)

3. Dans la barre de menus, sélectionnez **Afficher > Palette de commandes... > Azure Terraform : Init**.
4. Après quelques instants, lorsqu’un message vous demande *Voulez-vous ouvrir Cloud Shell ?* Cliquez sur **OK**.

    ![Message « Voulez-vous ouvrir Cloud Shell ? »](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. La première fois que vous lancez l’application web Cloud Shell à partir d’un nouveau dossier, vous devez la configurer. Cliquez sur **Ouvrir**.

    ![Premier lancement de Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. La page Bienvenue dans Azure Cloud Shell s’ouvre. Sélectionnez Bash ou PowerShell.

    ![Bienvenue dans Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Dans cet exemple, Bash (Linux) a été sélectionné.

7. Si vous n’avez pas déjà configuré un compte de stockage Azure, l’écran suivant s’affiche. Cliquez sur **Créer un stockage**.

    ![Vous n’avez aucun stockage monté](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell démarre dans l’interpréteur de commandes précédemment sélectionné, et affiche des informations concernant le lecteur cloud qu’il vient de créer pour vous.

    ![Votre service cloud a été créé.](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Vous pouvez maintenant quitter Cloud Shell.
10. Dans la barre de menus, sélectionnez **Afficher** > **Palette de commandes** > **Azure Terraform : init**.

    ![Terraform a été initialisé.](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualiser le plan

Précédemment dans ce tutoriel, vous avez installé GraphViz. Terraform peut utiliser GraphViz pour générer une représentation visuelle d’une configuration ou d’un plan d’exécution. L’extension Azure Terraform VS Code implémente cette fonctionnalité via la commande *visualize*.

- Dans la **barre de menus**, sélectionnez**Afficher > Palette de commandes > Azure Terraform : visualize**.

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

2. Tapez *yes*.

    ![Commande apply de Terraform : yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Vérifier que votre plan Terraform a été exécuté

Pour voir si votre nouveau groupe de ressources Azure a été correctement créé :

1. Ouvrez le portail Azure.
2. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**.

    ![Vérifier votre nouvelle ressource](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Votre nouveau groupe de ressources doit être répertorié dans la colonne **NOM**.

>[!NOTE]
>Vous pouvez laisser la fenêtre du portail Azure ouverte, car nous allons l’utiliser à l’étape suivante.

### <a name="run-terraform-destroy-command"></a>Exécuter la commande *destroy* de Terraform

1. Dans la barre de menus, sélectionnez **Afficher** > **Palette de commandes** > **Azure Terraform : destroy**.

    ![Commande destroy de Terraform](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Tapez *yes*.

    ![Commande destroy de Terraform : yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Vérifier que votre groupe de ressources a été détruit

Pour vérifier que Terraform a bien détruit votre nouveau groupe de ressources :

1. Cliquez sur **Actualiser** dans la page *Groupes de ressources* du portail Azure.
2. Votre groupe de ressources ne sera plus répertorié.

    ![Vérifier que le groupe de ressources a été détruit](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Exercice 2 : module *compute* de Terraform

Dans cet exercice, vous allez apprendre à charger le module *compute* de Terraform dans l’environnement Visual Studio Code.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Cloner le module terraform-azurerm-compute

1. Utilisez [ce lien](https://github.com/Azure/terraform-azurerm-compute) pour accéder au module Terraform Azure Rm Compute sur GitHub.
2. Cliquez sur **Cloner ou télécharger**.

    ![Cloner ou télécharger](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >Dans cet exemple, notre dossier a été nommé *terraform-azurerm-compute*.

### <a name="open-the-folder-in-vs-code"></a>Ouvrir le dossier dans VS Code

1. Lancez Visual Studio Code.
2. Dans la **barre de menus**, sélectionnez **Fichier > Ouvrir le dossier**, puis localisez et sélectionnez le dossier que vous avez créé à l’étape précédente.

    ![Dossier terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Initialiser Terraform

Avant de commencer à utiliser les commandes Terraform dans VS Code, vous devez télécharger les plug-ins de deux fournisseurs Azure : random et azurerm.

1. Dans le volet Terminal de l’IDE VS Code, tapez : `terraform init`

    ![Commande init de Terraform](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Tapez `az login`, puis suivez les instructions à l’écran.

### <a name="module-test-lint"></a>Test du module : *lint*

1. Dans la **barre de menus**, sélectionnez**Afficher > Palette de commandes > Azure Terraform : test**.
2. Dans la liste des options de type de test, sélectionnez **lint**.

    ![Sélectionner le type de test](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. Lorsqu’un message vous demande *Voulez-vous ouvrir Cloud Shell ?*, cliquez sur **OK**, puis suivez les instructions à l’écran.

    ![Message « Voulez-vous ouvrir Cloud Shell ? »](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Lorsque vous exécutez le test **lint** ou **end to end**, Azure utilise un service conteneur pour provisionner une machine de test dans le but d’exécuter le test. Pour cette raison, les résultats des tests peuvent prendre plusieurs minutes à s’afficher.

Après quelques instants, une liste s’affiche dans le volet Terminal, comme dans cet exemple :

![Résultats du test Lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Test du module : *end-to-end*

1. Dans la **barre de menus**, sélectionnez**Afficher > Palette de commandes > Azure Terraform : test**.
2. Dans la liste des options de type de test, sélectionnez **end to end**.

    ![Sélectionner le type de test](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. Lorsqu’un message vous demande *Voulez-vous ouvrir Cloud Shell ?*, cliquez sur **OK**, puis suivez les instructions à l’écran.

    ![Message « Voulez-vous ouvrir Cloud Shell ? »](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Lorsque vous exécutez le test **lint** ou **end to end**, Azure utilise un service conteneur pour provisionner une machine de test dans le but d’exécuter le test. Pour cette raison, les résultats des tests peuvent prendre plusieurs minutes à s’afficher.

Après quelques instants, une liste s’affiche dans le volet Terminal, comme dans cet exemple :

![Résultats du test end to end](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Étapes suivantes

Vous venez de voir comment Terraform peut simplifier le provisionnement des services Azure à partir de Visual Studio Code. Maintenant, nous vous invitons à consulter les ressources suivantes :
- Le registre des modules [Terraform Module Registry](https://registry.terraform.io/) répertorie tous les modules Terraform disponibles pour Azure et pour les autres fournisseurs pris en charge.

Les informations suivantes sont fournies pour chacun de ces modules :

- Une description des fonctionnalités générales du module et de ses caractéristiques
- Un exemple d’utilisation
- Des configurations de test, qui vous montrent comment créer, exécuter et tester chaque module sur votre ordinateur de développement local
- Un fichier Dockerfile pour générer et exécuter un environnement de développement de modules localement
