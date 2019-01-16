---
title: Créer un modèle de base Terraform dans Azure à l’aide de Yeoman
description: Découvrez comment créer un modèle de base Terraform dans Azure à l’aide de Yeoman.
services: terraform
ms.service: terraform
keywords: terraform, devops, machine virtuelle, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 36e4b424cdb961920fccdf7f050e28447ccbd6cf
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074517"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Créer un modèle de base Terraform dans Azure à l’aide de Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) permet de créer facilement des infrastructures sur Azure. [Yeoman](http://yeoman.io/) simplifie considérablement le travail du développeur de module pour la création de modules Terraform tout en offrant un excellent framework de *meilleures pratiques*.

Dans cet article, vous allez apprendre à utiliser le générateur de module Yeoman pour créer un modèle Terraform de base. Vous découvrirez ensuite comment tester votre nouveau modèle Terraform à l’aide de deux méthodes différentes :

- Exécuter le module Terraform à l’aide d’un fichier Docker créé dans cet article
- Exécuter le module Terraform en mode natif dans Azure Cloud Shell

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- **Visual Studio Code** : nous allons utiliser [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) pour examiner les fichiers créés par le générateur Yeoman. Toutefois, vous pouvez utiliser l'éditeur de code de votre choix.
- **Terraform** : [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) doit être installé pour exécuter le module créé par Yeoman.
- **Docker** : nous allons utiliser [Docker](https://www.docker.com/get-started) pour exécuter le module créé par le générateur Yeoman. (Si vous préférez, vous pouvez utiliser Ruby à la place de Docker pour exécuter l’exemple de module.)
- **Langage de programmation Go** : [Go](https://golang.org/) doit être installé, car les cas de test générés par Yeoman sont écrits en Go.

>[!NOTE]
>La plupart des procédures de ce tutoriel impliquent des entrées de ligne de commande. Les étapes décrites ici s’appliquent à tous les systèmes d’exploitation et tous les outils de ligne de commande. Dans nos exemples, nous avons choisi d’utiliser PowerShell pour l’environnement local et Git Bash pour l’environnement Cloud Shell.

## <a name="prepare-your-environment"></a>Préparation de votre environnement

### <a name="install-nodejs"></a>Installer Node.js

Pour utiliser Terraform dans Cloud Shell, vous devez [installer Node.js](https://nodejs.org/en/download/) 6.0 +.

>[!NOTE]
>Pour vérifier que Node.js est installé, ouvrez une fenêtre de terminal et tapez `node --version`.

### <a name="install-yeoman"></a>Installer Yeoman

À partir d’une invite de commandes, entrez `npm install -g yo`

![Installer Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Installer le modèle Yeoman pour le module Terraform

À partir d’une invite de commandes, entrez `npm install -g generator-az-terra-module`.

![Installer generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Pour vérifier que Yeoman est installé, entrez `yo --version` dans une fenêtre de terminal.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Créer un dossier vide pour contenir le module généré par Yeoman

Le modèle Yeoman génère des fichiers dans le **répertoire actif**. Pour cette raison, vous devez créer un répertoire.

>[!Note]
>Ce répertoire vide doit être placé sous $GOPATH/src. Vous trouverez [ici](https://github.com/golang/go/wiki/SettingGOPATH) les instructions nécessaires.

À partir d’une invite de commandes :

1. Accédez au répertoire parent qui doit contenir le nouveau répertoire vide que nous sommes sur le point de créer.
1. Entrez `mkdir <new-directory-name>`.

    >[!NOTE]
    >Remplacez <new-directory-name> par le nom de votre nouveau répertoire. Dans cet exemple, nous avons nommé le nouveau répertoire `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Accédez au nouveau répertoire en tapant `cd <new directory's name>`, puis en appuyant sur **Entrée**.

    ![Accéder à votre nouveau répertoire](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Pour vous assurer que ce répertoire est vide, entrez `ls`. Aucun fichier ne doit être répertorié dans le résultat de cette commande.

## <a name="create-a-base-module-template"></a>Créer un modèle de module de base

À partir d’une invite de commandes :

1. Entrez `yo az-terra-module`.

1. Suivez les instructions à l’écran pour fournir les informations suivantes :

    - *Nom du projet de module Terraform*

        ![Nom du projet](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >Dans cet exemple, nous avons entré `doc-sample-module`.

    - *Voulez-vous inclure le fichier d’image Docker ?*

        ![Inclure le fichier d’image Docker ?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Entrez `y`. Si vous sélectionnez **n**, le code de module généré ne prend en charge que l’exécution en mode natif.

3. Entrez `ls` pour afficher les fichiers créés.

    ![Répertorier les fichiers créés](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Passer en revue le code du module généré

1. Lancer Visual Studio Code

1. Dans la barre de menus, sélectionnez **Fichier > Ouvrir le dossier** et sélectionnez le dossier que vous avez créé.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Jetons un œil à certains des fichiers qui ont été créés par le générateur de module Yeoman.

>[!Note]
>Dans cet article nous allons utiliser les fichiers main.tf, variables.tf et outputs.tf créés par le générateur de module Yeoman. Toutefois, lors de la création de vos propres modules, vous pouvez modifier ces fichiers pour prendre en charge la fonctionnalité de votre module Terraform. Pour obtenir une explication plus approfondie sur ces fichiers et leur utilisation, consultez [Terratest dans les modules Terraform.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

### <a name="maintf"></a>main.tf

Définit un module appelé *random-shuffle*. L’entrée est une *string_list*. La sortie est le nombre de permutations.

### <a name="variablestf"></a>variables.tf

Définit les variables d’entrée et de sortie utilisées par le module.

### <a name="outputstf"></a>outputs.tf

Définit ce que le module génère. Ici, c’est la valeur retournée par **random_shuffle**, qui est un module Terraform intégré.

### <a name="rakefile"></a>Rakefile

Définit les étapes de génération. Ces étapes sont les suivantes :

- **build** : valide la mise en forme du fichier main.tf.
- **unit** : le squelette du module généré n’inclut pas le code pour un test unitaire. Si vous souhaitez spécifier un scénario de test unitaire, vous pouvez ajouter ce code ici.
- **e2e** : exécute un test du module de bout en bout.

### <a name="test"></a>test

- Les cas de test sont écrits en Go.
- Tous les codes de test sont des tests de bout en bout.
- Les tests de bout en bout essaient d’utiliser Terraform pour provisionner tous les éléments définis sous **fixture** et comparent les résultats dans le code **template_output.go** avec les valeurs attendues prédéfinies.
- **Gopkg.lock** et **Gopkg.toml** : définissent vos dépendances. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Tester votre nouveau module Terraform à l’aide d’un fichier Docker

>[!NOTE]
>Dans notre exemple, nous exécutons le module en tant que module local sans réellement toucher Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Confirmer que Docker est installé et en cours d’exécution

À partir d’une invite de commandes, entrez `docker version`.

![Version de Docker](media/terraform-vscode-module-generator/ymg-docker-version.png)

La sortie obtenue confirme que Docker est installé.

Pour confirmer que Docker est en cours d’exécution, entrez `docker info`.

![Informations Docker](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Configurer un conteneur Docker

1. À partir d’une invite de commandes, entrez

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Le message **Correctement généré** s’affiche.

    ![Correctement généré](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. À partir de l’invite de commandes, entrez `docker image ls`.

    Votre module nouvellement créé *terra-mod-example* est répertorié.

    ![Résultats du référentiel](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >Le nom du module, *terra-mod-example*, a été spécifié dans la commande que vous avez entrée à l’étape 1, ci-dessus.

1. Entrez `docker run -it terra-mod-example /bin/sh`.

    Vous exécutez maintenant Docker et pouvez répertorier le fichier en entrant `ls`.

    ![Répertorier le fichier Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Générer le module

1. Entrez `bundle install`.

    Attendez que le message **Bundle terminé** s’affiche, puis passez à l’étape suivante.

1. Entrez `rake build`.

    ![Build Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Exécuter le test de bout en bout

1. Entrez `rake e2e`.

1. Après quelques instants, le message **PASS** s’affiche.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Entrez `exit` pour terminer le test de bout en bout et quitter l’environnement Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Utiliser le générateur Yeoman pour créer et tester un module dans Cloud Shell

Dans la section précédente, vous avez appris à tester un module Terraform à l’aide d’un fichier Docker. Dans cette section, vous allez utiliser le générateur Yeoman pour créer et tester un module dans Cloud Shell.

L’utilisation de Cloud Shell à la place d’un fichier Docker simplifie considérablement le processus. Avec Cloud Shell :

- Il est inutile d’installer Node.js.
- Il est inutile d’installer Yeoman.
- Il est inutile d’installer Terraform.

Tous ces éléments sont préinstallés dans Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Démarrer une session Cloud Shell

1. Démarrez une session Azure Cloud Shell à l’aide du [portail Azure](https://portal.azure.com/), du site [shell.azure.com](https://shell.azure.com) ou de l’application [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. La page **Bienvenue dans Azure Cloud Shell** s’ouvre. Sélectionnez **Bash (Linux)**. (PowerShell n’est pas pris en charge.)

    ![Bienvenue dans Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Dans cet exemple, Bash (Linux) a été sélectionné.

1. Si vous n’avez pas déjà configuré un compte de stockage Azure, l’écran suivant s’affiche. Sélectionnez **Créer le stockage**.

    ![Vous n’avez aucun stockage monté](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell démarre dans l’interpréteur de commandes précédemment sélectionné, et affiche des informations concernant le lecteur cloud qu’il vient de créer pour vous.

    ![Votre service cloud a été créé.](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Préparer un dossier où stocker le module Terraform

1. À ce stade, Cloud Shell a déjà configuré GOPATH dans vos variables d’environnement. Pour voir le chemin, entrez `go env`.

1. Créez le dossier $GOPATH s’il n’existe pas déjà : Entrez `mkdir ~/go`.

1. Créez un dossier dans le dossier $GOPATH : Entrez `mkdir ~/go/src`. Ce dossier est utilisé pour stocker et organiser les différents dossiers de projet que vous pouvez créer, notamment le dossier <your-module-name> que nous allons créer à l’étape suivante.

1. Créez un dossier où stocker votre module Terraform : Entrez `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >Dans cet exemple, nous avons choisi `my-module-name` comme nom de dossier.

1. Accédez au dossier du module : Entrez `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Créer et tester le module Terraform

1. Entrez `yo az-terra-module` et suivez les instructions de l’Assistant.

    >[!NOTE]
    >Quand vous êtes invité à créer les fichiers Docker, vous pouvez entrer `N`.

1. Entrez `bundle install` pour installer les dépendances.

    Attendez que le message **Bundle terminé** s’affiche, puis passez à l’étape suivante.

1. Entrez `rake build` pour générer votre module.

    ![Build Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Entrez `rake e2e` pour exécuter le test de bout en bout.

1. Après quelques instants, le message **PASS** s’affiche.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Installez et utilisez l’extension Azure Terraform Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
