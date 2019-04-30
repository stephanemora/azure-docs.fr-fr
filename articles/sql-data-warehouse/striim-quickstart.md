---
title: Bien démarrer avec Striim à l’aide d’Azure SQL Data Warehouse | Microsoft Docs
description: Devenez rapidement opérationnel avec Striim et Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: a835fbd427408afae9ea6ae1b1bc2a84c18b0c34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475170"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Guide d’installation des offres de la Place de marché Striim Azure SQL DW

Ce guide de démarrage rapide part du principe que vous disposez d’une instance préexistante de SQL Data Warehouse.

Recherchez Striim dans la Place de marché Azure, puis sélectionnez Striim pour l’intégration des données sur l’option SQL Data Warehouse (Intermédiaire) 

![Installer Striim][install]

Configurez la machine virtuelle Striim avec des propriétés spécifiées, en notant le nom de cluster, le mot de passe et le mot de passe administrateur Striim

![Configurer Striim][configure]

Une fois déployé, cliquez sur \<nom de machine virtuelle >-masternode dans le portail Azure, cliquez sur se connecter et copier la connexion à l’aide de compte local de machine virtuelle 

![Connecter Striim à SQL Data Warehouse][connect]

Téléchargez le fichier sqljdbc42.jar à partir du site <https://www.microsoft.com/en-us/download/details.aspx?id=54671> sur votre ordinateur local. 

Ouvrez une fenêtre de ligne de commande et accédez au répertoire où vous avez téléchargé le fichier jar JDBC. Copiez via SCP le fichier jar sur votre machine virtuelle Striim, en obtenant l’adresse et le mot de passe à partir du portail Azure

![Copier le fichier jar sur votre machine virtuelle][copy-jar]

Ouvrez une autre fenêtre de ligne de commande, ou utilisez un utilitaire SSH pour vous connecter avec SSH au cluster Striim

![Se connecter avec SSH au cluster][ssh]

Exécutez les commandes suivantes pour déplacer le fichier jar JDBC dans le répertoire lib de Striim, et démarrer et arrêter le serveur.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Démarrer le cluster Striim][start-striim]

À présent, ouvrez votre navigateur préféré et accédez à \<nom DNS > : 9080

![Accéder à l’écran de connexion][navigate]

Connectez-vous avec le nom d’utilisateur et le mot de passe que vous avez configuré dans le portail Azure, puis sélectionnez votre Assistant préféré pour commencer, ou accédez à la page des applications pour commencer à utiliser l’interface utilisateur glisser-déplacer

![Se connecter avec les informations d’identification du serveur][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
