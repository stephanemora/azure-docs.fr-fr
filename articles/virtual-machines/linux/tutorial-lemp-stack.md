---
title: 'Tutoriel : Déployer LEMP sur une machine virtuelle Linux dans Azure'
description: Avec ce didacticiel, vous allez apprendre à installer la pile LEMP sur une machine virtuelle Linux dans Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d603dbf2746608f499ba37b4f17b533b64bc941
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80154353"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Tutoriel : Installer un serveur web LEMP sur une machine virtuelle Linux dans Azure

Cet article vous guide à travers le déploiement d’un serveur web NGINX, de celui de MySQL et de PHP (la pile LEMP) sur une machine virtuelle Ubuntu dans Azure. Pouvant également être installée dans Azure, la pile LEMP est une alternative à la très répandue [pile LAMP](tutorial-lamp-stack.md). Pour voir le serveur LEMP fonctionner, vous pouvez éventuellement installer et configurer un site WordPress. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu (la lettre « L » dans la pile LEMP)
> * Ouvrez le port 80 pour le trafic web
> * Installer NGINX, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur le serveur LEMP

Ce programme d’installation est destiné aux tests rapides ou à la preuve de concept.

Ce tutoriel utilise l’interface CLI disponible dans [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), qui est constamment mise à jour vers la dernière version. Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut d’un bloc de code.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.30 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Installer NGINX, MySQL et PHP

Exécutez la commande suivante pour mettre à jour les sources de package Ubuntu et installer NGINX, MySQL et PHP. 

```bash
sudo apt update && sudo apt install nginx && sudo apt install mysql-server php-mysql php-fpm
```

Vous êtes invité à installer les packages et les autres dépendances. Ce processus installe les extensions PHP minimales requises pour utiliser PHP avec MySQL.  

## <a name="verify-installation-and-configuration"></a>Vérifier l’installation et la configuration


### <a name="verify-nginx"></a>Vérifier NGINX

Vérifiez la version de NGINX à l’aide de la commande suivante :
```bash
nginx -v
```

Avec NGINX installé et le port 80 ouvert sur votre machine virtuelle, le serveur web est désormais accessible à partir d’internet. Pour afficher la page d’accueil de NGINX, ouvrez un navigateur web et entrez l’adresse IP publique de la machine virtuelle. Utilisez l’adresse IP publique dont vous vous êtes servi pour vous connecter par SSH à la machine virtuelle :

![Page par défaut de NGINX][3]


### <a name="verify-and-secure-mysql"></a>Vérifier et sécuriser MySQL

Vérifiez la version de MySQL avec la commande suivante (remarquez le paramètre `V` avec une majuscule) :

```bash
mysql -V
```

Pour mieux sécuriser l’installation de MySQL, notamment en définissant un mot de passe racine, exécutez le script `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Vous pouvez éventuellement configurer le plug-in de validation de mot de passe (recommandé). Ensuite, définissez un mot de passe pour l’utilisateur racine MySQL et configurez les autres paramètres de sécurité pour votre environnement. Nous vous recommandons de répondre par l’affirmative à toutes les questions.

Si vous souhaitez essayer des fonctionnalités de MySQL (créer une base de données MySQL, ajouter des utilisateurs ou changer des paramètres de configuration), connectez-vous à MySQL. Cette étape n’est pas nécessaire pour suivre ce didacticiel. 


```bash
sudo mysql -u root -p
```

Lorsque vous avez terminé, quittez l’invite de mysql en tapant `\q`.

### <a name="verify-php"></a>Vérifier PHP

Vérifiez la version de PHP avec la commande suivante :

```bash
php -v
```

Configurez NGINX pour utiliser l’interface PHP-FPM (PHP FastCGI Process Manager). Exécutez les commandes suivantes pour sauvegarder le fichier de configuration du bloc serveur NGINX d’origine et modifier ensuite le fichier original dans l’éditeur de votre choix :

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

Dans l’éditeur, remplacez le contenu de `/etc/nginx/sites-available/default` par ce qui suit. Consultez les commentaires pour lire une explication sur les paramètres. Remplacez l’adresse IP publique de votre machine virtuelle par *votreadresseIPpublique*, vérifiez la version PHP dans `fastcgi_pass` et conservez les autres paramètres. Puis enregistrez le fichier.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
    }
}
```

Vérifiez la configuration de NGINX à la recherche d’erreurs de syntaxe :

```bash
sudo nginx -t
```

Si la syntaxe est correcte, redémarrez NGINX avec la commande suivante :

```bash
sudo service nginx restart
```

Si vous voulez poursuivre le test, créez une page d’informations PHP rapide pour l’afficher dans un navigateur. La commande suivante crée la page d’informations PHP :

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



À présent, vous pouvez vérifier la page d’informations PHP que vous avez créée. Ouvrez un navigateur web et accédez à `http://yourPublicIPAddress/info.php`. Remplacez l’adresse IP publique de votre machine virtuelle. Elle doit ressembler à cette image.

![Page d’informations PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un serveur LEMP dans Azure. Vous avez appris à :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu
> * Ouvrez le port 80 pour le trafic web
> * Installer NGINX, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur la pile LEMP

Passez au tutoriel suivant pour savoir comment mieux protéger les serveurs web à l’aide des certificats TLS/SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web avec TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
