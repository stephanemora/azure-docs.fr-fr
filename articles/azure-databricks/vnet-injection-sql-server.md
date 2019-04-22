---
title: Interroger un conteneur Docker SQL Server Linux dans un réseau virtuel à partir d’un bloc-notes Azure Databricks
description: Cet article décrit comment déployer Azure Databricks à votre réseau virtuel, également appelé l’injection de réseau virtuel.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288950"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Didacticiel : Interroger un conteneur Docker SQL Server Linux dans un réseau virtuel à partir d’un bloc-notes Azure Databricks

Ce didacticiel vous apprend à intégrer Azure Databricks avec un conteneur Docker SQL Server Linux dans un réseau virtuel. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer un espace de travail Azure Databricks à un réseau virtuel
> * Installer une machine virtuelle Linux dans un réseau public
> * Installation de Docker
> * Installer Microsoft SQL Server sur le conteneur de docker Linux
> * Interroger le serveur SQL à l’aide de JDBC à partir d’une instance Databricks notebook

## <a name="prerequisites"></a>Conditions préalables

* Créer un [espace de travail Databricks dans un réseau virtuel](quickstart-create-databricks-workspace-vnet-injection.md).

* Installer [Ubuntu pour Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Téléchargez [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="create-a-linux-virtual-machine"></a>Créer une machine virtuelle Linux

1. Dans le portail Azure, sélectionnez l’icône de **Machines virtuelles**. Ensuite, sélectionnez **+ ajouter**.

    ![Ajouter la nouvelle machine virtuelle Azure](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Sur le **notions de base** onglet, cliquez sur Ubuntu Server 16.04 LTS. Modifier la taille de machine virtuelle à B1ms, ce qui a un processeurs virtuels et 2 Go de RAM. La configuration minimale requise pour un conteneur Docker Linux SQL Server est de 2 Go. Choisissez un administrateur de nom d’utilisateur et mot de passe.

    ![Onglet Basics de la nouvelle configuration de machine virtuelle](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Accédez à la **mise en réseau** onglet. Choisissez le réseau virtuel et le sous-réseau public qui inclut votre cluster Azure Databricks. Sélectionnez **révision + créer**, puis **créer** pour déployer la machine virtuelle.

    ![Onglet mise en réseau de configuration d’ordinateur virtuel](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Lorsque le déploiement est terminé, accédez à la machine virtuelle. Notez l’adresse IP publique et du réseau/sous-réseau virtuel dans le **vue d’ensemble**. Sélectionnez le **adresse IP publique**

    ![Vue d’ensemble de la machine virtuelle](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Modifier le **attribution** à **statique** et entrez un **nom DNS**. Sélectionnez **enregistrer**et redémarrez la machine virtuelle.

    ![Configuration d’adresse IP publique](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Sélectionnez le **mise en réseau** onglet sous **paramètres**. Notez que le groupe de sécurité réseau qui a été créé pendant le déploiement d’Azure Databricks est associé à la machine virtuelle. Sélectionnez **ajouter une règle de port entrant**.

7. Ajouter une règle pour ouvrir le port 22 pour SSH. Utilisez les paramètres suivants :
    
    |Paramètre|Valeur suggérée|Description|
    |-------|---------------|-----------|
    |Source|Adresses IP|Adresses IP Spécifie que le trafic entrant à partir d’une source spécifique, adresse IP sera autorisée ou refusée par cette règle.|
    |Adresses IP sources|< votre adresse ip publique\>|Entrez le votre adresse IP publique. Vous pouvez trouver votre adresse IP publique en vous rendant sur [bing.com](https://www.bing.com/) et en recherchant **« mon adresse IP »**.|
    |Plages de ports source|*|Autoriser le trafic à partir de n’importe quel port.|
    |Destination|Adresses IP|Adresses IP Spécifie que le trafic sortant d’une source spécifique, adresse IP sera autorisée ou refusée par cette règle.|
    |Adresses IP de destination|< votre adresse ip publique de machine virtuelle\>|Entrez l’adresse IP de votre machine virtuelle. Vous pouvez le trouver sur le **vue d’ensemble** page de votre machine virtuelle.|
    |Plages de ports de destination|22|Ouvrir le port 22 pour SSH.|
    |Priorité|290|Donnez à la règle de priorité.|
    |Nom|ssh-databricks-tutorial-vm|Nommez la règle.|


    ![Ajouter une règle de sécurité de trafic entrant pour le port 22](./media/vnet-injection-sql-server/open-port.png)

8. Ajouter une règle pour ouvrir le port 1433 pour SQL avec les paramètres suivants :

    |Paramètre|Valeur suggérée|Description|
    |-------|---------------|-----------|
    |Source|Adresses IP|Adresses IP Spécifie que le trafic entrant à partir d’une source spécifique, adresse IP sera autorisée ou refusée par cette règle.|
    |Adresses IP sources|10.179.0.0/16|Entrez la plage d’adresses pour votre réseau virtuel.|
    |Plages de ports source|*|Autoriser le trafic à partir de n’importe quel port.|
    |Destination|Adresses IP|Adresses IP Spécifie que le trafic sortant d’une source spécifique, adresse IP sera autorisée ou refusée par cette règle.|
    |Adresses IP de destination|< votre adresse ip publique de machine virtuelle\>|Entrez l’adresse IP de votre machine virtuelle. Vous pouvez le trouver sur le **vue d’ensemble** page de votre machine virtuelle.|
    |Plages de ports de destination|1433|Ouvrir le port 22 pour SQL Server.|
    |Priorité|300|Donnez à la règle de priorité.|
    |Nom|sql-databricks-tutorial-vm|Nommez la règle.|

    ![Ajouter une règle de sécurité de trafic entrant pour le port 1433](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Exécutez SQL Server dans un conteneur Docker

1. Ouvrez [Ubuntu pour Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), ou tout autre outil qui vous permettra d’utiliser SSH à la machine virtuelle. Accédez à votre machine virtuelle dans le portail Azure et sélectionnez **Connect** pour obtenir la commande SSH que vous devez vous connecter.

    ![Connexion à la machine virtuelle](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Entrez la commande dans votre terminal Ubuntu et le mot de passe administrateur que vous avez créé lors de la configuration de la machine virtuelle.

    ![Ubuntu terminal SSH connectez-vous](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Utilisez la commande suivante pour installer Docker sur la machine virtuelle.

    ```bash
    sudo apt-get install docker.io
    ```

    Vérifier l’installation de Docker avec la commande suivante :

    ```bash
    sudo docker --version
    ```

4. Installez l’image.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Vérifiez les images.

    ```bash
    sudo docker images
    ```

5. Exécutez le conteneur à partir de l’image.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Vérifiez que le conteneur est en cours d’exécution.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Créer une base de données SQL

1. Ouvrez SQL Server Management Studio et connectez-vous au serveur à l’aide du nom du serveur et l’authentification SQL. Le nom d’utilisateur de connexion est **SA** et le mot de passe est le mot de passe défini dans la commande Docker. Le mot de passe dans l’exemple de commande `Password1234`.

    ![Se connecter à SQL Server à l’aide de SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Une fois que vous avez correctement connecté, sélectionnez **nouvelle requête** et entrez l’extrait de code suivant pour créer une base de données, une table et insérer des enregistrements dans la table.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Requête pour créer une base de données SQL Server](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Requête SQL Server à partir d’Azure Databricks

1. Accédez à votre espace de travail Azure Databricks et vérifiez que vous avez créé un cluster dans le cadre des conditions préalables. Ensuite, sélectionnez **créer un bloc-notes**. Nommez le bloc-notes, sélectionnez *Python* comme langage, puis sélectionnez le cluster que vous avez créé.

    ![Nouveaux paramètres de bloc-notes Databricks](./media/vnet-injection-sql-server/create-notebook.png)

2. Utilisez la commande suivante à la commande ping l’adresse IP interne de la machine virtuelle SQL Server. Cette commande ping doit réussir. Si ce n’est pas le cas, vérifiez que le conteneur est en cours d’exécution et passez en revue la configuration de groupe de sécurité réseau.

    ```python
    %sh
    ping 10.179.64.4
    ```

    Vous pouvez également utiliser la commande nslookup pour passer en revue.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Une fois que vous avez ping avec succès le serveur SQL Server, vous pouvez interroger la base de données et les tables. Exécuter du code python suivant :

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, l’espace de travail Azure Databricks et toutes les ressources associées. La suppression du travail évite la facturation inutile. Si vous avez l’intention d’utiliser l’espace de travail Azure Databricks à l’avenir, vous pouvez arrêter le cluster et redémarrer ultérieurement. Si vous ne souhaitez pas continuer à utiliser cet espace de travail Azure Databricks, supprimez toutes les ressources que vous avez créé dans ce didacticiel en procédant comme suit :

1. Dans le menu de gauche dans le portail Azure, cliquez sur **groupes de ressources** puis cliquez sur le nom du groupe de ressources que vous avez créé.

2. Sur la page de votre groupe de ressources, sélectionnez **supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis sélectionnez **supprimer** à nouveau.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à extraire, transformer et charger des données à l’aide d’Azure Databricks.
> [!div class="nextstepaction"]
> [Tutoriel : Extraire, transformer et charger des données à l'aide d'Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
