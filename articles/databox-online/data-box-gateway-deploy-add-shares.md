---
title: Transférer des données avec Azure Data Box Gateway | Microsoft Docs
description: Découvrez comment ajouter et vous connecter à des partages sur un appareil Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6220a794c10f43d562155adbad910694a233ec88
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166015"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Didacticiel : Transférer des données avec Azure Data Box Gateway (préversion)


## <a name="introduction"></a>Introduction

Cet article explique comment ajouter et se connecter à des partages sur un appareil Data Box Gateway. Une fois les partages ajoutés, l’appareil Data Box Gateway peut transférer les données vers Azure.

Cette procédure peut prendre environ dix minutes. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un partage
> * Vous connecter à un partage

> [!IMPORTANT]
> - Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 
 
## <a name="prerequisites"></a>Prérequis

Avant d’ajouter des partages à votre appareil Data Box Gateway, assurez-vous que :

* Vous avez approvisionné un appareil virtuel et vous y êtes connecté comme indiqué dans [Approvisionnement d’Azure Data Box Gateway dans Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [Approvisionnement d’Azure Data Box Gateway dans VMware](data-box-gateway-deploy-provision-vmware.md). 

    L’appareil virtuel est activé comme détaillé dans [Connecter et activer votre appareil Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md). Il est prêt à créer des partages et à transférer des données.


## <a name="add-a-share"></a>Ajouter un partage

Pour créer un partage, procédez comme suit dans le [Portail Azure](https://portal.azure.com/).

1. Revenez au Portail Azure. Accédez à **Toutes les ressources**, puis recherchez votre ressource Data Box Gateway.
    
2. Dans la liste filtrée des ressources, sélectionnez votre ressource Data Box Gateway, puis accédez à **Vue d’ensemble**. Cliquez sur **+ Ajouter un partage** dans la barre de commandes de l’appareil.
   
   ![Ajouter un partage](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Dans **Ajouter un partage**, spécifiez les paramètres du partage. Indiquez un nom unique pour votre partage. 

   Les noms de partage peuvent uniquement contenir des chiffres, des lettres minuscules et des traits d’union. Le nom de partage doit contenir entre 3 et 63 caractères et commencer par une lettre ou un chiffre. Chaque trait d’union doit être précédé et suivi d’un caractère autre qu’un tiret.
    
5. Sélectionnez un **type** de partage. Le type peut être SMB ou NFS, SMB étant la valeur par défaut. SMB est la norme pour les clients Windows, tandis que NFS est utilisé pour les clients Linux. Selon que vous choisissez un partage SMB ou NFS, les options proposées diffèrent légèrement. 

6. Vous devez fournir un compte de stockage dans lequel résidera le partage. Si le conteneur n’existe pas déjà, un conteneur est créé dans le compte de stockage avec le nom du partage. Si le conteneur existe déjà, le conteneur existant est utilisé. 
    
7. Choisissez le **service de stockage** d’objet blob de blocs, d’objet blob de pages ou de fichier. Le type de service choisi varie selon le format dans lequel vous souhaitez que les données résident dans Azure. Par exemple, dans cette instance, nous voulons que les données résident en tant qu’objets blob de blocs dans Azure. C’est pourquoi nous sélectionnons Objet blob de blocs. Si vous choisissez un objet blob de pages, vous devez vous assurer que vos données sont de 512 octets alignés. Notez que VHDX est toujours de 512 octets alignés.
   
8. Cette étape varie selon que vous créez un partage SMB ou NFS. 
     
    - **Si vous créez un partage SMB** : dans le champ de l’utilisateur local avec tous les privilèges, choisissez **Créer** ou **Utiliser l’existant**. Si vous créez un nouvel utilisateur local, indiquez un **nom d’utilisateur**, un **mot de passe**, puis **confirmez le mot de passe**. Cela affecte les autorisations à l’utilisateur local. Après avoir attribué les autorisations à cet emplacement, vous pouvez utiliser l’Explorateur de fichiers par la suite pour modifier ces autorisations.
    
        ![Ajouter un partage SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Si vous cochez l’option permettant d’**autoriser uniquement les opérations de lecture** pour ces données de partage, vous aurez la possibilité d’indiquer des utilisateurs en lecture seule.
        
    - **Si vous créez un partage NFS** : vous devez fournir les adresses IP des clients autorisés qui peuvent accéder au partage.

        ![Ajouter un partage NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Cliquez sur **Créer** pour créer le partage. 
    
    Vous êtes averti que le partage est en cours de création. Une fois le partage créé avec les paramètres spécifiés, le panneau **Partages** s’actualise pour refléter le nouveau partage. 
    
    ![Liste des partages mise à jour](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Se connecter au partage

Effectuez ces étapes sur votre client Windows Server connecté à votre appareil Data Box Gateway pour vous connecter aux partages.


1. Ouvrez une fenêtre de commandes.  À l’invite de commandes, tapez :

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Lorsque vous y êtes invité, entrez le mot de passe du partage. L’exemple de sortie de cette commande est présenté ici.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Appuyez sur Windows + R. Dans la fenêtre **Exécuter**, spécifiez `\\<device IP address>`. Cliquez sur **OK**. L’Explorateur de fichiers s’affiche. Vous devriez maintenant voir les partages que vous avez créés sous forme de dossiers. Sélectionnez et double-cliquez sur un partage (dossier) pour afficher son contenu.
 
    ![Se connecter à un partage SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Les données sont écrites dans ces partages lors de leur génération et l’appareil envoie les données vers le cloud.

### <a name="connect-to-an-nfs-share"></a>Se connecter à un partage NFS

Effectuez ces étapes sur votre client Linux connecté à votre Data Box Edge.

1. Vérifiez que le client NFSv4 est installé sur le client. Pour installer le client NFS, utilisez la commande suivante :

   `sudo apt-get install nfs-common`

    Pour plus d’informations, accédez à [Installer le client NFSv4](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Une fois le client NFS installé, utilisez la commande suivante pour monter le partage NFS que vous avez créé sur votre appareil Data Box Gateway :

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Avant de configurer les montages, assurez-vous que les répertoires qui agiront comme points de montage sur votre ordinateur local sont déjà créés et ne contiennent pas de fichiers ou de sous-dossiers.

    L’exemple suivant montre comment se connecter à un partage sur l’appareil Gateway via NFS. L’IP de l’appareil virtuel est `10.10.10.60`, le partage `mylinuxshare2` est monté sur la machine virtuelle ubuntu, le point de montage étant `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Les mises en garde suivantes s’appliquent à la préversion :
> - Une fois un fichier créé dans les partages, vous ne pouvez plus en modifier le nom. 
> - La suppression d’un fichier à partir d’un partage ne supprime pas l’entrée du compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Gateway et avez notamment appris à :

> [!div class="checklist"]
> * Ajouter un partage
> * Vous connecter à un partage


Passez au didacticiel suivant pour découvrir comment administrer votre appareil Data Box Gateway.

> [!div class="nextstepaction"]
> [Utiliser l’interface utilisateur web locale pour administrer un appareil Data Box Gateway](http://aka.ms/dbg-docs)


