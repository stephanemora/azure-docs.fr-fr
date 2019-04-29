---
title: Valeur d’une Application aux développeurs sous contrôle de Distribution (ADCD) dans IBM zD & T v1 | Microsoft Docs
description: Exécutez un environnement de l’environnement de Test (zD & T) et de IBM Z Development sur Machines virtuelles (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: f8af19056a343abdbafcd4ead8b072330cb41fd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438450"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Valeur d’une Application aux développeurs sous contrôle de Distribution (ADCD) dans IBM zD & T v1

Vous pouvez exécuter un environnement de l’environnement de Test (zD & T) et de IBM Z Development sur Machines virtuelles (VM). Cet environnement émule l’architecture IBM Z Series. Il peut héberger une variété de systèmes d’exploitation Z ou installations (également appelées Packages ou les Instances de Z), qui sont accessibles via des faisceaux personnalisés appelés IBM Application développeurs contrôlé Distributions (ADCDs).

Cet article vous montre comment configurer une instance ADCD un zD & l’environnement T sur Azure. ADCDs créer des implémentations de système d’exploitation Z série complètes pour les environnements de développement et tests qui s’exécutent dans zD & T.

Comme zD & T, ADCDs sont disponibles uniquement pour les partenaires et clients d’IBM et sont exclusivement pour le développement et à des fins de tests. Ils ne doivent ne pas être utilisé pour les environnements de production. Nombreux packages d’installation IBM sont disponibles en téléchargement via [Passport parti](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) ou [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Conditions préalables

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Le [zD & environnement T] [ ibm-install-z] précédemment configuré sur Azure. Cet article suppose que vous utilisez la même image de machine virtuelle Ubuntu 16.04 créée précédemment.

- Accès au média ADCD via IBM PartnerWorld ou Passport parti.

- Un [le serveur de licences](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Cela est nécessaire pour exécuter IBM zD & T. La manière dont vous le créez dépend de comment vous une licence pour le logiciel à partir d’IBM :

  - **Serveur de licences en fonction du matériel** requiert un périphérique USB qui contient les jetons Rational nécessaires pour accéder à toutes les parties du logiciel. Vous devez l’obtenir à partir d’IBM.

  - **Serveur de licences basé sur logiciel** , vous devez configurer un serveur centralisé pour la gestion des clés de licence. Cette méthode est conseillée et vous oblige à configurer les clés que vous recevez d’IBM dans le serveur d’administration.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Télécharger les packages d’installation à partir de l’avantage de Passport

Accès au média ADCD est requis. Les étapes ci-dessous vous sont clients d’IBM et que vous pouvez utiliser Passport parti. IBM partenaires peuvent utiliser [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> Cet article part du principe qu’un PC Windows est utilisé pour accéder au portail Azure et téléchargez le média d’IBM. Si vous utilisez un ordinateur de bureau Mac ou Ubuntu, les commandes et les processus pour l’obtention du support IBM peuvent différer légèrement.

1. Ouvrez une session sur [Passport parti](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Sélectionnez **téléchargements de logiciels** et **accès aux supports**.

3. Sélectionnez **nombre offre et accord du programme**, puis cliquez sur **continuer**.

4. Entrez la description de la partie ou le numéro de référence, puis cliquez sur **Finder**.

5. Si vous le souhaitez, cliquez sur la liste par ordre alphabétique pour afficher et consulter LEPRODUIT par nom.

6. Sélectionnez **tous les systèmes d’exploitation** dans le **champ système d’exploitation**, et **toutes les langues** dans le **champ langues**. Ensuite, cliquez sur **accédez**.

7. Cliquez sur **sélectionner des fichiers individuels** pour développer la liste et afficher le média individuel pour le téléchargement.

8. Vérifiez que l’ou les packages que vous souhaitez télécharger, sélectionnez **télécharger**et puis télécharger les fichiers dans le répertoire souhaité.

## <a name="upload-the-adcd-packages"></a>Télécharger les packages ADCD

Maintenant que vous avez l’ou les packages, vous devez les télécharger sur votre machine virtuelle dans Azure.

1. Dans le portail Azure, lancer un **ssh** session avec le VM Ubuntu que vous avez créé. Accédez à votre machine virtuelle, sélectionnez le **vue d’ensemble** panneau, puis sélectionnez **Connect**.

2. Sélectionnez le **SSH** onglet, puis copiez la commande ssh dans le Presse-papiers.

3. Ouvrez une session sur votre machine virtuelle à l’aide de vos informations d’identification et la [client SSH](/azure/virtual-machines/linux/use-remote-desktop) de choix. Cette démonstration utilise les extensions Linux pour Windows 10, qui ajoute un interpréteur de commandes bash à l’invite de commandes Windows. PuTTY fonctionne aussi bien.

4. Une fois connecté, créez un répertoire pour télécharger les packages d’IBM. N’oubliez pas Linux respecte la casse. Par exemple, cette démonstration suppose que les packages sont téléchargés sur :

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Télécharger les fichiers à l’aide d’un client SSH tel que[WinSCP](https://winscp.net/eng/index.php). Étant donné que SCP est une partie du protocole SSH, il utilise le port 22, qui est utilisé par SSH. Si votre ordinateur local n’est pas Windows, vous pouvez taper le [commande scp](http://man7.org/linux/man-pages/man1/scp.1.html) dans votre session SSH.

6. Lancer le téléchargement vers le répertoire de machine virtuelle Azure que vous avez créé, qui devient le stockage des images pour zD & T.

    > [!NOTE]
    > Assurez-vous que l’option **ADCDTOOLS. XML** est inclus dans le téléchargement pour le **accueil/monIdUtilisateur/ZDT/adcd/nov2017** directory. Vous en aurez besoin ultérieurement.

7. Attendez que les fichiers à télécharger, ce qui peuvent prendre un certain temps en fonction de votre connexion à Azure.

8. Lorsque les téléchargements sont terminées, accédez au répertoire de volumes et décompresser tous les **gz** volumes :

    ```
        gunzip \*.gz
    ```
    
![Affichage de l’Explorateur de fichiers décompressés gz volumes](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Configurer le stockage des images

L’étape suivante consiste à configurer zD & T pour utiliser l’ou les packages téléchargé. Le processus de stockage d’image dans zD & T vous permet de monter et utiliser les images. Il peut utiliser SSH ou FTP.

1. Démarrer le **zDTServer**. Pour ce faire, vous devez être au niveau racine. Entrez les deux commandes suivantes dans l’ordre :
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Notez la sortie de l’URL par la commande et utiliser cette URL pour accéder au serveur web. Il ressemble à :
     > https://(Your VM Name or IP Address):9443/ZDTMC/index.HTML
     >
     > N’oubliez pas votre accès web utilise le port 9443. Utilisez cela pour vous connecter au serveur web. Est l’ID d’utilisateur pour ZD & T **zdtadmin** et le mot de passe est **mot de passe**.

    ![IBM zD & écran d’accueil de T Enterprise Edition](media/02-welcome.png)

3. Sur le **démarrage rapide** page sous **configurer**, sélectionnez **stockage des images**.

     ![IBM zD & d’écran de démarrage rapide de T Enterprise Edition](media/03-quickstart.png)

4. Sur le **configurer le stockage des images** page, sélectionnez **SSH File Transfer Protocol**.

5. Pour **nom d’hôte**, type **Localhost** et entrez le chemin de répertoire où vous avez chargé les images. Par exemple, /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Entrez le **ID utilisateur** et **mot de passe** pour la machine virtuelle. N’utilisez pas le ZD & T ID et le mot de passe.

7. Tester la connexion pour vous assurer que vous avez accès, puis sélectionnez **enregistrer** pour enregistrer la configuration.

## <a name="configure-the-target-environments"></a>Configurer les environnements cibles

L’étape suivante consiste à configurer le zD & environnement cible de T. Cet environnement hébergé émulé est où vos images de s’exécuter.

1. Sur le **démarrage rapide** page sous **configurer**, sélectionnez **environnements cibles**.

2. Sur le **configurer des environnements cibles** page, sélectionnez **Add Target**.

3. Sélectionnez **Linux**. IBM prend en charge deux types d’environnements, Linux et Cloud(OpenStack), mais cette démonstration s’exécute sur Linux.

4. Sur le **ajouter un environnement cible** page, pour **nom d’hôte**, entrez **localhost**. Conserver **port SSH** définie sur **22**.

5. Dans le **étiquette de l’environnement cible** , entrez une étiquette comme **MyCICS.**

     ![Ajouter l’écran d’environnement cible](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Configurer ADCD et déployer

Après avoir effectué les étapes de configuration précédentes, vous devez configurer zD & T pour utiliser l’environnement ou les packages et la cible. Là encore, vous utilisez le processus de stockage d’image dans zD & T, qui vous permet de monter et utiliser les images. Il peut utiliser SSH ou FTP.

1. Sur le **démarrage rapide** page sous **configurer**, sélectionnez **ADCD**. Un ensemble d’instructions qui s’affichent, vous indiquant les étapes qui doivent être effectuées avant un package ADCD peut être monté. Ceci explique pourquoi nous avons nommé le répertoire cible, comme nous l’avons précédemment.

2. En supposant que toutes les images ont été chargés dans les répertoires appropriés, cliquez sur le **IMAGE à partir de ADCD** lien affiché dans le coin inférieur droit (indiqué à l’étape 7 dans la capture d’écran suivante).

     ![IBM zD & T Enterprise Edition - écran de ADCD configurer](media/05-adcd.png)

## <a name="create-the-image"></a>Création de l’image

Lorsque l’étape précédente de la configuration est terminée, le **créer une image à l’aide de composants de ADCD** page s’affiche.

1. Sélectionnez le volume (novembre 2017 dans ce cas) pour afficher les différents packages qui se trouvent dans ce volume.

2. Pour cette démonstration, sélectionnez **informations contrôle CICS (Customer System) - 5.3**.

3. Dans le **nom de l’Image** , tapez un nom pour l’image comme **MyCICS Image**.

4. Sélectionnez le **créer une Image** bouton en bas à droite.

     ![IBM zD & T Enterprise Edition - créer une image à l’aide d’écran des composants ADCD](media/06-adcd.png)

5. Dans la fenêtre qui s’affiche, qui vous indique que l’image a été déployée avec succès, choisissez **déployer des images**.

6. Sur le **déployer une image à un environnement cible** page, sélectionnez l’image que vous avez créé dans la page précédente (**MyCICS Image**) et l’environnement cible créé précédemment (**MyCICS**).

7. Dans l’écran suivant, indiquez vos informations d’identification pour la machine virtuelle (autrement dit, pas les ztadmin informations d’identification).

8. Dans le volet Propriétés, entrez le nombre de **centrales processeurs (CPs)**, la quantité de **mémoire système (en Go)** et le **répertoire de déploiement** pour l’image en cours d’exécution. Dans la mesure où il s’agit d’une démonstration, gardez-le petit.

9. Assurez-vous que la case pour **déployer automatiquement le problème IPL commande pour z/OS après**.

     ![Écran des propriétés](media/07-properties.png)

10. Sélectionnez **complète**.

11. Sélectionnez **déployer une Image** à partir de la **déployer une image à un environnement cible** page.

Votre image peut désormais déployer et est prêt à être monté par un émulateur de terminal 3270.

> [!NOTE]
> Si vous recevez une erreur indiquant que vous n’avez pas suffisamment d’espace disque, notez que la région nécessite 151 go.

Félicitations ! Vous exécutez à présent un environnement de macroordinateurs IBM sur Azure.

## <a name="learn-more"></a>En savoir plus

- [Migration mainframe : mythes et les réalités](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [PureScale IBM DB2 sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [Dépannage](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Démystification de macroordinateur pour la migration vers Azure](https://azure.microsoft.com/en-us/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
