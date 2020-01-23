---
title: Utiliser l’émulateur azurite pour le développement de stockage Azure local
description: L’émulateur open source Azurite (préversion) fournit un environnement local gratuit, qui vous permet de tester vos applications du Stockage Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 5e1fce0852a4e820d7ee0af626ce3fddf6773750
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029928"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>Utiliser l’émulateur Azurite à des fins de développement et de test pour le Stockage Azure (préversion)

L’émulateur open source Azurite version 3.2 (préversion) fournit un environnement local gratuit, qui vous permet de tester vos applications du Stockage de file d'attente et Blob Azure. Une fois que vous êtes satisfait de la manière dont votre application fonctionne localement, passez à l’utilisation d’un compte de stockage Azure dans le cloud. L’émulateur offre une prise en charge multiplateforme, qui inclut Windows, Linux et macOS. Azurite v3 prend en charge les API implémentées par le service Blob Azure.

Azurite est la future plateforme d’émulateur de stockage. Azurite remplace l’[Émulateur de stockage Azure](storage-use-emulator.md). Azurite continuera d’être mis à jour pour prendre en charge les dernières versions des API de stockage Azure.

Il existe plusieurs manières différentes d’installer et d’exécuter Azurite sur votre système local :

  1. [Installer et exécuter l’extension Visual Studio Code Azurite](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [Installer et exécuter Azurite à l’aide de NPM](#install-and-run-azurite-by-using-npm)
  1. [Installer et exécuter l’image Docker Azurite](#install-and-run-the-azurite-docker-image)
  1. [Cloner, générer et exécuter Azurite à partir du dépôt GitHub](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>Installer et exécuter l’extension Visual Studio Code Azurite

Dans Visual Studio Code, sélectionnez le volet **EXTENSIONS**, puis recherchez *Azurite* dans **EXTENSIONS : PLACE DE MARCHÉ**.

![Place de marché des extensions Visual Studio Code](media/storage-use-azurite/azurite-vs-code-extension.png)

Vous pouvez également accéder au [marché des extensions VS Code](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite) dans votre navigateur. Sélectionnez le bouton **Installer** pour ouvrir Visual Studio Code et accéder directement à la page de l’extension Azurite.

Vous pouvez rapidement démarrer ou fermer Azurite en cliquant dans la barre d’état de VS Code sur **Service Blob Azurite** ou **Service de file d'attente Azurite**, ou en émettant les commandes suivantes dans la palette de commandes de VS Code. Pour ouvrir la palette de commandes, appuyez sur **F1** dans VS Code.

L’extension prend en charge les commandes Visual Studio Code suivantes :

   * **Azurite : Démarrer** - Démarrer tous les services Azurite
   * **Azurite : Fermer** - Fermer tous les services Azurite
   * **Azurite : Nettoyer** - Réinitialiser toutes les données de persistance des services Azurite
   * **Azurite : Démarrer le service Blob** - Démarrer le service Blob
   * **Azurite : Fermer le service blob** - Fermer le service BLOB
   * **Azurite : Nettoyer le service blob** - Nettoyer le service blob
   * **Azurite : Démarrer le service de file d’attente** - Démarrer le service de file d’attente
   * **Azurite : Fermer le service de file d’attente** - Fermer le service de file d’attente
   * **Azurite : Nettoyer le service de file d'attente** - Nettoyer le service de file d'attente

Pour configurer Azurite dans Visual Studio Code, sélectionnez le volet Extensions. Sélectionnez l'icône **Gérer** (engrenage) pour **Azurite**. Sélectionnez **Configure Extension Settings** (Configurer les paramètres de l’extension).

![Configuration des paramètres de l’extension Azurite](media/storage-use-azurite/azurite-configure-extension-settings.png)

Les paramètres suivants sont pris en charge :

   * **Azurite : Blob Host** (Hôte Blob) - Point de terminaison d’écoute du service Blob. Le paramètre par défaut est 127.0.0.1.
   * **Azurite : Blob Port** (Port Blob) - Port d’écoute du service Blob. Le port par défaut est 10000.
   * **Azurite : Debug** (Débogage) - Sortie du journal de débogage sur le canal Azurite. La valeur par défaut est **false**.
   * **Azurite : Location** (Emplacement) - Chemin de l’emplacement de l’espace de travail. Par défaut, il s’agit du dossier de travail de Visual Studio Code.
   * **Azurite : Hôte de file d'attente** - Point de terminaison d’écoute du service de file d'attente. Le paramètre par défaut est 127.0.0.1.
   * **Azurite : Port de la file d’attente** - Le port d’écoute du service de file d’attente. Le port par défaut est 10001.
   * **Azurite : Silent** (Sans assistance) - Ce mode entraîne la désactivation du journal des accès. La valeur par défaut est **false**.

## <a name="install-and-run-azurite-by-using-npm"></a>Installer et exécuter Azurite à l’aide de NPM

Cette méthode d’installation nécessite l’installation préalable de [Node.js version 8.0 ou ultérieure](https://nodejs.org). **npm** est l’outil de gestion des packages inclus dans chaque installation Node.js. Après avoir installé Node.js, exécutez la commande **npm** suivante pour installer Azurite.

```console
npm install -g azurite
```

Après avoir installé Azurite, consultez [Exécuter Azurite à partir d’une ligne de commande](#run-azurite-from-a-command-line).

## <a name="install-and-run-the-azurite-docker-image"></a>Installer et exécuter l’image Docker Azurite

Utilisez [DockerHub](https://hub.docker.com/) pour effectuer le tirage (pull) de l’[image Azurite la plus récente](https://hub.docker.com/_/microsoft-azure-storage-azurite) à l’aide de la commande suivante :

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Exécuter l’image Docker Azurite** :

La commande suivante exécute l’image Docker Azurite. Le paramètre `-p 10000:10000` redirige les requêtes du port 10000 de la machine hôte vers l’instance de Docker.

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**Spécifier l’emplacement de l’espace de travail** :

Dans l’exemple suivant, le paramètre `-v c:/azurite:/data` spécifie *c:/azurite* en tant qu’emplacement des données persistantes d’Azurite. Le répertoire *c:/azurite* doit être créé avant l’exécution de la commande Docker.

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Exécuter uniquement le service blob**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**Définir tous les paramètres Azurite** :

Cet exemple montre comment définir tous les paramètres de ligne de commande. Tous les paramètres ci-dessous doivent être placés sur une seule ligne de commande.

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

Pour plus d’informations sur la configuration d’Azurite au démarrage, consultez [Options de ligne de commande](#command-line-options).

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>Cloner, générer et exécuter Azurite à partir du dépôt GitHub

Cette méthode d’installation nécessite l’installation préalable de [Git](https://git-scm.com/). Clonez le [dépôt GitHub](https://github.com/azure/azurite) du projet Azurite à l’aide de la commande de console suivante.

```console
git clone https://github.com/Azure/Azurite.git
```

Après avoir cloné le code source, exécutez les commandes suivantes à partir de la racine du dépôt cloné pour générer et installer Azurite.

```console
npm install
npm run build
npm install -g
```

Après avoir installé et généré Azurite, consultez [Exécuter Azurite à partir d’une ligne de commande](#run-azurite-from-a-command-line).

## <a name="run-azurite-from-a-command-line"></a>Exécuter Azurite à partir d’une ligne de commande

> [!NOTE]
> Vous ne pouvez pas exécuter Azurite à partir de la ligne de commande si vous avez installé uniquement l’extension Visual Studio Code. À la place, utilisez la palette de commandes VS Code. Pour plus d’informations, consultez [Installer et exécuter l’extension Visual Studio Code Azurite](#install-and-run-the-azurite-visual-studio-code-extension).

Pour commencer immédiatement à l’aide de la ligne de commande, créez un répertoire appelé **c:\azurite**, puis lancez Azurite en émettant la commande suivante :

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

Cette commande indique à Azurite de stocker toutes les données dans un répertoire particulier, **c:\azurite**. Si l’option **--location** est omise, le répertoire de travail actif est utilisé.

## <a name="command-line-options"></a>Options de ligne de commande

Cette section détaille les commutateurs de ligne de commande disponibles quand vous lancez Azurite. Tous les commutateurs de ligne de commande sont facultatifs.

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** est un raccourci pour **--debug**, le commutateur **-l** est un raccourci pour **--location**, **-s** est un raccourci pour **--silent**, et **-h** est un raccourci pour **--help**.

### <a name="blob-listening-host"></a>Hôte d’écoute Blob

**Facultatif** Par défaut, Azurite écoute le serveur local à l’adresse 127.0.0.1. Utilisez le commutateur **--blobHost** pour définir l’adresse en fonction de vos exigences.

Accepter les requêtes sur la machine locale uniquement :

```console
azurite --blobHost 127.0.0.1
```

Autoriser les requêtes distantes :

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> L’autorisation des requêtes distantes peut rendre votre système vulnérable aux attaques externes.

### <a name="blob-listening-port-configuration"></a>Configuration du port d’écoute Blob

**Facultatif** Par défaut, Azurite écoute le service Blob sur le port 10000. Utilisez le commutateur **--blobPort** pour spécifier le port d’écoute nécessaire.

> [!NOTE]
> Après avoir utilisé un port personnalisé, vous devez mettre à jour la chaîne de connexion ou la configuration correspondante dans vos outils du service Stockage Azure ou vos kits SDK.

Personnaliser le port d’écoute du service Blob :

```console
azurite --blobPort 8888
```

Laisser le système sélectionner automatiquement un port disponible :

```console
azurite --blobPort 0
```

Le port utilisé s’affiche au démarrage d’Azurite.

### <a name="queue-listening-host"></a>Hôte d’écoute de file d'attente

**Facultatif** Par défaut, Azurite écoute le serveur local à l’adresse 127.0.0.1. Utilisez le commutateur **--queueHost** pour définir l’adresse en fonction de vos exigences.

Accepter les requêtes sur la machine locale uniquement :

```console
azurite --queueHost 127.0.0.1
```

Autoriser les requêtes distantes :

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> L’autorisation des requêtes distantes peut rendre votre système vulnérable aux attaques externes.

### <a name="queue-listening-port-configuration"></a>Configuration du port d’écoute de file d'attente

**Facultatif** Par défaut, Azurite écoute le service de file d’attente sur le port 10001. Utilisez le commutateur **--queuePort** pour spécifier le port d’écoute nécessaire.

> [!NOTE]
> Après avoir utilisé un port personnalisé, vous devez mettre à jour la chaîne de connexion ou la configuration correspondante dans vos outils du service Stockage Azure ou vos kits SDK.

Personnaliser le port d’écoute du service de file d'attente :

```console
azurite --queuePort 8888
```

Laisser le système sélectionner automatiquement un port disponible :

```console
azurite --queuePort 0
```

Le port utilisé s’affiche au démarrage d’Azurite.

### <a name="workspace-path"></a>Chemin de l’espace de travail

**Facultatif** Azurite stocke les données sur le disque local durant l’exécution. Utilisez le commutateur **--location** pour spécifier un chemin en tant qu’emplacement de l’espace de travail. Par défaut, le répertoire de travail du processus actuel est utilisé.

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>Journal d’accès

**Facultatif** Le journal des accès s’affiche par défaut dans la fenêtre de console. Désactivez l’affichage du journal des accès à l’aide du commutateur **--silent**.

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>Journal de débogage

**Facultatif** Le journal de débogage contient des informations détaillées sur chaque arborescence des appels de procédure relative aux requêtes et aux exceptions. Activez le journal de débogage en fournissant un chemin de fichier local valide au commutateur **--debug**.

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

### <a name="loose-mode"></a>Mode relâché

**Facultatif** Par défaut, Azurite applique le mode strict pour bloquer les en-têtes et les paramètres de requête non pris en charge. Désactivez le mode strict en utilisant le commutateur **--loose**.

```console
azurite --loose
```

Notez que le raccourci du commutateur est un « L » majuscule :

```console
azurite -L
```

## <a name="authorization-for-tools-and-sdks"></a>Autorisation pour les outils et les kits SDK

Connectez-vous à Azurite à partir des kits SDK ou des outils du service Stockage Azure, par exemple l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/), à l’aide d’une stratégie d’authentification. L’authentification est obligatoire. Azurite prend en charge l’autorisation basée sur une clé partagée et les SAP (signatures d’accès partagé). Azurite prend également en charge l’accès anonyme aux conteneurs publics.

### <a name="well-known-storage-account-and-key"></a>Clé et compte de stockage reconnus

Vous pouvez utiliser la clé et le nom de compte suivants avec Azurite. Ce compte et cette clé reconnus sont les mêmes que ceux utilisés par l’émulateur de stockage Azure hérité.

* Nom du compte : `devstoreaccount1`
* Clé de compte : `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> En plus de l’authentification SharedKey, Azurite prend en charge l’authentification SAP des comptes et des services. L’accès anonyme est également disponible quand un conteneur est défini pour autoriser l’accès public.

### <a name="connection-string"></a>Chaîne de connexion

La méthode la plus simple pour vous connecter à Azurite à partir de votre application consiste à configurer une chaîne de connexion dans le fichier config de votre application, qui référence le raccourci *UseDevelopmentStorage=true*. Voici un exemple de chaîne de connexion dans un fichier *app.config* :

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Pour plus d’informations, consultez [Configuration des chaînes de connexion Stockage Azure](storage-configure-connection-string.md).

### <a name="custom-storage-accounts-and-keys"></a>Comptes et clés de stockage personnalisés

Azurite prend en charge les noms de compte et les clés de stockage personnalisés via la définition de la variable d’environnement `AZURITE_ACCOUNTS` au format suivant : `account1:key1[:key2];account2:key1[:key2];...`.

Par exemple, utilisez un compte de stockage personnalisé qui a une clé :

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

Vous pouvez aussi utiliser plusieurs comptes de stockage avec 2 clés chacun :

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Par défaut, Azurite actualise les noms de compte et les clés personnalisés à partir de la variable d’environnement toutes les minutes. Avec cette fonctionnalité, vous pouvez effectuer dynamiquement une rotation de la clé de compte ou ajouter de nouveaux comptes de stockage sans redémarrer Azurite.

> [!NOTE]
> Le compte de stockage `devstoreaccount1` par défaut est désactivé quand vous définissez des comptes de stockage personnalisés.

> [!NOTE]
> Mettez à jour la chaîne de connexion en conséquence lors de l’utilisation de noms et de clés de compte personnalisés.

> [!NOTE]
> Utilisez le mot clé `export` pour définir des variables d’environnement dans un environnement Linux ; dans Windows, utilisez `set`.

### <a name="storage-explorer"></a>Explorateur Stockage

Dans l’Explorateur Stockage Azure, connectez-vous à Azurite en cliquant sur l’icône **Add Account** (Ajouter un compte), sélectionnez **Attach to a local emulator** (Attacher à un émulateur local), puis cliquez sur **Connect** (Connexion).

## <a name="differences-between-azurite-and-azure-storage"></a>Différences entre Azurite et Stockage Azure

Il existe des différences fonctionnelles entre une instance locale d’Azurite et un compte de stockage Azure dans le cloud.

### <a name="endpoint-and-connection-url"></a>Point de terminaison et URL de connexion

Les points de terminaison de service pour Azurite se distinguent des points de terminaison d’un compte de stockage Azure. Dans la mesure où l’ordinateur local n’effectue pas de résolution de noms de domaine, les points de terminaison Azurite doivent être des adresses locales.

Quand vous adressez une ressource dans un compte de stockage Azure, le nom du compte fait partie du nom d’hôte de l’URI. La ressource adressée fait partie du chemin de l’URI :

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

L’URI suivant est une adresse valide pour un objet blob dans un compte de stockage Azure :

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Dans la mesure où l’ordinateur local n’effectue pas de résolution de noms de domaine, le nom du compte fait partie du chemin de l’URI au lieu du nom d’hôte. Utilisez le format d’URI suivant pour une ressource dans Azurite :

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Vous pouvez utiliser l’adresse suivante pour accéder à un objet blob dans Azurite :

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>Mise à l’échelle et niveau de performance

Azurite n’est pas un service de stockage scalable et ne prend pas en charge un grand nombre de clients simultanés. Il est impossible de garantir le niveau de performance. Azurite est conçu à des fins de développement et de test.

### <a name="error-handling"></a>Gestion des erreurs

Azurite s’aligne sur la logique de gestion des erreurs du Stockage Azure, mais il existe des différences. Par exemple, les messages d’erreur ne sont pas toujours les mêmes, alors que les codes d’état d’erreur sont identiques.

### <a name="ra-grs"></a>RA-GRS

Azurite prend en charge la réplication géoredondante avec accès en lecture (RA-GRS). Pour les ressources de stockage, accédez à l’emplacement secondaire en ajoutant **-secondary** au nom du compte. Par exemple, vous pouvez utiliser l’adresse suivante pour accéder à un objet blob à l’aide de l’emplacement secondaire en lecture seule dans Azurite :

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite est open source

Les contributions et suggestions relatives à Azurite sont les bienvenues. Accédez à la page des [projets GitHub](https://github.com/Azure/Azurite/projects) ou des [problèmes GitHub](https://github.com/Azure/Azurite/issues) relatifs à Azurite pour connaître les jalons et les éléments de travail que nous suivons en ce qui concerne les fonctionnalités à venir et les résolutions de bogues. Les éléments de travail détaillés sont également suivis dans GitHub.

## <a name="next-steps"></a>Étapes suivantes

* L’article [Utilisation de l’émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md) documente l’émulateur de stockage Azure hérité, qui est remplacé par Azurite.
* L’article [Configuration des chaînes de connexion Stockage Azure](storage-configure-connection-string.md) explique comment assembler une chaîne de connexion Stockage Azure valide.
