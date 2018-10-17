---
title: Azure Terraform ou générateur de module de code
description: Découvrez comment utiliser Yeoman pour créer un modèle de base Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, machine virtuelle, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396251"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>Créer un modèle de base Terraform à l’aide de Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) permet de créer facilement des infrastructure sur Azure. [Yeoman](http://yeoman.io/) simplifie considérablement le travail du développeur de module pour la création de modules Terraform tout en offrant un excellent framework de *meilleures pratiques*.

Dans cet article, vous allez apprendre à utiliser le générateur de module Yeoman pour créer un modèle Terraform de base.

## <a name="prerequisites"></a>Prérequis

- Un ordinateur exécutant Windows 10, Linux ou macOS 10.10+
- **Abonnement Azure** : si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
- **Visual Studio Code** : nous allons utiliser [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) pour examiner les fichiers créés par le générateur Yeoman. Toutefois, vous pouvez utiliser n’importe quel éditeur de code de votre choix.
- **Terraform** : [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) doit être installé pour exécuter le module créé par Yeoman.
- **Docker** : nous allons utiliser [Docker](https://www.docker.com/get-started) pour exécuter le module créé par le générateur Yeoman. (Si vous préférez, vous pouvez utiliser Ruby à la place de Docker pour exécuter l’exemple de module.)
- **Langage de programmation Go** : [Go](https://golang.org/) doit être installé, car les cas de test générés par Yeoman sont écrits en Go.

>[!NOTE]
>La plupart des procédures de ce tutoriel impliquent des entrées de ligne de commande. Les étapes décrites ici s’appliquent à tous les systèmes d’exploitation et tous les outils de ligne de commande. Dans nos exemples, nous avons choisi d’utiliser PowerShell. Toutefois, vous pouvez utiliser n’importe quelle alternative, comme Git Bash, des invites de commandes Windows ou des commandes de ligne de commande Linux ou macOS.

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
        >Entrez `y`. Si vous sélectionnez **n**, le code de module généré ne prendra en charge que l’exécution en mode natif.

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

- **build** : valide la mise en forme du fichier main.tf.
- **unit** : le squelette du module généré n’inclut pas le code pour un test unitaire. Si vous souhaitez spécifier un scénario de test unitaire, vous pouvez ajouter ce code ici.
- **e2e** : exécute un test du module de bout en bout.

### <a name="test"></a>test

- Les cas de test sont écrits en Go.
- Tous les codes de test sont des tests de bout en bout.
- Les tests de bout en bout essaient d’utiliser Terraform pour provisionner tous les éléments définis sous **fixture** et comparent les résultats dans le code **template_output.go** avec les valeurs attendues prédéfinies.
- **Gopkg.lock** et **Gopkg.toml** : définir vos dépendances. 

## <a name="test-the-module-using-docker"></a>Tester le module à l’aide de Docker

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

1. Entrez `bundle install`.

    Attendez que le message **Bundle terminé** s’affiche, puis passez à l’étape suivante.

1. Entrez `rake build`.

    ![Build Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>Exécuter le test de bout en bout

1. Entrez `rake e2e`.

1. Après quelques instants, le message **PASS** s’affiche.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Entrez `exit` pour exécuter le test de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Installez et utilisez l’extension Azure Terraform Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
