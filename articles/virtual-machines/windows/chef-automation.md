---
title: Déploiement d’une machine virtuelle Azure avec Chef | Microsoft Docs
description: Découvrez comment utiliser Chef pour effectuer un déploiement et une configuration de machine virtuelle automatisés dans Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 74b92c277b1d6eaa0984e55a70459bad59c2bf84
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719275"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisation du déploiement de machine virtuelle Azure avec Chef

Chef est un excellent outil d’automatisation et de fourniture des configurations d’état souhaitées.

Avec la dernière version d’API cloud, Chef assure l’intégration transparente avec Azure, ce qui vous donne la possibilité de configurer et de déployer les états de configuration via une commande unique.

Dans cet article, vous allez configurer votre environnement Chef pour provisionner des machines virtuelles Azure et créer une stratégie ou un « Livre de recettes », puis procéder à son déploiement sur une machine virtuelle Azure.

## <a name="chef-basics"></a>Principes fondamentaux de Chef
Avant de commencer, [familiarisez-vous avec les principes fondamentaux de Chef](https://www.chef.io/chef).

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

Créez un répertoire nommé C:\Chef.

Téléchargez et installez la dernière version d'[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) sur votre station de travail.

## <a name="configure-azure-service-principal"></a>Configurer le principal du service Azure

Dans sa forme la plus simple et le principal du service Azure se trouve un compte de service.   Nous allons utiliser un principal de service pour créer plus facilement des ressources Azure à partir de notre station de travail Chef.  Pour créer le principal de service correspondant aux autorisations requises, nous devons exécuter les commandes suivantes dans PowerShell :
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Notez les éléments SubscriptionID, TenantID, ClientID et clé secrète client (le mot de passe que vous avez défini précédemment) car vous en aurez besoin plus loin. 

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

Vous devez téléchargé le fichier `organization-validator.pem` séparément, car il s’agit d’une clé privée et les clés privées ne doivent pas être stockées sur le serveur Chef. À partir de [Chef Manage](https://manage.chef.io/), accédez à la section Administration et sélectionnez Réinitialiser la clé de validation, ce qui fournit un fichier que vous pouvez télécharger séparément. Enregistrez le fichier dans c:\chef.

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

validation_client_name   "myorg-validator"

validation_key           "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Ces lignes garantissent que le fichier knife effectue ses références dans notre répertoire de livres de recettes dans c:\chef\cookbooks et utilise également le principal de service Azure que vous avez créé pendant les opérations d’Azure.

Votre fichier knife.rb doit maintenant ressembler à l’exemple suivant :

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

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

Ensuite, [téléchargez et installez](https://downloads.chef.io/chef-workstation/) Chef Workstation.
Installez Chef Workstation à l’emplacement par défaut. Cette installation peut prendre quelques minutes.

Sur le Bureau, « CW PowerShell » apparaît, ce qui correspond à un environnement chargé avec l’outil dont vous avez besoin pour interagir avec les produits Chef. CW PowerShell rend disponibles de nouvelles commandes ad-hoc, comme `chef-run` ainsi que les commandes d'interface CLI traditionnelles de Chef, comme `chef`. Déterminez votre version installée de Chef Worstation et des outils Chef avec la commande `chef -v`. Vous pouvez également vérifier votre version de Chef Workstation en sélectionnant « About Chef Workstation » dans l’application éponyme.

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

    knife azurerm server list

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

Pour ce faire, utilisez la commande **knife azurerm server create**.

Un exemple de la commande apparaît ci-dessous.

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


L’exemple ci-dessus crée une machine virtuelle Standard_DS2_v2 avec Windows Server 2016 installé dans la région USA Ouest. Remplacez vos variables particulières et exécutez.

> [!NOTE]
> À l’aide de la ligne de commande, nous automatisons également les règles de filtrage du réseau de point de terminaison à l’aide du paramètre –tcp-endpoints. Nous avons ouvert les ports 80 et 3389 pour fournir l’accès à la page web et à la session RDP.
>
>

Après avoir exécuté la commande, accédez au portail Azure pour voir que votre machine commence à être provisionnée.

![][15]

L’invite de commande apparaît ci-dessous.

![][16]

Au terme du déploiement, l’adresse IP publique de la machine virtuelle s'affiche. Vous pouvez la copier et la coller dans un navigateur web et afficher le site web que vous avez déployé. Lorsque nous avons déployé la machine virtuelle, nous avons ouvert le port 80, et il doit donc être disponible en externe.   

![][11]

Cet exemple utilise du code HTML créatif.

Vous pouvez également afficher l’état du nœud [Chef Manage](https://manage.chef.io/). 

![][17]

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
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
