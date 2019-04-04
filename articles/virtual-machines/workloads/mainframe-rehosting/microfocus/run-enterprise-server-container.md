---
title: Exécutez Micro Focus Enterprise Server 4.0 dans un conteneur Docker sur des Machines virtuelles
description: Ré-héberger vos charges de travail IBM z/OS mainframe en exécutant Micro Focus Enterprise Server dans un conteneur Docker sur des Machines virtuelles Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896431"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Exécutez Micro Focus Enterprise Server 4.0 dans un conteneur Docker sur Azure

Vous pouvez exécuter Micro Focus Enterprise Server 4.0 dans un conteneur Docker sur Azure. Ce didacticiel vous montre comment. Il utilise la démonstration d’acctdemo Windows CICS (Customer Information contrôle System) pour le serveur d’entreprise.

Docker ajoute la portabilité et isolation aux applications. Par exemple, vous pouvez exporter une image Docker à partir d’une machine virtuelle de Windows pour s’exécuter sur un autre, ou d’un référentiel à un serveur Windows avec Docker. L’image Docker s’exécute dans le nouvel emplacement avec la même configuration, sans avoir à installer le serveur d’entreprise. Il s’agit de la partie de l’image. Considérations relatives aux licences s’appliquent toujours.

Ce didacticiel installe le **Windows 2016 Datacenter avec Containers** machine virtuelle (VM) à partir de la place de marché Azure. Cette machine virtuelle inclut **Docker 18.09.0**. Les étapes ci-dessous vous montrent comment déployer le conteneur, exécutez-le, puis connectez-vous avec un émulateur 3270.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer, consultez ces conditions préalables :

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Le logiciel de Micro Focus et une licence valide (ou licence d’essai). Si vous êtes un client de Micro Focus existant, contactez votre représentant de Micro Focus. Sinon, [demandez un essai](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Les fichiers de démonstration Docker sont inclus avec Enterprise Server 4.0. Ce didacticiel utilise ent\_server\_dockerfiles\_4.0\_Windows.zip ne. Accédez-y à partir de la même place que vous avez accédé le fichier d’installation Enterprise Server, ou accédez à *Micro Focus* pour commencer.

- La documentation de [Enterprise Server et code Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Sécuriser le média à partir de la ent\_server\_dockerfiles\_4.0\_Windows.zip ne fichier. Sécuriser le fichier de licence ES-Docker-Prod-XXXXXXXX.mflic (nécessaire pour créer les images Docker).

2. Créez la machine virtuelle. Pour ce faire, ouvrez le portail, Azure, sélectionnez **créer une ressource** dans le coin supérieur gauche, filtrer par *serveur windows*. Dans les résultats, sélectionnez **Windows Server Datacenter 2016 – avec des conteneurs**.

     ![Résultats de recherche du portail Azure](media/01-portal.png)

3. Pour configurer les propriétés de la machine virtuelle, choisissez Détails de l’instance :

    1. Choisissez une taille de machine virtuelle. Pour ce didacticiel, envisagez d’utiliser un **Standard DS2\_v2** machine virtuelle avec 2 processeurs virtuels et 7 Go de mémoire.

    2. Sélectionnez le **région** et **groupe de ressources** vous souhaitez déployer sur.

    3. Pour **options de disponibilité**, utilisez le paramètre par défaut.

    4. Pour **nom d’utilisateur**, tapez le compte d’administrateur que vous souhaitez utiliser et le mot de passe.

    5. Assurez-vous que **port 3389 RDP** est ouvert. Uniquement ce port doit être exposée publiquement, afin de pouvoir vous connecter à la machine virtuelle. Acceptez toutes les valeurs par défaut, puis cliquez sur **révision + créer**.

     ![Créer un volet de la machine virtuelle](media/container-02.png)

4. Attendez que le déploiement se termine (quelques minutes). Un message indique que votre machine virtuelle a été créée.

5. Cliquez sur **accéder à la ressource** pour accéder à la **vue d’ensemble** Panneau de votre machine virtuelle.

6. Sur la droite, cliquez sur le **Connect** bouton. Le **se connecter à la machine virtuelle** options apparaissent à droite.

7. Cliquez sur le **télécharger le fichier RDP** bouton pour télécharger le fichier RDP qui vous permet d’attacher à la machine virtuelle.

8. Une fois que le fichier de téléchargement est terminé, ouvrez il et tapez le nom d’utilisateur et le mot de passe créé pour la machine virtuelle.

     > [!NOTE]
     > N’utilisez pas vos informations d’identification d’entreprise pour vous connecter. (Le client RDP suppose que vous pouvez souhaiter utiliser. Vous ne procédez pas.)

9.  Sélectionnez **autres choix**, puis sélectionnez vos informations d’identification de la machine virtuelle.

À ce stade, la machine virtuelle est en cours d’exécution et jointe via RDP. Vous êtes connecté et prêt pour l’étape suivante.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Créez un répertoire de bac à sable et de charger le fichier zip

1.  Créez un répertoire sur la machine virtuelle où vous pouvez télécharger les fichiers de démonstration et de licence. Par exemple, **C:\\bac à sable**.

2.  Télécharger **ent\_server\_dockerfiles\_4.0\_Windows.zip ne** et **ES-Docker-Prod-XXXXXXXX.mflic** fichier dans le répertoire que vous avez créé.

3.  Extrayez le contenu du fichier zip pour le **ent\_server\_dockerfiles\_4.0\_windows** répertoire créé par le processus d’extraction. Ce répertoire inclut un fichier readme (en tant que fichier .html et .txt) et deux sous-répertoires, **EnterpriseServer** et **exemples**.

4.  Copie **ES-Docker-Prod-XXXXXXXX.mflic** au lecteur C:\\bac à sable\\ent\_server\_dockerfiles\_4.0\_windows\\ EnterpriseServer et C:\\bac à sable\\ent\_server\_dockerfiles\_4.0\_windows\\exemples\\répertoires CICS.

> [!NOTE]
> Veillez à que copier le fichier de licence pour les deux annuaires. Ils sont requis pour l’étape de génération Docker pour vous assurer que les images disposent des licences adéquates.

## <a name="check-docker-version-and-create-base-image"></a>Vérifier la version de Docker et de créer l’image de base

> [!IMPORTANT]
> Création de l’image Docker approprié est un processus en deux étapes. Commencez par créer l’image de base Enterprise Server 4.0. Puis créez une autre image pour le x64 plateforme. Bien que vous pouvez créer un x86 (32 bits) de l’image, utilisez l’image de 64 bits.

1. Ouvrez une invite de commandes.

2. Vérifiez que Docker est installé.  À l’invite de commandes, tapez :

    ```
        docker version
    ```

     Par exemple, la version a été 18.09.0 lorsque cela a été écrit.

3. Pour modifier le répertoire, tapez **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Type **bld.bat IacceptEULA** pour commencer le processus de génération pour l’image de base initiale. Patientez quelques minutes pour exécuter ce processus. Dans les résultats, notez que les deux images qui ont été créés : un pour x64 et l’autre pour x86 :

     ![Fenêtre de commande affichant des images](media/container-04.png)

5. Pour créer l’image finale pour la démonstration CICS, basculez vers le répertoire CICS en tapant **cd\\bac à sable\\ent\_server\_dockerfiles\_4.0\_windows\\ Exemples\\CICS**.

6. Pour créer l’image, tapez **bld.bat x64**. Patientez quelques minutes pour le processus à exécuter et le message indiquant que l’image a été créée.

7. Type **images docker** pour afficher une liste de toutes les images Docker installés sur la machine virtuelle. Assurez-vous que **microfocus, qui produit/es-acctdemo** est un d’eux.

     ![Fenêtre de commande affichant es-acctdemo image](media/container-05.png)

## <a name="run-the-image"></a>Exécuter l’image 

1.  Pour lancer Enterprise Server 4.0 et l’application acctdemo, à l’invite de commandes, tapez :

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installer un émulateur de terminal 3270 tel que [x3270](http://x3270.bgp.nu/) et l’utiliser pour joindre, via le port 9040, à l’image qui est en cours d’exécution.

3.  Obtenir l’adresse IP du conteneur acctdemo, donc Docker peut agir comme un serveur DHCP pour les conteneurs qu’il gère :

    1.  Obtenir l’ID du conteneur en cours d’exécution. Type **Docker ps** à l’invite de commandes et notez l’ID (**22a0fe3159d0** dans cet exemple). Enregistrez-le pour l’étape suivante.

    2.  Pour obtenir l’adresse IP pour le conteneur acctdemo, utilisez l’ID de conteneur à partir de l’étape précédente comme suit :

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Par exemple : 

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Notez l’adresse IP de l’image acctdemo. Par exemple, l’adresse dans la sortie suivante est 172.19.202.52.

     ![Fenêtre de commande indiquant l’adresse IP](media/container-06.png)

5. Montez l’image à l’aide de l’émulateur. Configurer l’émulateur pour utiliser l’adresse de l’image d’acctdemo et le port 9040. Ici, il a **172.19.202.52:9040**. Vôtre sera similaire. Le **Signon pour CICS** écran s’ouvre.

    ![Authentification à l’écran CICS](media/container-07.png)

6. Connectez-vous à la région CICS en entrant **SYSAD** pour le **USERID** et **SYSAD** pour le **mot de passe**.

7. Effacer l’écran, à l’aide de keymap de l’émulateur. Pour x3270, sélectionnez le **Keymap** option de menu.

8. Pour lancer l’application acctdemo, tapez **ACCT**. L’écran initial de l’application s’affiche.

     ![Écran de démonstration d’un compte](media/container-08.png)

9. Faites des essais avec les types de compte d’affichage. Par exemple, tapez **D** pour la demande et **11111** pour le **compte**. Autres numéros de compte pour essayer sont 22222, 33333 et ainsi de suite.

     ![Écran de démonstration d’un compte](media/container-09.png)

10. Pour afficher la console Administration de serveur d’entreprise, accédez à l’invite de commandes et tapez **démarrer http:172.19.202.52:86**

     ![Console d’Administration de serveur d’entreprise](media/container-010.png)

Et voilà ! Vous êtes maintenant en cours d’exécution et la gestion d’une application CICS dans un conteneur Docker.

## <a name="next-steps"></a>Étapes suivantes

- [Installation de Micro Focus Enterprise Server 4.0 et code Enterprise Developer 4.0 sur Azure](./set-up-micro-focus-azure.md)
- [Migration d’applications d’un environnement mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
