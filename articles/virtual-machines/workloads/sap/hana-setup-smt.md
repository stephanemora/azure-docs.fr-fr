---
title: Comment configurer un serveur SMT pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Découvrez comment configurer un serveur SMT pour SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/25/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9350b6d2a8b593e224da817bcbc3142605276248
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599123"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configuration de serveur SMT pour SUSE Linux

Dans cet article, nous allons passer en revue les étapes de configuration du serveur SMT pour SAP HANA sur Azure (grandes instances), également appelé infrastructure BareMetal.

Les grandes instances SAP HANA n’ont pas de connexion directe à Internet. En conséquence, l’inscription d’une telle unité auprès du fournisseur du système d’exploitation ainsi que le téléchargement et l’application des mises à jour ne sont pas simples. Avec SUSE Linux, une solution peut être de configurer un serveur SMT sur une machine virtuelle Azure (VM). Hébergez la machine virtuelle dans un réseau virtuel Azure connecté à la grande instance HANA (HLI). Avec le serveur SMT, l’unité de grande instance HANA peut inscrire et télécharger des mises à jour. 

Pour plus d’informations sur SUSE, reportez-vous à son [Outil de gestion des abonnements pour SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

## <a name="prerequisites"></a>Configuration requise

Pour installer un serveur SMT pour des grandes instances HANA, vous avez d’abord besoin des éléments suivants :

- un réseau virtuel Azure connecté au circuit ExpressRoute de la grande instance HANA,
- un compte SUSE associé à une organisation. L’organisation doit disposer d’un abonnement SUSE valide.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installation du serveur SMT sur une machine virtuelle Azure

1. Connectez-vous tout d’abord à l’[Espace clients SUSE](https://scc.suse.com/). Accédez à **Organization** > **Organization Credentials**. Dans cette section, vous devez rechercher les informations d’identification nécessaires pour configurer le serveur SMT.

2. Installez une machine virtuelle SUSE Linux dans le réseau virtuel Azure. Pour déployer la machine virtuelle, prenez une image de la galerie SLES 12 SP2 d’Azure (sélectionnez l’image BYOS SUSE). Dans le processus de déploiement, ne définissez pas de nom DNS et n’utilisez pas d’adresses IP statiques.

    ![Capture d’écran du déploiement d’une machine virtuelle pour un serveur SMT.](./media/hana-installation/image3_vm_deployment.png)

    La machine virtuelle déployée a l’adresse IP interne du réseau virtuel Azure : 10.34.1.4. Le nom de la machine virtuelle est *smtserver*. Après l’installation, vérifiez la connectivité aux grandes instances HANA. Selon la façon dont vous avez organisé la résolution de noms, il se peut que vous deviez configurer la résolution des grandes instances HANA dans le fichier etc/hosts de la machine virtuelle Azure. 

3. Ajoutez un disque à la machine virtuelle. Vous utilisez ce disque pour stocker les mises à jour et le disque de démarrage lui-même pourrait être trop petit. Ici, le disque a été monté sur /srv/www/htdocs, comme indiqué dans la capture d’écran suivante. Un disque de 100 Go devrait suffire.

    ![Capture d’écran montrant le disque ajouté dans la fenêtre PuTTy.](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

4. Connectez-vous aux grandes instances HANA, maintenez /etc/hosts. Vérifiez si vous pouvez accéder à la machine virtuelle Azure qui exécutera le serveur SMT sur le réseau.

5. Connectez-vous à la machine virtuelle Azure qui doit exécuter le serveur SMT. Si vous utilisez PuTTY pour vous connecter à la machine virtuelle, exécutez cette séquence de commandes dans la fenêtre Bash :

    ```
    cd ~
    echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
    ```

6. Redémarrez votre interpréteur de commandes Bash pour activer les paramètres. Ensuite, démarrez YaST.

7. Connectez votre machine virtuelle (smtserver) au site SUSE.

    ```
    smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
    Registered SLES_SAP 12.2 x86_64
    To server: https://scc.suse.com
    Using E-Mail: email address
    Successfully registered system.
    ```
    
8. Une fois la machine virtuelle connectée au site SUSE, installez les packages SMT. Utilisez la commande putty suivante pour installer les packages SMT.

    ```
    smtserver:~ # zypper in smt
    Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
    Loading repository data...
    Reading installed packages...
    Resolving package dependencies...
    ```
    
    Vous pouvez également utiliser l’outil YAST pour installer les packages SMT. Dans YaST, accédez à **Software Maintenance** (Maintenance logicielle), puis recherchez smt. Sélectionnez **smt**, qui bascule automatiquement vers yast2-smt.

    [![Capture d’écran de SMT dans YAST.](./media/hana-installation/image5_smt_in_yast.PNG)](./media/hana-installation/image5_smt_in_yast.PNG#lightbox)

    Acceptez la sélection pour l’installation sur smtserver. 


9. Une fois l’installation terminée, accédez à la configuration du serveur SMT. Entrez les informations d'identification de votre organisation que vous avez récupérées précédemment à partir du SUSE Customer Center. Entrez également le nom d’hôte de votre machine virtuelle Azure en tant qu’URL du serveur SMT. Dans cet exemple, il s’agit de http:\//smtserver.

    [![Capture d’écran d’une configuration de serveur SMT.](./media/hana-installation/image6_configuration_of_smtserver1.png)](./media/hana-installation/image6_configuration_of_smtserver1.png#lightbox)

10. Vérifiez maintenant si la connexion au SUSE Customer Center fonctionne. Comme le montre la capture d’écran suivante, dans cet exemple, cela a fonctionné.

    [![Capture d’écran d’un test de connexion à l’Espace client SUSE.](./media/hana-installation/image7_test_connect.png)](./media/hana-installation/image7_test_connect.png#lightbox)

11. Une fois le programme d’installation de SMT lancé, fournissez un mot de passe de base de données. Comme il s’agit d’une nouvelle installation, vous devez définir ce mot de passe, comme indiqué dans la capture d’écran suivante.

    [![Capture d’écran de la définition du mot de passe pour la base de données.](./media/hana-installation/image8_define_db_passwd.PNG)](./media/hana-installation/image8_define_db_passwd.PNG#lightbox)

12. Création d'un certificat

    [![Capture d’écran de la création d’un certificat pour le serveur SMT.](./media/hana-installation/image9_certificate_creation.PNG)](./media/hana-installation/image9_certificate_creation.PNG#lightbox)

    À la fin de la configuration, la vérification de la synchronisation peut prendre quelques minutes. Une fois l’installation et la configuration du serveur SMT terminées, vous devez rechercher le référentiel de répertoire sous le point de montage /srv/www/htdocs/. Il existe également certains sous-répertoires sous le référentiel. 

13. Redémarrez le serveur SMT et ses services associés avec ces commandes.

    ```
    rcsmt restart
    systemctl restart smt.service
    systemctl restart apache2
    ```

## <a name="download-packages-onto-the-smt-server"></a>Télécharger des packages sur le serveur SMT

1. Une fois que tous les services ont redémarré, sélectionnez les packages appropriés dans SMT Management (Gestion de SMT) à l’aide de YaST. La sélection du package dépend de l’image du système d’exploitation du serveur de la grande instance HANA. La sélection du package ne dépend pas de la version SLES ou de la machine virtuelle exécutant le serveur SMT. La capture d’écran suivante présente un exemple de l’écran de sélection.

    [![Capture d’écran de sélection de packages.](./media/hana-installation/image10_select_packages.PNG)](./media/hana-installation/image10_select_packages.PNG#lightbox)

2. Démarrez la copie initiale des packages sélectionnés sur le serveur SMT que vous configurez. Cette copie est déclenchée dans l’interpréteur de commandes à l’aide de la commande smt-mirror.

   [ ![Capture d’écran des packages de téléchargement sur le serveur SMT](./media/hana-installation/image11_download_packages.PNG)](./media/hana-installation/image11_download_packages.PNG#lightbox)

    Les packages doivent être copiés dans les répertoires créés sous le point de montage /srv/www/htdocs. Ce processus peut prendre une ou plusieurs heures selon le nombre de packages que vous sélectionnez. Lorsque ce processus s’achève, passez à la configuration du client SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instances"></a>Configurer le client SMT sur des grandes instances HANA

Dans ce cas, le ou les clients sont les grandes instances HANA. Le programme d’installation du serveur SMT a copié le script clientSetup4SMT.sh sur la machine virtuelle Azure. 

Copiez ce script sur la grande instance HANA que vous souhaitez connecter à votre serveur SMT. Démarrez le script avec l’option -h, puis donnez-lui en guise de paramètre le nom de votre serveur SMT. Dans cet exemple, le nom est *smtserver*.

[![Capture d’écran de configuration du client SMT.](./media/hana-installation/image12_configure_client.PNG)](./media/hana-installation/image12_configure_client.PNG#lightbox)

Il est possible que la charge du certificat du serveur par le client réussisse. Dans cet exemple, toutefois, l’inscription échoue, comme illustré dans la capture d’écran suivante.

[![Capture d’écran de la défaillance de l’inscription du client.](./media/hana-installation/image13_registration_failed.PNG)](./media/hana-installation/image13_registration_failed.PNG#lightbox)

Si l’inscription échoue, lisez le [document de support SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024), puis effectuez les étapes qu’il décrit.

> [!IMPORTANT] 
> Pour le nom du serveur, fournissez le nom de la machine virtuelle, en l’occurrence *smtserver*, sans le nom de domaine complet. 
    
Après avoir effectué ces étapes, exécutez la commande suivante sur la grande instance HANA :
    
```
SUSEConnect –cleanup
```

> [!Note] 
> Attendez quelques minutes après cette étape. Si vous exécutez clientSetup4SMT.sh immédiatement, une erreur peut s’afficher.

Si vous rencontrez un problème, que vous devez le résoudre en suivant les étapes de l’article sur SUSE, redémarrez clientSetup4SMT.sh sur la grande instance HANA. L’opération devrait à présent aboutir correctement.

[![Capture d’écran d’une inscription de client réussie.](./media/hana-installation/image14_finish_client_config.PNG)](./media/hana-installation/image14_finish_client_config.PNG#lightbox)

Vous avez configuré la connexion du client SMT du HLI sur le serveur SMT installé sur la machine virtuelle Azure. Maintenant, prenez « zypper up » ou « zypper in » pour installer les mises à jour du système d’exploitation sur de grandes instances HANA ou installez d’autres packages. Vous pouvez uniquement obtenir des mises à jour que vous avez téléchargées auparavant sur le serveur SMT.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la migration SAP HANA sur Azure (grandes instances) vers des machines virtuelles Azure.

> [!div class="nextstepaction"]
> [Migration de SAP HANA sur grande instance Azure vers des machines virtuelles Azure](hana-large-instance-virtual-machine-migration.md)
