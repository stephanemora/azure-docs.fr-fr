---
title: Comment configurer un serveur SMT pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Comment configurer un serveur SMT pour SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982623"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>Configuration de serveur SMT pour SUSE Linux
Les grandes instances SAP HANA n’ont pas de connexion directe à Internet. Par conséquent, le processus incluant l’inscription d’une telle unité auprès du fournisseur du système d’exploitation ainsi que le téléchargement et l’application des correctifs n’est pas un simple. Avec SUSE Linux, une solution peut être de configurer un serveur SMT sur une machine virtuelle Azure. En revanche, la machine virtuelle Azure doit être hébergée sur un réseau virtuel Azure connecté à la grande instance HANA. Avec un test serveur SMT, l’unité de grande instance HANA peut inscrire et télécharger des correctifs. 

SUSE fournit un guide plus étoffé sur son [outil de gestion des abonnement pour SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Comme condition préalable à l’installation d’un serveur SMT accomplissant la tâche pour une grande instance HANA, vous devez disposer des éléments suivants :

- un réseau virtuel Azure connecté au circuit ER de la grande instance HANA ;
- un compte SUSE associé à une organisation, tandis que l’organisation aurait besoin d’un abonnement SUSE valide.

## <a name="installation-of-smt-server-on-azure-vm"></a>Installation du serveur SMT sur une machine virtuelle Azure

Dans le cadre de cette étape, vous installez le serveur SMT sur une machine virtuelle Azure. La première mesure consiste à se connecter au [Centre de clients SUSE](https://scc.suse.com/).

Une fois connecté, accédez à Organisation--> Informations d’identification de l’organisation. Dans cette section, vous devez rechercher les informations d’identification nécessaires pour configurer le serveur SMT.

La troisième étape consiste à installer une machine virtuelle SUSE Linux dans le réseau virtuel Azure. Pour déployer la machine virtuelle, prenez une image de la galerie SLES 12 SP2 d’Azure (sélectionnez l’image BYOS SUSE). Dans le processus de déploiement, ne définissez pas de nom DNS et n’utilisez pas d’adresses IP statiques comme indiqué dans cette capture d’écran.

![Déploiement de machine virtuelle pour le serveur SMT](./media/hana-installation/image3_vm_deployment.png)

La machine virtuelle déployée était plus petite et a obtenu l’adresse IP 10.34.1.4 dans le réseau virtuel Azure. Le nom de la machine virtuelle était smtserver. Après l’installation, la connectivité aux unités de grande instance HANA a été activée. Selon la façon dont vous avez organisé la résolution de noms, il se peut que vous deviez configurer la résolution des unités de grande instance HANA dans le fichier etc/hosts de la machine virtuelle Azure. Ajoutez un disque à la machine virtuelle qui va accueillir les correctifs. Le disque de démarrage lui-même pourrait être trop petit. Dans le cas présenté, le disque a été monté sur /srv/www/htdocs comme indiqué dans la capture d’écran suivante. Un disque de 100 Go devrait suffire.

![Déploiement de machine virtuelle pour le serveur SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Connectez-vous aux unités de grande instance HANA, conservez le fichier /etc/hosts et vérifiez si vous pouvez atteindre la machine virtuelle Azure supposée exécuter le serveur SMT sur le réseau.

Une fois cette vérification réussie, vous devez vous connecter à la machine virtuelle Azure supposée exécuter le serveur SMT. Si vous utilisez PuTTY pour vous connecter à la machine virtuelle, vous devez exécuter cette séquence de commandes dans la fenêtre Bash :

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Après avoir exécuté ces commandes, redémarrez votre interpréteur de commandes pour activer les paramètres. Ensuite, démarrez YaST.

Connectez votre machine virtuelle (smtserver) au site SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Une fois que la machine virtuelle est connectée au site SUSE, installez les packages smt. Saisissez la commande PuTTY suivante pour installer les packages smt.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Vous pouvez également utiliser l’outil YAST pour installer les packages smt. Dans YaST, accédez à Software Maintenance (Maintenance logicielle), puis recherchez smt. Sélectionnez smt, qui bascule automatiquement vers yast2-smt, comme illustré ci-dessous.

![SMT dans YaST](./media/hana-installation/image5_smt_in_yast.PNG)


Acceptez la sélection pour l’installation sur smtserver. Une fois l’installation terminée, accédez à la configuration du serveur SMT, puis entrez les informations d'identification de votre organisation que vous avez récupérées précédemment à partir du Centre de clients SUSE. Entrez également le nom d’hôte de votre machine virtuelle Azure en tant qu’URL du serveur SMT. Dans cette démonstration, il s’agit de https://smtserver, comme dans le graphique suivant.

![Configuration du serveur SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

À l’étape suivante, vous allez vérifier si la connexion au Centre de clients SUSE fonctionne. Comme le montrent les graphiques suivants, dans le cas de la démonstration, cela n’a pas fonctionné.

![Test de connexion au Centre de clients SUSE](./media/hana-installation/image7_test_connect.png)

Lors que le programme d’installation de SMT démarre, vous devez fournir un mot de passe de base de données. Comme il s’agit d’une nouvelle installation, vous devez définir celui-ci comme indiqué dans le graphique suivant.

![Définir le mot de passe pour la base de données](./media/hana-installation/image8_define_db_passwd.PNG)

L’interaction suivante intervient lors de la création d’un certificat. Parcourez la boîte de dialogue comme dans l’illustration suivante pour achever l’étape.

![Créer un certificat pour le serveur SMT](./media/hana-installation/image9_certificate_creation.PNG)

L’étape « Run synchronization check » (Exécuter le contrôle de synchronisation) à la fin de la configuration peut prendre quelques minutes. Une fois l’installation et la configuration du serveur SMT terminées, vous devez rechercher le référentiel de répertoire sous le point de montage /srv/www/htdocs/, ainsi que certains sous-répertoires dans le référentiel. 

Redémarrez le serveur SMT et ses services associés avec ces commandes.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>Télécharger des packages sur le serveur SMT

Une fois que tous les services ont redémarré, sélectionnez les packages appropriés dans SMT Management (Gestion de SMT) à l’aide de YaST. La sélection du package dépend de l’image de système d'exploitation du serveur de grande instance HANA, et non de la publication ou de la version SLES de la machine virtuelle exécutant le serveur SMT. Vous trouverez ci-dessous un exemple de l’écran de sélection.

![Sélectionner des packages](./media/hana-installation/image10_select_packages.PNG)

Une fois les packages sélectionnés, vous devez démarrer leur copie initiale sur le serveur SMT que vous configurés. Cette copie est déclenchée dans l’interpréteur de commandes à l’aide de la commande smt-mirror, comme illustré ci-dessous :


![Télécharger des packages sur le serveur SMT](./media/hana-installation/image11_download_packages.PNG)

Comme ci-dessus, les packages doivent être copiés dans les répertoires créés sous le montage point /srv/www/htdocs. Ce processus peut prendre un certain temps. Selon le nombre de packages que vous sélectionnez, il peut prendre jusqu’à une heure, voire davantage.
Lorsque ce processus s’achève, vous devez passer à la configuration du client SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurer le client SMT sur des unités de grande instance HANA

Dans ce cas, les clients sont les unités de grande instance HANA. Le programme d’installation du serveur SMT a copié le script clientSetup4SMT.sh sur la machine virtuelle Azure. Copiez ce script sur l’unité de grande instance HANA que vous souhaitez connecter à votre serveur SMT. Démarrez le script avec l’option -h, puis donnez-lui en guise de paramètre le nom de votre serveur SMT. Dans ce exemple, il s’agit de smtserver.

![Configurer un client SMT](./media/hana-installation/image12_configure_client.PNG)

Il peut y avoir un scénario où le chargement du certificat à partir du serveur par le client réussit, mais où l’inscription échoue, comme illustré ci-dessous.

![L’inscription du client échoue](./media/hana-installation/image13_registration_failed.PNG)

Si l’inscription échoue, lisez ce [document de support SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024), puis exécutez les étapes qu’il décrit.

> [!IMPORTANT] 
> En tant que nom de serveur, vous devez fournir le nom de la machine virtuelle, en l’occurrence smtserver, sans le nom de domaine complet. Le nom de la machine virtuelle suffit. 

Une fois ces étapes effectuées, vous devez exécuter la commande suivante sur l’unité de grande instance HANA.

```
SUSEConnect –cleanup
```

> [!Note] 
> Dans nos tests, nous avons toujours dû attendre quelques minutes après cette étape. L’exécution immédiate de clientSetup4SMT.sh, après les mesures de correction décrites dans l’article sur SUSE, s’est terminée avec des messages indiquant que le certificat n’était pas encore valide. Une attente de 5 à 10 minutes, puis l’exécution de clientSetup4SMT.sh a abouti à une configuration réussie du client.

Si vous avez rencontré le problème et avez du le résoudre en suivant les étapes de l’article sur SUSE, vous devez redémarrer clientSetup4SMT.sh sur l’unité de grande instance HANA. L’opération devrait à présent aboutir correctement, comme indiqué ci-dessous.

![Inscription du client réussie](./media/hana-installation/image14_finish_client_config.PNG)

Dans le cadre de cette étape, vous avez configuré le client SMT de l’unité de grande instance HANA pour qu’il se connecte au serveur SMT que vous avez installé sur la machine virtuelle Azure. Vous pouvez à présent utiliser « zypper up » ou « zypper in » pour installer des correctifs logiciels de système d’exploitation sur des grandes instances HANA ou installer des packages supplémentaires. Il est entendu que vous pouvez obtenir uniquement les correctifs que vous avez téléchargés auparavant sur le serveur SMT.

**Étapes suivantes**
- Consultez [HANA Installation on HLI](hana-example-installation.md) (Installation de HANA sur HLI).











