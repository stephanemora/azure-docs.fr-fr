---
title: Comment déployer le module OPC Vault à partir de zéro - Azure | Microsoft Docs
description: Comment déployer OPC Vault à partir de zéro.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 73588d8c84525cf70de9fac312b0a485055b0a2c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605704"
---
# <a name="deploy-opc-vault-from-scratch"></a>Déployer OPC Vault à partir de zéro

OPC Vault est un microservice qui peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications serveur et clientes OPC UA dans le cloud. Cet article vous montre comment déployer OPC Vault à partir de zéro.

## <a name="configuration-and-environment-variables"></a>Configuration et variables d’environnement

La configuration du service est stockée à l’aide des adaptateurs de configuration ASP.NET Core, dans appsettings.ini. Le format INI permet de stocker des valeurs dans un format lisible, avec des commentaires.
L’application prend également en charge l’insertion de variables d’environnement, telles que des informations d’identification et des détails de mise en réseau. (Cette section s’appelle normalement Configuration TODO et variables d’environnement).

Le fichier de configuration dans le référentiel fait référence à certaines variables d’environnement qui doivent être créées au moins une fois. Selon votre système d’exploitation et l’IDE, il existe plusieurs façons de gérer les variables d’environnement :

- Pour les utilisateurs Windows, le script env-vars-setup.cmd ne doit être préparé et exécuté qu’une seule fois. Lorsqu’il est exécuté, les paramètres seront conservés lors des démarrages et sessions de terminal.

- Pour les environnements Linux et OSX, le script env-vars-setup doit être exécuté chaque fois qu’une nouvelle console est ouverte. En fonction du système d’exploitation et du terminal, il existe des méthodes pour rendre persistantes les valeurs globalement. Si vous souhaitez plus d’informations, ces pages peuvent être utiles :

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio : Des variables d’environnement peuvent également être définies dans Visual Studio, sous Propriétés du projet, dans le volet de gauche, sélectionnez « Propriétés de configuration » et « Environnement » pour accéder à une section dans laquelle vous pouvez ajouter plusieurs variables.

- IntelliJ Rider : Des variables d’environnement peuvent être définies dans chaque configuration d’exécution, comme pour IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Exécuter et déboguer à l’aide de Visual Studio

Visual Studio vous permet d’ouvrir rapidement l’application sans utiliser d’invite de commandes et sans configurer quoi que ce soit en dehors de l’IDE.

Pour exécuter et déboguer l’application à l’aide de Visual Studio :

1. Ouvrez la solution à l’aide du fichier `iot-opc-gds-service.sln`.

1. Quand la solution est chargée, cliquez avec le bouton droit sur le projet `WebService`, sélectionnez et accédez à la section `Debug`.

1. Dans la même section, définissez les variables d’environnement requises.

1. Appuyez sur **F5**, ou cliquez sur l’icône **Exécuter**. Visual Studio doit ouvrir votre navigateur en affichant l’état du service au format JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Exécuter et déboguer avec IntelliJ Rider

1. Ouvrez la solution à l’aide du fichier `iot-opc-gds-service.sln`.

1. Lorsque la solution est chargée, accédez à `Run > Edit Configurations` et créez une nouvelle configuration `.NET Project`.

1. Dans la configuration, sélectionnez le projet WebService.

1. Enregistrez les paramètres et exécutez la configuration qui est créée à partir de la barre d’outils de l’IDE.

1. Vous devez voir les messages de démarrage du service dans la fenêtre d’exécution IntelliJ, ainsi que des informations comme l’URL d’exécution du service web et les journaux du service.

## <a name="build-and-run-from-the-command-line"></a>Générer et exécuter depuis la ligne de commande

Le dossier des scripts contient des scripts pour les tâches fréquentes :

- `build`: compilez tous les projets et exécutez les tests.

- `compile`: compilez tous les projets.

- `run`: compilez les projets et exécutez le service, qui exige des privilèges élevés dans Windows pour exécuter le service web.

La vérification des scripts pour la configuration des variables d’environnement. Vous pouvez définir les variables d’environnement globalement dans votre système d’exploitation, ou utiliser le script « env-var-setup » dans le dossier de scripts.

### <a name="sandbox"></a>Bac à sable

Les scripts supposent que vous avez configuré votre environnement de développement avec .NET Core et Docker. Vous pouvez éviter d’installer .NET Core et installer Docker uniquement et utiliser le paramètre de ligne de commande `--in-sandbox` (ou la forme abrégée `-s`), par exemple :

- `build --in-sandbox`: exécute la tâche de génération à l’intérieur d’un conteneur Docker (forme abrégée `build -s`).

- `compile --in-sandbox`: exécute la tâche de compilation à l’intérieur d’un conteneur Docker (forme abrégée `compile -s`).

- `run --in-sandbox`: exécute le service à l’intérieur d’un conteneur Docker (forme abrégée `run -s`).

Les images de Docker utilisées pour le bac à sable sont hébergées sur Docker Hub [ici](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Empaqueter l’application dans une image Docker

Le dossier `scripts` comprend un sous-dossier docker avec les fichiers nécessaires pour empaqueter le service dans une image Docker :

- `Dockerfile`: spécifications d’images docker.
- `build`: créer un conteneur Docker et stocker l’image dans le Registre local.
- `run`: exécuter le conteneur Docker à partir de l’image stockée dans le Registre local.
- `content`: un dossier contenant les fichiers copiés dans l’image, y compris le script de point d’entrée.

## <a name="azure-iot-hub-setup"></a>Configuration d’Azure IoT Hub

Pour utiliser le microservice, configurez votre Azure IoT Hub pour les tests d’intégration et de développement.

Le projet inclut des scripts Bash pour vous aider avec cette configuration :

- Créer une nouvelle instance IoT Hub : `./scripts/iothub/create-hub.sh`

- Répertorier les hubs existants : `./scripts/iothub/list-hubs.sh`

- Afficher les détails IoT Hub (par exemple, les clés) : `./scripts/iothub/show-hub.sh`

Et, si vous avez plusieurs abonnements Azure :

- Afficher la liste des abonnements : `./scripts/iothub/list-subscriptions.sh`

- Modifier l’abonnement actuel : `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Configuration du développement

### <a name="net-setup"></a>Configuration .NET

Le flux de travail du projet est géré via [.NET Core](https://dotnet.github.io) 1.x, que vous devez installer dans votre environnement, afin de pouvoir exécuter tous les scripts et vous assurer que votre IDE fonctionne comme prévu.

Nous fournissons également une [version Java](https://github.com/Azure/iot-opc-gds-service-dotnet) de ce projet et d’autres composants Azure IoT PCS.

### <a name="ide"></a>IDE

Voici quelques-uns des IDE que vous pouvez utiliser pour travailler sur Azure IoT PCS :

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Configuration Git

Le projet inclut un hook Git, afin d’automatiser certaines vérifications avant d’accepter une modification du code. Vous pouvez exécuter les tests manuellement, ou laisser la plateforme CI pour exécuter les tests. Nous utilisons le hook Git suivant pour exécuter automatiquement tous les tests avant d’envoyer des modifications de code à GitHub et d’accélérer le flux de travail de développement.

Si, à tout moment, vous souhaitez supprimer le raccordement, supprimez simplement le fichier installé sous `.git/hooks`. Vous pouvez également ignorer le hook avant validation à l’aide de l’option `--no-verify`.

#### <a name="pre-commit-hook-with-sandbox"></a>Hook avant validation avec le bac à sable

Pour configurer les hooks inclus, ouvrez une console Windows/Linux/Mac OS et exécutez :

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Avec cette configuration, lors de la vérification des fichiers, Git vérifie que l’application réussit tous les tests, exécute le build et les tests à l’intérieur d’un conteneur Docker qui est configuré avec toutes les exigences de développement.

#### <a name="pre-commit-hook-without-sandbox"></a>Hook avant validation sans le bac à sable

> [!NOTE] 
> Le hook sans bac à sable exige [.NET Core](https://dotnet.github.io) dans le CHEMIN D’ACCÈS système.

Pour configurer les hooks inclus, ouvrez une console Windows/Linux/Mac OS et exécutez :

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Avec cette configuration, lors de la vérification des fichiers, Git vérifie que l’application réussit tous les tests, exécute le build et les tests dans votre station de travail à l’aide des outils installés dans votre SE.

Conseils sur le style de code de projet :

- Dans certains cas, la longueur des lignes est limitée à 80 caractères maximum pour simplifier les révisions du code et les éditeurs de lignes de commande.

- Mise en retrait des blocs de code avec quatre espaces. Le caractère tabulation doit être évité.

- Les fichiers texte utilisent le format de ligne de fin Unix (LF).

- L’Injection de dépendances est gérée avec [Autofac](https://autofac.org).

- Les champs d’API de service Web sont de type CamelCase à l’exception des métadonnées.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment déployer OPC Vault à partir de zéro, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Deploy OPC Twin from scratch](howto-opc-twin-deploy-modules.md) (Déployer OPC Twin à partir de zéro)