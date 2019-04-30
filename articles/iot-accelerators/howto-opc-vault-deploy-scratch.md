---
title: Comment déployer le module OPC coffre à partir de zéro - Azure | Microsoft Docs
description: Comment déployer OPC coffre à partir de zéro.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450327"
---
# <a name="deploy-opc-vault-from-scratch"></a>Déployer OPC Vault à partir de zéro

OPC Vault est un microservice qui peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications serveur et clientes OPC UA dans le cloud. Cet article vous montre comment déployer OPC coffre à partir de zéro.

## <a name="configuration-and-environment-variables"></a>Variables d’environnement et de configuration

La configuration du service est stockée à l’aide des adaptateurs de configuration ASP.NET Core, dans appsettings.ini. Le format INI permet de stocker des valeurs dans un format lisible, avec des commentaires.
L’application prend également en charge l’insertion variables d’environnement, telles que les informations d’identification et les détails de mise en réseau. (Cette section est intitulée à l’origine de variables d’environnement et de Configuration de tâches).

Le fichier de configuration dans le référentiel fait référence à certaines variables d’environnement qui doivent créées au moins une fois. Selon votre système d’exploitation et l’IDE, il existe plusieurs façons de gérer les variables d’environnement :

- Pour les utilisateurs Windows, le script env-var-setup.cmd doit être préparée et exécutée qu’une seule fois. Lors de l’exécution, les paramètres persistera entre les redémarrages et les sessions terminal.

- Pour les environnements Linux et OSX, le script env-var-le programme d’installation doit être exécutée chaque fois qu’une nouvelle console est ouvert. Selon le système d’exploitation et le Terminal Server, des façons pour rendre persistantes les valeurs dans le monde entier, pour plus d’informations que visent à ces pages :

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio : Variables d’environnement peut également indiquer à partir de Visual Studio, sous propriétés du projet, dans le volet gauche, sélectionnez « Propriétés de Configuration » et « Environnement » pour accéder à une section dans laquelle vous pouvez ajouter plusieurs variables.

- IntelliJ Rider : Variables d’environnement peuvent être définies dans chaque Configuration de s’exécuter, de même pour IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Exécuter et déboguer avec Visual Studio

Visual Studio vous permet d’ouvrir rapidement l’application sans ouvrir l’invite de commandes, sans configurer quoi que ce soit en dehors de l’IDE.

Étapes à l’aide de Visual Studio 2017 :

1. Ouvrez la solution utilisant le `iot-opc-gds-service.sln` fichier.

1. Quand la solution est chargée, avec le bouton droit sur le `WebService` de projet, sélectionnez et accédez à la `Debug` section.

1. Dans la même section, définissez les variables d’environnement requises.

1. Appuyez sur **F5**, ou le **exécuter** icône. Visual Studio doit ouvrir votre navigateur en affichant l’état du service au format JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Exécuter et déboguer avec IntelliJ Rider

1. Ouvrez la solution utilisant le `iot-opc-gds-service.sln` fichier.

1. Lorsque la solution est chargée, accédez à `Run > Edit Configurations` et créez un nouveau `.NET Project` configuration.

1. Dans la configuration, sélectionnez le projet de service Web.

1. Enregistrer les paramètres et exécuter la configuration qui est créée à partir de la barre d’outils de l’IDE.

1. Vous devez voir le service amorcer des messages dans la fenêtre IntelliJ exécuter avec les détails tels que l’URL où le service web est en cours d’exécution ainsi que le service de journaux.

## <a name="build-and-run-from-the-command-line"></a>Générer et exécuter à partir de la ligne de commande

Le dossier scripts contient des scripts pour les tâches fréquentes :

- `build`: Compilez tous les projets et exécuter les tests.

- `compile`: Compilez tous les projets.

- `run`: Compiler les projets et exécuter le service, qui invite à privilèges élevés dans Windows pour exécuter le service web.

Les scripts de vérification pour la configuration de variables d’environnement. Vous pouvez définir les variables d’environnement globalement dans votre système d’exploitation, ou utiliser le script « env-var-setup » dans le dossier scripts.

### <a name="sandbox"></a>Bac à sable

Les scripts supposent que vous configuré votre environnement de développement avec .NET Core et Docker. Vous pouvez éviter d’installer .NET Core et installer Docker uniquement et utilisez le paramètre de ligne de commande `--in-sandbox` (ou la forme abrégée `-s`), par exemple :

- `build --in-sandbox`: Exécute la tâche de génération à l’intérieur d’un conteneur Docker (forme abrégée `build -s`).

- `compile --in-sandbox`: Exécute la tâche de compilation à l’intérieur d’un conteneur Docker (forme abrégée `compile -s`).

- `run --in-sandbox`: Démarre le service à l’intérieur d’un conteneur Docker (forme abrégée `run -s`).

Les images de Docker utilisées pour le bac à sable sont hébergés sur le Hub Docker [ici](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Empaqueter l’application sur une image Docker

Le `scripts` dossier comprend un sous-dossier de docker avec les fichiers nécessaires pour empaqueter le service dans une image Docker :

- `Dockerfile`: Spécifications d’images docker.
- `build`: Créer un conteneur Docker et stockez l’image dans le Registre local.
- `run`: Exécutez le conteneur Docker à partir de l’image stockée dans le Registre local.
- `content`: Un dossier contenant les fichiers copiés dans l’image, y compris le script de point d’entrée.

## <a name="azure-iot-hub-setup"></a>Programme d’installation de Azure IoT Hub

Pour pouvoir utiliser le microservice, configuré votre Azure IoT Hub pour les tests d’intégration et de développement.

Le projet inclut des scripts Bash pour vous aider avec cette configuration :

- Créer un IoT Hub : `./scripts/iothub/create-hub.sh`

- Répertorier les hubs existants : `./scripts/iothub/list-hubs.sh`

- Afficher les détails (par exemple, les clés) IoT Hub : `./scripts/iothub/show-hub.sh`

Et, au cas où vous avez plusieurs abonnements Azure :

- Afficher la liste des abonnements : `./scripts/iothub/list-subscriptions.sh`

- Modifier l’abonnement actuel : `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Programme d’installation de développement

### <a name="net-setup"></a>Programme d’installation de .NET

Le flux de travail de projet est géré par le biais de [.NET Core](https://dotnet.github.io) 1.x, que vous devez installer dans votre environnement, afin que vous pouvez exécuter tous les scripts et vous assurer que votre IDE fonctionne comme prévu.

Nous fournissons également un [version Java](https://github.com/Azure/iot-opc-gds-service-dotnet) de ce projet et d’autres composants Azure IoT PC.

### <a name="ide"></a>IDE

Voici quelques-unes des IDE que vous pouvez utiliser pour travailler sur Azure IoT PC :

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio pour Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Programme d’installation de GIT

Le projet inclut un crochet Git, afin d’automatiser certaines vérifications avant d’accepter une modification du code. Vous pouvez exécuter les tests manuellement, ou laisser la plateforme CI pour exécuter les tests. Nous utilisons le crochet Git suivant pour exécuter tous les tests avant d’envoyer les modifications de code à GitHub et à accélérer le flux de travail de développement automatiquement.

Si à tout moment vous souhaitez supprimer le raccordement, supprimez simplement le fichier installé sous `.git/hooks`. Vous pouvez également ignorer le raccordement de validation préalable à l’aide de la `--no-verify` option.

#### <a name="pre-commit-hook-with-sandbox"></a>Raccordement de validation préliminaire avec le bac à sable

Pour configurer les raccordements inclus, ouvrez une console Windows/Linux/Mac OS et exécutez :

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Avec cette configuration, lors de l’archivage des fichiers, Git vérifie que l’application réussit tous les tests, exécuter la build et les tests à l’intérieur d’un conteneur Docker qui est configuré avec toutes les exigences de développement.

#### <a name="pre-commit-hook-without-sandbox"></a>Raccordement avant validation sans bac à sable

> [!NOTE] 
> Nécessite le raccordement sans bac à sable [.NET Core](https://dotnet.github.io) dans la variable système PATH.

Pour configurer les raccordements inclus, ouvrez une console Windows/Linux/Mac OS et exécutez :

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Avec cette configuration, lors de l’archivage des fichiers, Git vérifie que l’application réussit tous les tests, exécuter la build et les tests dans votre station de travail en utilisant les outils installés dans votre système d’exploitation.

Obtenir des conseils sur le style de code de projet :

- Dans certains cas, la longueur des lignes est limité à 80 caractères maximum pour aider à la révision du code et les éditeurs de ligne de commande.

- Mise en retrait des blocs de code avec quatre espaces. Le caractère d’onglet doit être évité.

- Fichiers texte utilisent fin Unix du format de ligne (LF).

- L’Injection de dépendances est gérée avec [Autofac](https://autofac.org).

- Les champs d’API de service Web sont CamelCased à l’exception des métadonnées.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à déployer OPC coffre à partir de zéro, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Déployer la représentation d’OPC à partir de zéro](howto-opc-twin-deploy-modules.md)