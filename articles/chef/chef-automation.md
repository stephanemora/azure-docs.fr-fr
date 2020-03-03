---
title: Démarrage rapide – Configurer une machine virtuelle Windows dans Azure avec Chef
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser Chef pour déployer et configurer une machine virtuelle Windows dans Azure
keywords: chef, azure, devops, machine virtuelle
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589493"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Démarrage rapide – Configurer une machine virtuelle Windows dans Azure avec Chef

Chef vous permet de tirer parti des configurations d’état souhaité et d’automatisation.

Avec la dernière version d’API cloud, Chef assure l’intégration transparente avec Azure, ce qui vous donne la possibilité de configurer et de déployer les états de configuration via une commande unique.

Dans cet article, vous allez configurer votre environnement Chef pour provisionner des machines virtuelles Azure et créer une stratégie ou un livre de recettes, puis procéder à son déploiement sur une machine virtuelle Azure.

## <a name="chef-basics"></a>Principes fondamentaux de Chef

Avant de commencer, [passez en revue les concepts de base de Chef](https://www.chef.io/chef).

Le schéma ci-dessous illustre l’architecture globale de Chef.

![Architecture de Chef](media/chef-automation/chef-architecure.png)

Chef comporte trois principaux composants architecturaux : 
- Serveur Chef – Point de gestion. Il existe deux options pour le serveur Chef : une solution hébergée ou une solution locale.
- Client Chef (nœud) – Agent installé sur les serveurs que vous gérez.
- Chef Workstation – Nom de la station de travail d’administration (où vous créez des stratégies et exécutez des commandes de gestion) et nom du package logiciel des outils Chef.

En règle générale, **votre station de travail** correspond à l’emplacement où vous exécutez des commandes et **Chef Workstation** au package logiciel.

Par exemple, vous téléchargez la commande knife dans **Chef Workstation**, mais vous exécutez les commandes knife à partir de **votre station de travail** pour gérer l’infrastructure.

Chef utilise également les concepts de *livres de recettes* et de *recettes*. Ces termes sont les stratégies définies et appliquées aux serveurs, respectivement.

## <a name="preparing-your-workstation"></a>Préparation de votre station de travail

Tout d’abord, préparez votre station de travail en créant un répertoire pour stocker les fichiers de configuration et les livres de recettes Chef.

Créez un répertoire nommé `C:\Chef`.

Téléchargez et installez la dernière version d'[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sur votre station de travail.

## <a name="configure-azure-service-principal"></a>Configurer le principal du service Azure

Nous allons utiliser un principal de service pour créer plus facilement des ressources Azure à partir de notre station de travail Chef.  Pour créer le principal de service approprié à l’aide des autorisations requises, exécutez les commandes suivantes dans PowerShell :
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Notez vos éléments SubscriptionID, TenantID, ClientID et clé secrète client (le mot de passe que vous avez défini précédemment dans ce tutoriel) car vous aurez besoin de ces valeurs. 

## <a name="setup-chef-server"></a>Configurer le serveur Chef

Ce guide suppose que vous allez vous inscrire à Hosted Chef.

Si vous n’utilisez pas déjà un serveur Chef, vous pouvez effectuer les actions suivantes :

* Inscrivez-vous à [Hosted Chef](https://manage.chef.io/signup), ce qui est le moyen le plus rapide de prendre en main Chef.
* Installez un serveur Chef autonome sur une machine Linux, en suivant les [instructions d’installation](https://docs.chef.io/install_server.html) données dans [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Création d’un compte Hosted Chef

Inscrivez-vous pour obtenir un compte Hosted Chef [ici](https://manage.chef.io/signup).

Pendant le processus d’inscription, vous devrez créer une organisation.

![Fenêtre de création d’une organisation](media/chef-automation/create-organization.png)

Une fois que votre organisation est créée, téléchargez le starter kit.

![Configuration de Chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Si vous recevez une invite pour vous prévenir que vos clés seront réinitialisées, continuez, étant donné que nous ne disposons pas encore d’une infrastructure configurée.
>

Le fichier zip de ce starter kit contient les fichiers config et la clé d’utilisateur de votre organisation dans le répertoire `.chef`.

Vous devez télécharger le fichier `organization-validator.pem` séparément, car il s’agit d’une clé privée et les clés privées ne doivent pas être stockées sur le serveur Chef. À partir de [Chef Manage](https://manage.chef.io/), accédez à la section Administration et sélectionnez Reset Validation Key (Réinitialiser la clé de validation), ce qui fournit un fichier que vous pouvez télécharger séparément. Enregistrez le fichier dans c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configuration de votre station de travail Chef

Extrayez le contenu du fichier `chef-starter.zip` dans `c:\chef`.

Copiez tous les fichiers figurant sous `chef-starter\chef-repo\.chef` dans votre répertoire `c:\chef`.

Copiez le fichier `organization-validator.pem` dans `c:\chef`, s’il est enregistré dans `c:\Downloads`.

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

Ajoutez les informations suivantes dans votre fichier knife.rb, en remplaçant les espaces réservés par vos informations :

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Ces lignes garantissent que Knife référence le répertoire de livres de recettes sous `c:\chef\cookbooks`.

Votre fichier `knife.rb` doit maintenant ressembler à l’exemple suivant :

![Exemple de fichier knife](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Installer Chef Workstation

Ensuite, [téléchargez et installez Chef Workstation](https://downloads.chef.io/chef-workstation/).

Installez Chef Workstation à l’emplacement par défaut.

Sur le Bureau, vous voyez une icône CW PowerShell. Cet outil permet d’interagir avec les produits Chef. CW PowerShell met à votre disposition de nouvelles commandes, comme `chef-run` et les commandes d’interface CLI de Chef (comme `chef`). Déterminez votre version installée de Chef Worstation et des outils Chef avec la commande `chef -v`. Vous pouvez également vérifier la version de votre station de travail en sélectionnant **About Chef Workstation** dans l’application Chef Workstation.

La commande `chef --version` doit retourner quelque chose comme :

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> L’ordre du chemin d’accès est important ! Si vos chemins opscode ne sont pas dans l’ordre adéquat, cela créera des problèmes.
>

Redémarrez votre station de travail avant de continuer.

### <a name="install-knife-azure"></a>Installer Knife Azure

Ce tutoriel suppose que vous utilisez Azure Resource Manager pour interagir avec votre machine virtuelle.

Installez l’extension Knife Azure, qui comprend le plug-in Azure.

Exécutez la commande suivante :

    chef gem install knife-azure ––pre

> [!NOTE]
> L’argument `–-pre` garantit que vous recevez la version RC la plus récente du plug-in Knife Azure, qui permet d’accéder au dernier ensemble d’API.
>
>

Il est probable que plusieurs dépendances soient également installées en même temps.

![Sortie de l’installation de knife-azure](./media/chef-automation/install-knife-azure.png)

Pour vous assurer que tout est configuré correctement, exécutez la commande suivante.

    knife azurerm server list

Si tout est configuré correctement, vous verrez une liste d’images Azure disponibles défiler.

Félicitations ! Votre station de travail est configurée !

## <a name="creating-a-cookbook"></a>Création d’un livre de recettes

Dans Chef, un livre de recettes permet de définir un ensemble de commandes que vous souhaitez exécuter sur votre client managé. La création d’un livre de recettes est très simple. Il suffit d’utiliser la commande `chef generate cookbook` pour générer le modèle de livre de recettes. Ce livre de recettes est destiné à un serveur web qui déploie IIS automatiquement.

Sous `C:\Chef directory`, exécutez la commande suivante.

    chef generate cookbook webserver

Cette commande génère un jeu de fichiers dans le répertoire C:\Chef\cookbooks\webserver. Ensuite, définissez l’ensemble de commandes que le client Chef doit exécuter sur la machine virtuelle managée.

Les commandes sont stockées dans le fichier default.rb. Dans ce fichier, définissez un ensemble de commandes qui installe IIS, démarre IIS et copie un fichier de modèle dans le dossier `wwwroot`.

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

Dans cette étape, vous allez générer un fichier de modèle à utiliser en tant que page `default.html`.

Exécutez la commande suivante pour générer le modèle :

    chef generate template webserver Default.htm

Accédez au fichier `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Modifiez le fichier en y ajoutant le code HTML simple *Hello World*, puis enregistrez le fichier.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Charger le livre de recettes sur le serveur Chef

Dans cette étape, vous faites une copie du livre de recettes que vous avez créé sur l’ordinateur local et vous la chargez sur le serveur hébergé Chef. Une fois chargé, le livre de recettes apparaît sous l’onglet **Policy** (Stratégie).

    knife cookbook upload webserver

![Résultats de l’installation du livre de recettes sur le serveur Chef](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Déployer une machine virtuelle avec Knife Azure

Déployez une machine virtuelle Azure et appliquez le livre de recettes `Webserver` à l’aide de la commande `knife`.

La commande `knife` installe également le service web IIS et la page web par défaut.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

L’exemple de commande `knife` crée une machine virtuelle *Standard_DS2_v2* avec Windows Server 2016 installé dans la région USA Ouest. Modifiez ces valeurs selon les besoins de votre application.

Après avoir exécuté la commande, accédez au portail Azure pour voir votre machine commencer le provisionnement.

![Machine virtuelle en cours de provisionnement](./media/chef-automation/virtual-machine-being-provisioned.png)

L’invite de commande apparaît ci-dessous.

![Sortie de Knife lors de la création de la machine virtuelle](./media/chef-automation/knife-output-when-creating-vm.png)

Une fois le déploiement terminé, l’adresse IP publique de la nouvelle machine virtuelle est affichée. Collez cette valeur dans un navigateur web pour afficher le nouveau site web. Lorsque nous avons déployé la machine virtuelle, nous avons ouvert le port 80, et il doit donc être disponible en externe.   

![Test de la machine virtuelle](./media/chef-automation/testing-the-virtual-machine.png)

Cet exemple utilise du code HTML créatif.

Vous pouvez également afficher l’état du nœud [Chef Manage](https://manage.chef.io/). 

![Visualisation de l’état du nœud](./media/chef-automation/viewing-node-status.png)

N’oubliez pas que vous pouvez également vous connecter par le biais d’une session RDP à partir du portail Azure via le port 3389.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Chef sur Azure](/azure/chef/)