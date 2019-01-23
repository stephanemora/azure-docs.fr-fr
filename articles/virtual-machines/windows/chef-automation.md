---
title: Déploiement d’une machine virtuelle Azure avec Chef | Microsoft Docs
description: Découvrez comment utiliser Chef pour effectuer un déploiement et une configuration de machine virtuelle automatisés dans Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: jeconnoc
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: d57a7baafc533aee52ec8012d410d5f25b510b60
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359950"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisation du déploiement de machine virtuelle Azure avec Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef est un excellent outil d’automatisation et de fourniture des configurations d’état souhaitées.

Avec la dernière version d’API cloud, Chef assure l’intégration transparente avec Azure, ce qui vous donne la possibilité de configurer et de déployer les états de configuration via une commande unique.

Dans cet article, vous allez configurer votre environnement Chef pour provisionner des machines virtuelles Azure et créer une stratégie ou un « Livre de recettes », puis procéder à son déploiement sur une machine virtuelle Azure.

## <a name="chef-basics"></a>Principes fondamentaux de Chef
Avant de commencer, [familiarisez-vous avec les principes fondamentaux de Chef](http://www.chef.io/chef).

Le schéma ci-dessous illustre l’architecture de Chef de haut niveau.

![][2]

Chef comporte trois principaux composants architecturaux : Le serveur Chef, le client Chef (nœud) et la station de travail Chef.

Le serveur Chef est le point de gestion. Il existe deux options pour le serveur Chef : une solution hébergée ou une solution locale.

Le client Chef (nœud) est l’agent qui se trouve sur les serveurs que vous gérez.

Chef Workstation, à savoir la station de travail Chef, correspond soit au nom de la station de travail d’administrateur où vous créez des stratégies et exécutez des commandes de gestion, soit au nom du package logiciel des outils Chef.

En règle générale, _votre station de travail_ correspond à l’emplacement où vous effectuez des actions et _Chef Workstation_ au package logiciel.
Par exemple, vous allez télécharger la commande knife dans _Chef Workstation_, mais vous allez exécuter des commandes knife à partir de _votre station de travail_ pour gérer l’infrastructure.

Chef utilise également les concepts de « livres de recettes » et de « recettes », qui sont effectivement les stratégies que nous définissons et appliquons aux serveurs.

## <a name="preparing-your-workstation"></a>Préparation de votre station de travail

Tout d’abord, préparez votre station de travail en créant un répertoire pour stocker les fichiers de configuration et les livres de recettes Chef.

Créez un répertoire nommé C:\chef.

Téléchargez vos [paramètres de publication](https://docs.microsoft.com/en-us/dynamics-nav/how-to--download-and-import-publish-settings-and-subscription-information) Azure PowerShell.

## <a name="setup-chef-server"></a>Configurer le serveur Chef

Ce guide suppose que vous allez vous inscrire à Hosted Chef.

Si vous n’utilisez pas déjà un serveur Chef, vous pouvez effectuer les actions suivantes :

* Inscrivez-vous à [Hosted Chef](https://manage.chef.io/signup), ce qui est le moyen le plus rapide de prendre en main Chef.
* Installez un serveur Chef autonome sur une machine Linux, en suivant les [instructions d’installation](https://docs.chef.io/install_server.html) données dans [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Création d’un compte Hosted Chef

Inscrivez-vous pour obtenir un compte Hosted Chef [ici](https://manage.chef.io/signup).

Pendant le processus d’inscription, vous devrez créer une organisation.

![][3]

Une fois que votre organisation est créée, téléchargez le starter kit.

![][4]

> [!NOTE]
> Si vous recevez une invite pour vous prévenir que vos clés seront réinitialisées, continuez, étant donné que nous ne disposons pas encore d’une infrastructure configurée.
>

Le fichier zip de ce starter kit contient les fichiers config et la clé d’utilisateur de votre organisation dans le répertoire `.chef`.

Vous devez téléchargé le fichier `organization-validator.pem` séparément, car il s’agit d’une clé privée et les clés privées ne doivent pas être stockées sur le serveur Chef. À partir de [Chef Manage](https://manage.chef.io/), sélectionnez Reset Validation Key (Réinitialiser la clé de validation), ce qui fournit un fichier que vous pouvez télécharger séparément. Enregistrez le fichier dans c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configuration de votre station de travail Chef

Extrayez le contenu du fichier chef-starter.zip dans C:\chef.

Copiez tous les fichiers sous chef-starter\chef-repo\.chef vers votre répertoire C:\chef.

Copiez le fichier `organization-validator.pem` vers c:\chef, s’il est enregistré dans c:\Downloads.

Votre répertoire doit ressembler à ce qui suit.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Vous devez maintenant avoir cinq fichiers et quatre répertoires (notamment le répertoire chef-repo vide) à la racine de c:\chef.

### <a name="edit-kniferb"></a>Modifier knife.rb

Les fichiers PEM contiennent les clés privées de votre organisation et d’administration pour la communication, tandis que le fichier knife.rb contient votre configuration knife. Il convient de modifier le fichier knife.rb.

Ouvrez le fichier knife.rb dans l’éditeur de votre choix. Le fichier non modifié doit ressembler à ceci :

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Ajoutez les informations suivantes à votre fichier knife.rf :

validation_client_name   "myorg-validator" validation_key           ""#{current_dir}/myorg.pem"

Ajoutez également la ligne suivante, pour refléter le nom de votre fichier de paramètres de publication Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Modifiez « cookbook_path » en supprimant les caractères /../ de sorte à obtenir :

    cookbook_path  ["#{current_dir}/cookbooks"]

Ces lignes garantissent que le fichier knife effectue ses références dans notre répertoire de livres de recettes dans c:\chef\cookbooks et utilise également notre fichier de paramètres de publication Azure pendant les opérations d’Azure.

Votre fichier knife.rb doit maintenant ressembler à l’exemple suivant :

![][6]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
knife.rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "mynode"
client_key               "#{current_dir}/user.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
validation_client_name   "myorg-validator"
validation_key           ""#{current_dir}/myorg.pem"
cookbook_path            ["#{current_dir}/cookbooks"]
knife[:azure_publish_settings_file] = "yourfilename.publishsettings"
```

## <a name="install-chef-workstation"></a>Installer Chef Workstation

Ensuite, [téléchargez et installez](https://downloads.chef.io/chef-workstation/) Chef Workstation.
Installez Chef Workstation à l’emplacement par défaut. Cette installation peut prendre quelques minutes.

Sur le Bureau, « CW PowerShell » apparaît, ce qui correspond à un environnement chargé avec l’outil dont vous avez besoin pour interagir avec les produits Chef. CW PowerShell rend disponibles de nouvelles commandes ad-hoc, comme `chef-run` ainsi que les commandes d’interface CLI traditionnelles de Chef, comme `chef`. Déterminez votre version installée de Chef Worstation et des outils Chef avec la commande `chef -v`. Vous pouvez également vérifier votre version de Chef Workstation en sélectionnant « About Chef Workstation » dans l’application éponyme.

La commande `chef --version` doit retourner quelque chose comme :

```
Chef Workstation: 0.2.29
  chef-run: 0.2.2
  Chef Client: 14.6.47x
  delivery-cli: master (6862f27aba89109a9630f0b6c6798efec56b4efe)
  berks: 7.0.6
  test-kitchen: 1.23.2
  inspec: 3.0.12
```

> [!NOTE]
> L’ordre du chemin d’accès est important !  Si vos chemins opscode ne sont pas dans l’ordre adéquat, cela créera des problèmes.
>

Redémarrez votre station de travail avant de continuer.

### <a name="install-knife-azure"></a>Installer Knife Azure

Ce tutoriel suppose que vous utilisez Azure Resource Manager pour interagir avec votre machine virtuelle.

Installez l’extension Knife Azure. Cela fournit le « plug-in Azure » à Knife.

Exécutez la commande ci-dessous.

    chef gem install knife-azure ––pre

> [!NOTE]
> L’argument –pre garantit que vous recevez la dernière version RC du plug-in Knife Azure, qui permet d’accéder à la dernière série d’API.
>
>

Il est probable que plusieurs dépendances soient également installées en même temps.

![][8]

Pour vous assurer que tout est configuré correctement, exécutez la commande suivante.

    knife azure image list

Si tout est configuré correctement, vous verrez une liste d’images Azure disponibles défiler.

Félicitations ! Votre station de travail est configurée !

## <a name="creating-a-cookbook"></a>Création d’un livre de recettes

Dans Chef, un livre de recettes permet de définir un ensemble de commandes que vous souhaitez exécuter sur votre client managé. La création d’un livre de recettes est très simple. Il suffit d’utiliser la commande **chef generate cookbook** pour générer le modèle de livre de recettes. Ce livre de recettes est destiné à un serveur web qui déploie IIS automatiquement.

Dans le répertoire C:\Chef, exécutez la commande suivante.

    chef generate cookbook webserver

Cette commande génère un jeu de fichiers dans le répertoire C:\Chef\cookbooks\webserver. Ensuite, définissez le jeu de commandes du client Chef de sorte à ce qu’il s’exécute sur la machine virtuelle gérée.

Les commandes sont stockées dans le fichier default.rb. Dans ce fichier, définissez un jeu de commandes qui installe IIS, démarre IIS et copie un fichier de modèle dans le dossier wwwroot.

Modifiez le fichier C:\chef\cookbooks\webserver\recipes\default.rb et ajoutez les lignes suivantes.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Enregistrez le fichier une fois que vous avez terminé.

## <a name="creating-a-template"></a>Création d’un modèle
Au cours de cette étape, vous allez générer un fichier de modèle à utiliser en tant que page default.html.

Exécutez la commande suivante pour générer le modèle :

    chef generate template webserver Default.htm

Accédez au fichier `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Modifiez le fichier en y ajoutant le code html simple « Hello World », puis enregistrez-le.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Téléchargez le livre de recettes sur le serveur Chef
Au cours de cette étape, vous faites une copie du livre de recettes que vous avez créé sur l’ordinateur local et vous la chargez vers le serveur hébergé Chef. Une fois téléchargé, le livre de recettes apparaît sous l’onglet **Stratégie**.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Déployer une machine virtuelle avec Knife Azure
Déployez une machine virtuelle Azure et appliquez le livre de recettes « Webserver » qui installe le service web IIS et la page web par défaut.

Pour ce faire, utilisez la commande **knife azure server create** .

Un exemple de la commande apparaît ci-dessous.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Les paramètres sont clairs. Remplacez vos variables particulières et exécutez.

> [!NOTE]
> À l’aide de la ligne de commande, nous automatisons également les règles de filtrage du réseau de point de terminaison à l’aide du paramètre –tcp-endpoints. Nous avons ouvert les ports 80 et 3389 pour fournir l’accès à notre page Web et à la session RDP.
>
>

Après avoir exécuté la commande, accédez au portail Azure pour voir que votre machine commence à être provisionnée.

![][13]

L’invite de commande apparaît ci-dessous.

![][10]

Une fois le déploiement terminé, vous devez être en mesure de vous connecter au service web sur le port 80, car vous l’avez ouvert quand vous avez provisionné la machine virtuelle avec la commande Knife Azure. Étant donné que cette machine virtuelle est la seule machine virtuelle dans ce service cloud, connectez-la avec l’URL du service cloud.

![][11]

Cet exemple utilise du code HTML créatif.

N’oubliez pas que vous pouvez également vous connecter par le biais d’une session RDP à partir du portail Azure via le port 3389.

Merci ! Démarrez votre voyage en matière d’Infrastructure en tant que code avec Azure dès aujourd’hui !

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->
