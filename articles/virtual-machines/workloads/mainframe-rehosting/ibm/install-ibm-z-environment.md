---
title: Installez IBM zD & environnement de développement/test T sur Azure | Microsoft Docs
description: Déployer IBM Z Development et environnement de Test (zD & T) sur l’infrastructure de Machine virtuelle Azure (VM) en tant que service (IaaS).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894523"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Installez IBM zD & environnement de développement/test T sur Azure

Pour créer un environnement de développement/test pour les charges de travail mainframe sur IBM Z Systems, vous pouvez déployer IBM Z Development et Test Environment (zD & T) sur l’infrastructure de Machine virtuelle Azure (VM) en tant que service (IaaS).

Avec zD & T, vous pouvez tirer parti des économies de la x86 plate-forme pour vos environnements de développement et de test moins critiques, puis à transmettre les mises à jour dans un environnement de production Z système. Pour plus d’informations, consultez le [IBM ZD & T des instructions d’installation](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Azure et Azure Stack prennent en charge les versions suivantes :

- zD & T Personal Edition
- zD & T Parallel Sysplex
- zD & T Enterprise Edition

Toutes les éditions de zD & T exécutent uniquement sur les x86 les systèmes Linux, pas Windows Server. Enterprise Edition est pris en charge sur Red Hat Enterprise Linux (RHEL) ou Ubuntu/Debian. Les images RHEL et machine virtuelle Debian sont disponibles pour Azure.

Cet article vous montre comment configurer zD & T Enterprise Edition sur Azure, vous pouvez donc utiliser le zD & du serveur web T Enterprise Edition pour la création et gestion des environnements. Installation zD & T n’installe pas les environnements. Vous devez créer séparément comme des packages d’installation. Par exemple, Application développeurs contrôlé Distributions (ADCD) sont des images de volume des environnements de test. Elles sont contenues dans les images de zip sur la distribution multimédia disponible à partir d’IBM. Consultez Comment [configurer un environnement ADCD sur Azure](demo.md).

Pour plus d’informations, consultez le [zD & vue d’ensemble de T](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) dans le centre de connaissances d’IBM.

Cet article vous montre comment configurer Z développement et l’environnement de Test (zD & T) Enterprise Edition sur Azure. Ensuite, vous pouvez utiliser le zD & du serveur web T Enterprise Edition pour créer et gérer des environnements Z sur Azure.

## <a name="prerequisites"></a>Conditions préalables

> [!NOTE]
> IBM permet zD & T Enterprise Edition doit être installé dans les environnements de développement/test uniquement :*pas* les environnements de production.

- Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

- Vous devez avoir accès au média, qui est disponible uniquement pour les partenaires et clients d’IBM. Pour plus d’informations, contactez votre représentant IBM ou les informations de contact sur le [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) site Web.

- Un [le serveur de licences](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Cela est nécessaire pour l’accès aux environnements. La manière dont vous le créez dépend de comment vous une licence pour le logiciel à partir d’IBM :

     - **Serveur de licences en fonction du matériel** requiert un périphérique USB qui contient les jetons Rational nécessaires pour accéder à toutes les parties du logiciel. Vous devez l’obtenir à partir d’IBM.

     - **Serveur de licences basé sur logiciel** , vous devez configurer un serveur centralisé pour la gestion des clés de licence. Cette méthode est conseillée et vous oblige à configurer les clés que vous recevez d’IBM dans le serveur d’administration.

## <a name="create-the-base-image-and-connect"></a>Créer l’image de base et de se connecter

1. Dans le portail Azure, [créer une machine virtuelle](/azure/virtual-machines/linux/quick-create-portal) avec la configuration de système d’exploitation. Cet article suppose une machine virtuelle B4ms (avec 4 processeurs virtuels et 16 Go de mémoire) exécutant Ubuntu 16.04.

2. Une fois que la machine virtuelle est créée, ouvrez les ports entrants 22 pour SSH, 21 pour FTP et 9443 pour le serveur web.

3. Obtenir les informations d’identification SSH indiquées sur la **vue d’ensemble** Panneau de la machine virtuelle via le **Connect** bouton. Sélectionnez le **SSH** onglet et copiez la commande d’ouverture de session SSH dans le Presse-papiers.

4. Ouvrez une session sur un [interpréteur de commandes Bash](/azure/cloud-shell/quickstart) à partir de votre PC local et le coller la commande. Il sera sous la forme **ssh\<id utilisateur\>\@\<adresse IP\>**. Lors de l’invité à entrer vos informations d’identification, entrez-les pour établir une connexion à votre répertoire de base.

## <a name="copy-the-installation-file-to-the-server"></a>Copiez le fichier d’installation sur le serveur

Le fichier d’installation pour le serveur web est **ZDT\_installer\_EE\_V12.0.0.1.tgz**. Il est inclus dans le support fourni par IBM. Vous devez télécharger ce fichier à votre VM Ubuntu.

1. À partir de la ligne de commande, entrez la commande suivante pour vous assurer que tout est à jour dans l’image qui vient d’être créée :

    ```
    sudo apt-get update
    ```

2. Créer le répertoire d’installation pour :

    ```
    mkdir ZDT
    ```

3. Copiez le fichier à partir de votre ordinateur local à la machine virtuelle :

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Cette commande copie le fichier d’installation dans le répertoire ZDT dans votre répertoire de base, qui varie selon que votre client s’exécute Windows ou Linux.

## <a name="install-the-enterprise-edition"></a>Installez l’édition entreprise

1. Accédez au répertoire ZDT et décompresser le ZDT\_installer\_EE\_fichier V12.0.0.1.tgz utilisant les commandes suivantes :

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Exécutez le programme d’installation :

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Sélectionnez **1** pour installer le serveur d’entreprise.

4. Appuyez sur **entrée** et lisez attentivement les contrats de licence. À la fin de la licence, entrez **Oui** pour continuer.

5. Lorsque vous êtes invité à modifier le mot de passe pour l’utilisateur nouvellement créé, **ibmsys1**, utilisez la commande **sudo passwd ibmsys1** et entrez le nouveau mot de passe.

6. Pour vérifier si l’installation a réussi Entrez

    ```
    dpkg -l | grep zdtapp
    ```

7. Vérifiez que la sortie contienne la chaîne **zdtapp 12.0.0.0**, qui indique que le gaz package été installé avec succès

### <a name="starting-enterprise-edition"></a>Au départ Enterprise Edition

N’oubliez pas que, lorsque le serveur web démarre, il s’exécute sous l’ID d’utilisateur zD & T qui a été créé pendant le processus d’installation.

1. Pour démarrer le serveur web, utilisez l’ID d’utilisateur racine pour exécuter la commande suivante :

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Copiez la sortie de l’URL par le script, qui ressemble à :

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Collez l’URL dans un navigateur web pour ouvrir le composant de gestion de votre installation de T zD.

## <a name="next-steps"></a>Étapes suivantes

[Valeur d’une Application aux développeurs sous contrôle de Distribution (ADCD) dans IBM zD & T v1](./demo.md)
