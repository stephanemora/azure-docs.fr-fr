---
title: Tutoriel – Déployer LAMP et WordPress sur une machine virtuelle
description: Dans ce tutoriel, vous allez apprendre à installer la pile LAMP et WordPress sur une machine virtuelle Linux dans Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816342"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Tutoriel : Installer une pile LAMP sur une machine virtuelle Linux Azure

Cet article vous guide à travers le déploiement d’un serveur web Apache, de celui de MySQL et de PHP (la pile LAMP) sur une machine virtuelle Ubuntu dans Azure. Pour voir le serveur LAMP fonctionner, vous pouvez éventuellement installer et configurer un site WordPress. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu 
> * Ouvrez le port 80 pour le trafic web
> * Installer Apache, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress 

Ce programme d’installation est destiné aux tests rapides ou à la preuve de concept. Pour plus d’informations sur la pile LAMP, notamment des recommandations relatives à un environnement de production, consultez la [Documentation Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Ce tutoriel utilise l’interface CLI disponible dans [Azure Cloud Shell](../../cloud-shell/overview.md), qui est constamment mise à jour vers la dernière version. Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut d’un bloc de code.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.30 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm). 

L’exemple suivant crée une machine virtuelle nommée *myVM* et des clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`. La commande définit aussi *azureuser* comme nom d’utilisateur administrateur. Vous utilisez ce nom pour vous connecter à la machine virtuelle. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Lorsque la machine virtuelle a été créée, l’interface de ligne de commande Azure affiche des informations similaires à l’exemple suivant. Notez la valeur de `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle dans des étapes ultérieures.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Par défaut, seules les connexions SSH sont autorisées dans les machines virtuelles Linux déployées dans Azure. Étant donné que cette machine virtuelle sera un serveur web, vous devez ouvrir le port 80 à partir d’Internet. Utilisez la commande [az vm open-port](/cli/azure/vm) pour ouvrir le port souhaité.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Pour plus d’informations sur l’ouverture de ports sur votre machine virtuelle, consultez [Ouvrir des ports](nsg-quickstart.md).

## <a name="ssh-into-your-vm"></a>Se connecter avec SSH à votre machine virtuelle

Pour trouver l’adresse IP publique de votre machine virtuelle, exécutez la commande [az network public-ip list](/cli/azure/network/public-ip). Vous aurez besoin de cette adresse au cours de plusieurs étapes ultérieures.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Remplacez l’adresse IP publique correcte de votre machine virtuelle. Dans cet exemple, l’adresse IP est *40.68.254.142*. *azureuser* est le nom d’utilisateur administrateur défini lors de la création de la machine virtuelle.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Installer Apache, MySQL et PHP

Exécutez la commande suivante pour mettre à jour les sources de package Ubuntu et installer Apache, MySQL et PHP. Notez le signe insertion (^) à la fin de la commande, qui fait partie du nom du package `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Vous êtes invité à installer les packages et les autres dépendances. Ce processus installe les extensions PHP minimales requises pour utiliser PHP avec MySQL.  

## <a name="verify-apache"></a>Vérifier Apache

Vérifiez la version d’Apache à l’aide de la commande suivante :
```bash
apache2 -v
```

Avec Apache installé et le port 80 ouvert sur votre machine virtuelle, le serveur web est maintenant accessible à partir d’Internet. Pour afficher la page par défaut d’Apache2 Ubuntu, ouvrez un navigateur web et entrez l’adresse IP publique de la machine virtuelle. Utilisez l’adresse IP publique dont vous vous êtes servi pour vous connecter par SSH à la machine virtuelle :

![Page par défaut d’Apache][3]


## <a name="verify-and-secure-mysql"></a>Vérifier et sécuriser MySQL

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

## <a name="verify-php"></a>Vérifier PHP

Vérifiez la version de PHP avec la commande suivante :

```bash
php -v
```

Si vous voulez poursuivre le test, créez une page d’informations PHP rapide pour l’afficher dans un navigateur. La commande suivante crée la page d’informations PHP :

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

À présent, vous pouvez vérifier la page d’informations PHP que vous avez créée. Ouvrez un navigateur et accédez à `http://yourPublicIPAddress/info.php`. Remplacez l’adresse IP publique de votre machine virtuelle. Elle doit ressembler à cette image.

![Page d’informations PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez déployé un serveur LAMP dans Azure. Vous avez appris à :

> [!div class="checklist"]
> * Créer une machine virtuelle Ubuntu
> * Ouvrez le port 80 pour le trafic web
> * Installer Apache, MySQL et PHP
> * Vérifier l’installation et la configuration
> * Installer WordPress sur le serveur LAMP

Passez au tutoriel suivant pour savoir comment mieux protéger les serveurs web à l’aide des certificats TLS/SSL.

> [!div class="nextstepaction"]
> [Sécuriser un serveur web avec TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
