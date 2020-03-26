---
title: Tutoriel - Créer un modèle de base Terraform dans Azure avec Yeoman
description: Découvrez comment créer un modèle de base Terraform dans Azure à l’aide de Yeoman.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472143"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Tutoriel : Créer un modèle de base Terraform dans Azure à l’aide de Yeoman

Dans ce tutoriel, vous allez apprendre à utiliser la combinaison de [Terraform](/azure/terraform/) et de [Yeoman](https://yeoman.io/). Terraform est un outil de création d’infrastructure sur Azure. Yeoman facilite la création de modules Terraform.

Cet article vous montre comment effectuer les tâches suivantes :
> [!div class="checklist"]
> * Créer un modèle Terraform de base à l’aide du générateur de module Yeoman.
> * Tester le modèle Terraform à l’aide de deux méthodes différentes.
> * Exécuter le module Terraform à l’aide d’un fichier Docker.
> * Exécuter le module Terraform en mode natif dans Azure Cloud Shell.

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- **Visual Studio Code** : [téléchargez Visual Studio Code](https://code.visualstudio.com/download) pour votre plateforme.
- **Terraform** : installez [Terraform](terraform-install-configure.md) pour exécuter le module créé par Yeoman.
- **Docker** : [installez Docker](https://www.docker.com/get-started) pour exécuter le module créé par le générateur Yeoman.
- **Langage de programmation Go** : [installez Go](https://golang.org/), car les cas de test générés par Yeoman sont du code utilisant le langage Go.

>[!NOTE]
>La plupart des procédures de ce tutoriel impliquent l’interface de ligne de commande. Les étapes décrites s’appliquent à tous les systèmes d’exploitation et à tous les outils en ligne de commande. Pour les exemples, PowerShell a été choisi pour l’environnement local et Git Bash pour l’environnement Cloud Shell.

## <a name="prepare-your-environment"></a>Préparation de votre environnement

### <a name="install-nodejs"></a>Installer Node.js

Pour utiliser Terraform dans Cloud Shell, vous devez [installer Node.js](https://nodejs.org/en/download/) 6.0 +.

>[!NOTE]
>Pour vérifier que Node.js est installé, ouvrez une fenêtre de terminal et tapez `node --version`.

### <a name="install-yeoman"></a>Installer Yeoman

Exécutez la commande suivante :

```bash
npm install -g yo
```

![Installer Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Installer le modèle Yeoman pour le module Terraform

Exécutez la commande suivante :

```bash
npm install -g generator-az-terra-module
```

![Installer generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Pour vérifier que Yeoman est installé, exécutez la commande suivante :

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Créer un répertoire pour le module généré par Yeoman

Le modèle Yeoman génère des fichiers dans le répertoire actif. Pour cette raison, vous devez créer un répertoire.

Ce répertoire vide doit être placé sous $GOPATH/src. Pour plus d’informations sur ce chemin, consultez l’article [Définition de GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Accédez au répertoire parent à partir duquel créer un nouveau répertoire.

1. Exécutez la commande suivante en remplaçant l’espace réservé. Pour cet exemple, le nom de répertoire `GeneratorDocSample` est utilisé.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Accédez au nouveau répertoire :

    ```bash
    cd <new-directory-name>
    ```

    ![Accéder à votre nouveau répertoire](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Créer un modèle de module de base

1. Exécutez la commande suivante :

    ```bash
    yo az-terra-module
    ```

1. Suivez les instructions à l’écran pour fournir les informations suivantes :

    - **Nom du projet de module Terraform** : la valeur `doc-sample-module` est utilisée pour l’exemple.

        ![Nom du projet](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Voulez-vous inclure le fichier d’image Docker ?** : entrez `y`. Si vous sélectionnez `n`, le code de module généré ne prend en charge que l’exécution en mode natif.

        ![Inclure le fichier d’image Docker ?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Listez le contenu du répertoire pour afficher les fichiers créés :

    ```bash
    ls
    ```

    ![Répertorier les fichiers créés](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Passer en revue le code du module généré

1. Lancer Visual Studio Code

1. Dans la barre de menus, sélectionnez **Fichier > Ouvrir le dossier** et sélectionnez le dossier que vous avez créé.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Les fichiers suivants ont été créés par le générateur de module Yeoman. Pour plus d’informations sur ces fichiers et leur utilisation, consultez [Terratest dans les modules Terraform](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf` : définit un module appelé `random-shuffle`. L’entrée est un `string_list`. La sortie est le nombre de permutations.
- `variables.tf` : définit les variables d’entrée et de sortie utilisées par le module.
- `outputs.tf` : définit ce que le module génère. Ici, c’est la valeur retournée par `random_shuffle`, qui est un module Terraform intégré.
- `Rakefile` : définit les étapes de génération. Ces étapes sont les suivantes :
    - `build` : valide la mise en forme du fichier main.tf.
    - `unit` : le squelette du module généré n’inclut pas le code pour un test unitaire. Si vous souhaitez spécifier un scénario de test unitaire, vous pouvez ajouter ce code ici.
    - `e2e` : exécute un test de bout en bout du module.
- `test`
    - Les cas de test sont écrits en Go.
    - Tous les codes de test sont des tests de bout en bout.
    - Les tests de bout en bout tentent de provisionner tous les éléments définis sous `fixture`. Les résultats figurant dans le fichier `template_output.go` sont comparés aux valeurs attendues prédéfinies.
    - `Gopkg.lock` et `Gopkg.toml` : définit les dépendances. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Tester votre nouveau module Terraform à l’aide d’un fichier Docker

Cette section montre comment tester un module Terraform à l’aide d’un fichier Docker.

>[!NOTE]
>Cet exemple exécute le module localement, et non pas sur Azure.

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

    ![Message indiquant une génération réussie](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Dans l’invite de commandes, entrez `docker image ls` pour voir le module `terra-mod-example` que vous avez créé listé.

    ![Liste contenant le nouveau module](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Entrez `docker run -it terra-mod-example /bin/sh`. Une fois la commande `docker run` exécutée, vous êtes dans l’environnement Docker. À ce stade, vous pouvez découvrir le fichier à l’aide de la commande `ls`.

    ![Liste de fichiers dans Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Générer le module

1. Exécutez la commande suivante :

    ```bash
    bundle install
    ```

1. Exécutez la commande suivante :

    ```bash
    rake build
    ```

    ![Build Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Exécuter le test de bout en bout

1. Exécutez la commande suivante :

    ```bash
    rake e2e
    ```

1. Après quelques instants, le message **PASS** s’affiche.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Entrez `exit` pour terminer le test et quitter l’environnement Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Utiliser le générateur Yeoman pour créer et tester un module dans Cloud Shell

Dans cette section, le générateur Yeoman est utilisé pour créer et tester un module dans Cloud Shell. L’utilisation de Cloud Shell à la place d’un fichier Docker simplifie considérablement le processus. Si vous utilisez Cloud Shell, les produits suivants sont tous préinstallés :

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Démarrer une session Cloud Shell

1. Démarrez une session Azure Cloud Shell à l’aide du [portail Azure](https://portal.azure.com/), du site [shell.azure.com](https://shell.azure.com) ou de l’application [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. La page **Bienvenue dans Azure Cloud Shell** s’ouvre. Sélectionnez **Bash (Linux)** .

    ![Bienvenue dans Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Si vous n’avez pas déjà configuré un compte de stockage Azure, l’écran suivant s’affiche. Sélectionnez **Créer le stockage**.

    ![Vous n’avez aucun stockage monté](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell démarre dans l’interpréteur de commandes précédemment sélectionné, et affiche des informations concernant le lecteur cloud qu’il vient de créer pour vous.

    ![Votre service cloud a été créé.](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Préparer un répertoire où stocker le module Terraform

1. À ce stade, Cloud Shell a déjà configuré GOPATH dans vos variables d’environnement. Pour voir le chemin, entrez `go env`.

1. Créez le répertoire $GOPATH, s’il n’existe pas déjà : Entrez `mkdir ~/go`.

1. Créez un répertoire dans le répertoire $GOPATH. Ce répertoire est utilisé pour contenir les différents répertoires de projet créés dans cet exemple. 

    ```bash
    mkdir ~/go/src
    ```

1. Créez un répertoire pour stocker votre module Terraform, en remplaçant l’espace réservé. Pour cet exemple, le nom de répertoire `my-module-name` est utilisé.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Accédez au répertoire de votre module : 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Créer et tester le module Terraform

1. Exécutez la commande suivante et suivez les instructions. Quand vous êtes invité à créer les fichiers Docker, entrez `N`.

    ```bash
    yo az-terra-module
    ```

1. Exécutez la commande suivante pour installer les dépendances :

    ```bash
    bundle install
    ```

1. Exécutez la commande suivante pour générer le module :

    ```bash
    rake build
    ```

    ![Build Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Exécutez la commande suivante pour exécuter le texte :

    ```bash
    rake e2e
    ```

    ![Résultats de passe de test](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Installer et utiliser l’extension Azure Terraform Visual Studio Code](/azure/terraform/terraform-vscode-extension).