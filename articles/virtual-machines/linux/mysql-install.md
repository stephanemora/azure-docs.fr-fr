---
title: Configurer MySQL sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: Découvrez comment installer la pile MySQL sur une machine virtuelle Linux (famille de systèmes d’exploitation Ubuntu ou Red Hat) dans Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158436"
---
# <a name="how-to-install-mysql-on-azure"></a>Installation de MySQL sur Azure
Dans cet article, vous allez apprendre à installer et à configurer MySQL sur une machine virtuelle Azure qui exécute Linux.


> [!NOTE]
> Vous devez déjà disposer d’une machine virtuelle Microsoft Azure exécutant Linux pour effectuer ce didacticiel. Consultez le [didacticiel sur les machines virtuelles Linux Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour créer et configurer une machine virtuelle Linux avec `mysqlnode` comme nom de la machine virtuelle et `azureuser` en tant qu’utilisateur avant de continuer.
> 
> 

[Dans ce cas, utilisez le port 3306 comme port MySQL.]  

Nous allons utiliser le package du référentiel pour installer MySQL5.6 à titre d'exemple dans cet article. En réalité, MySQL5.6 est une version améliorée en termes de performances par rapport à MySQL5.5.  Plus d’informations [ici](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

## <a name="install-mysql56-on-ubuntu"></a>Installation de MySQL5.6 sur Ubuntu
Nous allons utiliser une VM Linux exécutant Ubuntu.


### <a name="install-mysql"></a>Installer MySQL

Installer MySQL Server 5.6 en basculant vers la `root` utilisateur :

```bash  
sudo su -
```

Instraller mysql-server 5.6 :

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
Pendant l’installation, vous verrez une fenêtre de dialogue apparaît pour vous demander de vous permettent de définir de passe racine MySQL ci-dessous, et vous devez définir le mot de passe ici.
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

Entrez une nouvelle fois le mot de passe pour le confirmer.

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>Se connecter
  
Une fois l’installation du serveur MySQL terminée, le service MySQL démarre automatiquement. Vous pouvez vous connecter au serveur MySQL avec la `root` utilisateur, entrez votre mot de passe.

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>Gérer le service MySQL

Obtenir l’état du service MySQL

```bash   
service mysql status
```
  
Démarrer le Service MySQL

```bash  
service mysql start
```
  
Arrêter le service MySQL

```bash  
service mysql stop
```
  
Redémarrez le service MySQL

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>Installation de MySQL sur le système d’exploitation de Red Hat, CentOS, Oracle Linux
Nous utiliserons ici des machines virtuelles Linux avec CentOS ou Oracle Linux.

### <a name="add-the-mysql-yum-repository"></a>Ajoutez le référentiel yum de MySQL
    
Basculez vers `root` utilisateur :

```bash  
sudo su -
```

Téléchargez et installez le package de lancement MySQL :

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>Activer le référentiel MySQL
Modifier le fichier ci-dessous pour activer le dépôt MySQL afin de télécharger le package MySQL5.6

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
Mettre à jour chaque valeur de ce fichier ci-dessous :

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>Installer MySQL 

Installer MySQL à partir du référentiel.

```bash  
yum install mysql-community-server
```
  
Le package RPM MySQL et tous les packages associés seront installés.


## <a name="manage-the-mysql-service"></a>Gérer le service MySQL
  
Vérifier l’état de service du serveur MySQL :

```bash  
service mysqld status\
```
  
Vérifiez si la valeur par défaut port du serveur MySQL est en cours d’exécution :

```bash  
netstat  –tunlp|grep 3306
```

Démarrer le serveur MySQL :

```bash
service mysqld start
```

Arrêter le serveur MySQL :

```bash
service mysqld stop
```

Définissez MySQL démarre lorsque le système démarre :

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>Installation de MySQL sur SUSE Linux

Nous utiliserons ici une machine virtuelle Linux avec OpenSUSE.

### <a name="download-and-install-mysql-server"></a>Télécharger et installer le serveur MySQL
  
Basculez vers l’utilisateur `root` via la commande ci-dessous :  

```bash  
sudo su -
```
  
Téléchargez et installez le package MySQL :

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>Gérer le service MySQL
  
Vérifiez l’état du serveur MySQL :

```bash  
rcmysql status
```
  
Vérifiez si le port par défaut du serveur MySQL :

```bash  
netstat  –tunlp|grep 3306
```

Démarrer le serveur MySQL :

```bash
rcmysql start
```

Arrêter le serveur MySQL :

```bash
rcmysql stop
```

Définissez MySQL démarre lorsque le système démarre :

```bash
insserv mysql
```

## <a name="next-step"></a>Étape suivante
Pour plus d’informations, consultez le [MySQL](https://www.mysql.com/) site Web.

