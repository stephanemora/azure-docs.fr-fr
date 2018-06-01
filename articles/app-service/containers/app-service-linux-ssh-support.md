---
title: Prise en charge SSH pour Azure App Service sur Linux | Microsoft Docs
description: Apprenez à utiliser SSH avec Azure App Service sur Linux.
keywords: azure app service, application web, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: c2beb67a27b667d31402b903f38dbf116e9425d0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301073"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Prise en charge SSH pour Azure App Service sur Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) est couramment utilisé pour exécuter des commandes d’administration à distance à partir d’un terminal de ligne de commande. App Service sur Linux assure la prise en charge SSH dans le conteneur d’application avec chacune des images Docker intégrées utilisées pour la pile d’exécution des nouvelles applications web. 

![Piles d’exécution](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Pour les images Docker personnalisées, en configurant le serveur SSH dans votre image personnalisée.

Vous pouvez également vous connecter au conteneur directement à partir de votre machine de développement locale par SSH et SFTP.

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

Pour établir une connexion de client SSH avec votre conteneur, votre application doit être en cours d’exécution.

Collez l’URL suivante dans votre navigateur, puis remplacer \<app_name> par le nom de votre application :

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Si vous n’êtes pas déjà authentifié, vous devez le faire à l’aide de votre abonnement Azure pour vous connecter. Une fois authentifié, vous voyez un interpréteur de commandes dans le navigateur, vous permettant d’exécuter des commandes à l’intérieur de votre conteneur.

![Connexion SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Utiliser la prise en charge SSH avec des images Docker personnalisées

Pour qu’une image Docker personnalisée prenne en charge la communication entre le conteneur et le client dans le portail Azure, procédez comme suit pour votre image Docker.

Cette procédure est affichée dans le référentiel Azure App Service en tant qu’[exemple](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Incluez l’installation `openssh-server` dans [l’instruction `RUN`](https://docs.docker.com/engine/reference/builder/#run) au sein du fichier Dockerfile de votre image et définissez le mot de passe du compte racine sur `"Docker!"`.

    > [!NOTE]
    > Cette configuration n’autorise pas les connexions externes avec le conteneur. SSH est accessible uniquement via le site Kudu/SCM, authentifié à l’aide des informations d’identification de publication.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Ajoutez une [instruction `COPY`](https://docs.docker.com/engine/reference/builder/#copy) au fichier Dockerfile pour copier un fichier [sshd_config](http://man.openbsd.org/sshd_config) dans le répertoire */etc/ssh/*. Votre fichier de configuration doit être basé sur le fichier sshd_config dans le référentiel GitHub Azure-App-Service [ici](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > Le fichier *sshd_config* doit inclure les éléments suivants pour éviter que la connexion échoue : 
    > * `Ciphers` doit inclure au moins un des éléments suivants : `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` doit inclure au moins un des éléments suivants : `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Incluez le port 2222 dans [l’instruction `EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) pour le fichier Dockerfile. Bien que le mot de passe racine soit connu, le port 2222 n’est pas accessible à partir d’Internet. Il s’agit seulement d’un port interne accessible uniquement par les conteneurs au sein du réseau de pont d’un réseau privé virtuel.

    ```docker
    EXPOSE 2222 80
    ```

1. Veillez à démarrer le service SSH à l’aide d’un script shell (voir exemple dans [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

Le fichier Dockerfile utilise le [l’instruction `ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint) pour exécuter le script.

    ```docker
    COPY startup /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Ouvrir une session SSH à partir d’un interpréteur de commandes à distance

> [!NOTE]
> Cette fonctionnalité est actuellement en préversion.
>

À l’aide du tunneling TCP, vous pouvez créer une connexion réseau entre votre machine de développement et Web App pour conteneurs via une connexion WebSocket authentifiée. Il vous permet d’ouvrir une session SSH avec votre conteneur en cours d’exécution dans App Service à partir du client de votre choix.

Pour commencer, vous devez installer [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Pour voir comment il fonctionne sans installer Azure CLI, ouvrez [Azure Cloud Shell](../../cloud-shell/overview.md). 

Ajouter l’extension App Service la plus récente en exécutant [az extension add](/cli/azure/extension?view=azure-cli-latest#az-extension-add) :

```azurecli-interactive
az extension add -–name webapp
```

Si vous avez déjà exécuté `az extension add`, exécutez plutôt [az extension update](/cli/azure/extension?view=azure-cli-latest#az-extension-update) :

```azurecli-interactive
az extension update -–name webapp
```

Ouvrez une connexion à distance vers votre application à l’aide de la commande [az webapp remote-connection create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Spécifiez _\<groupe\_nom>_ et \_< app\_name>_ pour votre application, puis remplacez \<port> par un numéro de port local.

```azurecli-interactive
az webapp remote-connection create --resource-group <group_name> -n <app_name> -p <port> &
```

> [!TIP]
> `&` à la fin de la commande est simplement présent pour des raisons pratiques si vous utilisez Cloud Shell. Il exécute le processus en arrière-plan afin que vous puissiez exécuter la commande suivante dans le même interpréteur de commandes.

La sortie de commande vous donne les informations dont vous avez besoin pour ouvrir une session SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Ouvrez une session SSH avec votre conteneur au moyen du client de votre choix, en utilisant le port local. L’exemple suivant utilise la commande [ssh](https://ss64.com/bash/ssh.html) par défaut :

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Lorsque cela vous est demandé, tapez `yes` pour poursuivre la connexion. Vous êtes alors invité à entrer le mot de passe. Utilisez `Docker!`, qui vous a été indiqué précédemment.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Une fois authentifié, vous devez voir l’écran d’accueil de session.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Vous êtes maintenant connecté à votre connecteur. 

Essayez d’exécuter la commande [top](https://ss64.com/bash/top.html). Vous devez être en mesure de voir les processus de votre application dans la liste des processus. Dans l’exemple ci-dessous, il s’agit de celui avec `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez poser des questions et signaler vos préoccupations sur le [forum Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Pour plus d’informations sur Web App pour conteneurs, consultez :

* [Présentation du débogage distant des applications Node.js sur Azure App Service à partir de VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Guide pratique pour utiliser une image Docker personnalisée pour Web App pour conteneurs](quickstart-docker-go.md)
* [Utiliser .NET Core dans Azure App Service sur Linux](quickstart-dotnetcore.md)
* [Utiliser Ruby dans Azure App Service sur Linux](quickstart-ruby.md)
* [Forum aux questions sur Azure App Service Web App for Containers](app-service-linux-faq.md)