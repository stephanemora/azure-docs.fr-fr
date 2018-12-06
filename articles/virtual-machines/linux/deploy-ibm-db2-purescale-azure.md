---
title: Déployer IBM DB2 pureScale sur Azure
description: Découvrez comment déployer un [exemple d’architecture](ibm-db2-purescale-azure.md) utilisé récemment par une entreprise pour migrer son environnement IBM DB2 s’exécutant sur z/OS vers IBM DB2 pureScale sur Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 24bdc9867af869437cc0e440a80e5bf4813abbae
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977979"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Déployer IBM DB2 pureScale sur Azure

Cet article décrit comment déployer un [exemple d’architecture](ibm-db2-purescale-azure.md) utilisé récemment par une entreprise pour migrer son environnement IBM DB2 s’exécutant sur z/OS vers IBM DB2 pureScale sur Azure.

Pour suivre les étapes de la migration, consultez les scripts d’installation dans le dépôt [DB2onAzure](http://aka.ms/db2onazure) sur GitHub. Ces scripts reposent sur l’architecture utilisée pour une charge de travail de traitement transactionnel en ligne (OLTP) classique de taille moyenne.

## <a name="get-started"></a>Prise en main

Pour déployer cette architecture, téléchargez et exécutez le script deploy.sh qui se trouve dans le dépôt [DB2onAzure](http://aka.ms/db2onazure) sur GitHub.

Ce dépôt comprend également des scripts que vous pouvez utiliser pour configurer un tableau de bord Grafana qui peut être utilisé pour interroger Prometheus, le système de supervision et d’alerte en open source inclus avec DB2.

> [!NOTE]
> Le script deploy.sh sur le client crée des clés SSH privées et les transmet au modèle de déploiement via HTTPS. Pour une sécurité accrue, nous vous recommandons d’utiliser [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) pour stocker des secrets, des clés et des mots de passe.

## <a name="how-the-deployment-script-works"></a>Fonctionnement du script de déploiement

Le script deploy.sh crée et configure les ressources Azure qui sont utilisées dans cette architecture. Le script vous invite à indiquer l’abonnement Azure et les machines virtuelles utilisées dans l’environnement cible, puis :

-   Configure le groupe de ressources, le réseau virtuel et les sous-réseaux sur Azure pour l’installation.

-   Configure les groupes de sécurité réseau et SSH pour l’environnement.

-   Configure plusieurs cartes réseau sur les machines virtuelles GlusterFS et pureScale DB2.

-   Crée les machines virtuelles de stockage GlusterFS.

-   Crée la machine virtuelle jumpbox.

-   Crée les machines virtuelles DB2 pureScale.

-   Crée la machine virtuelle témoin sur laquelle DB2 pureScale effectue un test ping.

-   Crée une machine virtuelle Windows à utiliser pour le test, mais n’installe rien dessus.

Ensuite, les scripts de déploiement configurent le réseau de zone de stockage virtuel (vSAN) iSCSI pour le stockage partagé sur Azure. Dans cet exemple, iSCSI se connecte à GlusterFS. Cette solution vous donne également la possibilité d’installer les cibles iSCSI sous la forme d’un nœud Windows unique. (iSCSI fournit une interface de stockage de bloc partagé sur TCP/IP qui permet à la procédure d’installation deDB2 pureScale d’utiliser une interface d’appareil pour se connecter à un stockage partagé.) Pour connaître les concepts de base sur GlusterFS, consultez la rubrique [Architecture : Types de volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) dans Bien démarrer avec GlusterFS.

Les scripts de déploiement exécutent les étapes générales suivantes :

1.  Configurer un cluster de stockage partagé sur Azure. GlusterFS est utilisé pour configurer le cluster de stockage partagé. Au moins deux nœuds Linux sont impliqués. Pour plus d’informations sur la configuration, consultez [Setting up Red Hat Gluster Storage in Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) dans la documentation de Red Hat Gluster.

2.  Configurer une interface iSCSI Direct sur des serveurs Linux cibles pour GlusterFS. Pour plus d’informations sur la configuration, consultez [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) dans le Guide d’administration GlusterFS.

3.  Configurer l’initiateur iSCSI sur les machines virtuelles Linux qui accèderont au cluster GlusterFS à l’aide de la cible iSCSI. Pour plus d’informations sur l’installation, consultez [Guide pratique pour configurer une cible et un initiateur iSCSI dans Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) dans la documentation RootUsers.

4.  Installer GlusterFS comme couche de stockage pour l’interface iSCSI.

Après avoir créé l’appareil iSCSI, l’étape finale consiste à installer DB2 pureScale. Dans le cadre de la configuration de DB2 purescale, [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (anciennement nommé GPFS) est compilé et installé sur le cluster GlusterFS. Ce système de fichiers en cluster permet à DB2 pureScale de partager des données avec les différentes machines virtuelles qui exécutent le moteur DB2 pureScale. Pour plus d’informations, consultez la documentation relative à [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) sur le site web d’IBM.

## <a name="db2-purescale-response-file"></a>Fichier réponse DB2 pureScale

Le dépôt GitHub inclut DB2server.rsp, un fichier réponse (.rsp) qui vous permet de générer un script automatisé pour l’installation de DB2 pureScale. Le tableau suivant liste les options de DB2 pureScale utilisées par le fichier réponse pour la configuration. Vous pouvez personnaliser le fichier réponse en fonction de votre environnement d’installation.

> [!NOTE]
> Un exemple de fichier réponse, DB2server.rsp, est inclus dans le dépôt [DB2onAzure](http://aka.ms/db2onazure) sur GitHub. Si vous utilisez ce fichier, vous devez le modifier avant de pouvoir travailler dans votre environnement.

**Options du fichier réponse DB2 pureScale**

| Nom d’écran               | Champ                                        | Valeur                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Bienvenue                   |                                              | Nouvelle installation                                                                                           |
| Choisir un projet          |                                              | DB2 Version 11.1.3.3. Éditions Server avec DB2 pureScale                                              |
| Configuration             | Répertoire                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Choisir le type d'installation                 | Standard                                                                                               |
|                           | J’accepte les conditions IBM                     | Activé                                                                                               |
| Propriétaire de l’instance            | Utilisateur existant pour l’instance, nom d’utilisateur        | DB2sdin1                                                                                              |
| Utilisateur délimité               | Utilisateur existant, nom d’utilisateur                     | DB2sdfe1                                                                                              |
| Système de fichiers de cluster       | Chemin de périphérique de partition disque partagée            | /dev/dm-2                                                                                             |
|                           | Point de montage                                  | /DB2sd\_1804a                                                                                         |
|                           | Disque partagé pour les données                         | /dev/dm-1                                                                                             |
|                           | Point de montage (Données)                           | /DB2fs/datafs1                                                                                        |
|                           | Disque partagé pour le journal                          | /dev/dm-0                                                                                             |
|                           | Point de montage (Journal)                            | /DB2fs/logfs1                                                                                         |
|                           | Services de cluster DB2 Tiebreaker. Chemin de l’appareil | /dev/dm-3                                                                                             |
| Liste des hôtes                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | CF principal par défaut                         | cf1                                                                                                   |
|                           | CF secondaire par défaut                       | cf2                                                                                                   |
| Fichier réponse et résumé | première option                                 | Installer DB2 Server Edition avec la fonctionnalité IBM DB2 pureScale et enregistrer mes paramètres dans un fichier réponse |
|                           | Nom du fichier réponse                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>À propos de ce déploiement

-   Les valeurs de /dev-dm0, /dev-dm1, /dev-dm2 et /dev-dm3 peuvent changer après un redémarrage de la machine virtuelle sur lequel l’installation a lieu (d0 dans le script automatisé). Pour trouver les valeurs appropriées, vous pouvez émettre la commande suivante avant d’exécuter le fichier réponse sur le serveur sur lequel l’installation aura lieu :

```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
```

-   Les scripts d’installation utilisent des alias pour les disques iSCSI afin de trouver facilement les véritables noms.

-   Lorsque le script d’installation est exécuté sur d0, les valeurs **/dev/dm-\*** peuvent être différentes sur d1, cf0 et cf1. Le programme d’installation de DB2 pureScale ne s’en soucie pas.

## <a name="troubleshooting-and-known-issues"></a>Problèmes connus et dépannage

Le dépôt GitHub inclut une Base de connaissances gérée par les auteurs. Il liste les problèmes que vous pouvez potentiellement rencontrer et les résolutions que vous pouvez essayer. Par exemple, des problèmes connus peuvent se produire dans les situations suivantes :

-   Tentative d’accès à l'adresse IP de la passerelle.

-   Compilation du GPL.

-   Échec de la négociation de sécurité entre les hôtes.

-   Détection d’un système de fichiers existant par le programme d’installation de DB2.

-   Installation manuelle de GPFS.

-   Installation de DB2 pureScale lorsque GPFS a déjà été créé.

-   Suppression de DB2 pureScale et GPFS.

Pour plus d’informations sur ces problèmes connus et d’autres, consultez le fichier kb.md dans le dépôt [DB2onAzure](http://aka.ms/Db2onAzure).

## <a name="next-steps"></a>Étapes suivantes

-   [iSCSI GlusterFS](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Création des utilisateurs requis pour l’installation d’une fonctionnalité DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - Commande Créer une instance](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Solution de données de clusters DB2 pureScale](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBMData Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Alliance de modernisation de plateforme : IBM DB2 sur Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure Virtual Datacenter: Lift and Shift Guide](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
