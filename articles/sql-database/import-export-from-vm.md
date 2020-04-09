---
title: Importer ou exporter une base de données SQL
description: Importez ou exportez une base de données Azure SQL sans autoriser les services Azure à accéder au serveur.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529232"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importer ou exporter une base de données Azure SQL sans autoriser les services Azure à accéder au serveur

Cet article explique comment importer ou exporter une base de données Azure SQL quand *Autoriser les services Azure* est défini sur *DÉSACTIVÉ* sur le serveur SQL Azure. Le workflow exécute SqlPackage sur une machine virtuelle Azure pour effectuer l’opération d’importation ou d’exportation.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Créer la machine virtuelle Azure

Créez une machine virtuelle Azure en sélectionnant le bouton **Déployer sur Azure**.

Ce modèle vous permet de déployer une machine virtuelle Windows simple à partir de différentes options de version de Windows intégrant les derniers correctifs. La machine virtuelle ainsi déployée présente une taille A2 à l’emplacement du groupe de ressources et le nom de domaine complet de la machine virtuelle est retourné.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Pour plus d’informations, consultez [Very simple deployment of Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

Les étapes suivantes vous montrent comment vous connecter à votre machine virtuelle à l’aide d’une connexion Bureau à distance.

1. Une fois le déploiement terminé, accédez à la ressource de machine virtuelle.

    ![Machine virtuelle](./media/import-export-from-vm/vm.png)  

2. Sélectionnez **Connecter**.

   Un formulaire de fichier .rdp (Remote Desktop Protocol) s’affiche avec l’adresse IP publique et le numéro de port de la machine virtuelle.

   ![Formulaire RDP](./media/import-export-from-vm/rdp.png)  

3. Sélectionnez **Télécharger le fichier RDP**.

   > [!NOTE]
   > Vous pouvez également utiliser le protocole SSH pour vous connecter à votre machine virtuelle.

4. Fermez le formulaire **Se connecter à la machine virtuelle**.
5. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé.
6. Lorsque vous y êtes invité, sélectionnez **Connexion**. Sur un Mac, vous avez besoin d’un client RDP similaire à ce [Client Bureau à distance](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) disponible sur le Mac App Store.

7. Entrez le nom d’utilisateur et le mot de passe que vous avez spécifiés au moment de créer la machine virtuelle, puis choisissez **OK**.

8. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Choisissez **Oui** ou **Continuer** pour établir la connexion.



## <a name="install-sqlpackage"></a>Installer SqlPackage

[Téléchargez et installez la dernière version de SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Pour plus d’informations, consultez [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Créer une règle de pare-feu pour autoriser la machine virtuelle à accéder au serveur

Ajoutez l’adresse IP publique de la machine virtuelle au pare-feu du serveur SQL Database.

Les étapes suivantes permettent de créer une règle de pare-feu IP au niveau du serveur pour l’adresse IP publique de votre machine virtuelle et d’activer la connectivité à partir de la machine virtuelle.

1. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**. La page de vue d’ensemble de votre base de données s’ouvre. Elle affiche le nom complet du serveur (par exemple, **servername.database.windows.net**) et fournit des options pour poursuivre la configuration.

2. Copiez ce nom de serveur complet pour vous connecter à votre serveur et à ses bases de données.

   ![nom du serveur](./media/sql-database-get-started-portal/server-name.png)

3. Sélectionnez **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur de base de données s’ouvre.

   ![règle de pare-feu IP au niveau du serveur](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Dans la barre d’outils, choisissez **Ajouter une adresse IP de client** pour ajouter l’adresse IP publique de votre machine virtuelle à une nouvelle règle de pare-feu IP au niveau du serveur. Une règle de pare-feu IP au niveau du serveur peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Sélectionnez **Enregistrer**. Une règle de pare-feu IP au niveau du serveur est créée pour l’adresse IP publique de votre machine virtuelle actuelle et ouvre le port 1433 sur le serveur SQL Database.

6. Fermez la page **Paramètres de pare-feu**.



## <a name="export-a-database-using-sqlpackage"></a>Exporter une base de données à l’aide de SqlPackage

Pour exporter une base de données SQL à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consultez [Paramètres et propriétés d’exportation](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). L’utilitaire SqlPackage est fourni avec les dernières versions de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Vous pouvez aussi télécharger la dernière version de [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Nous recommandons l’utilisation de l’utilitaire SqlPackage pour bénéficier de la mise à l’échelle et du niveau de performance dans la plupart des environnements de production. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Cet exemple montre comment exporter une base de données à l’aide de SqlPackage.exe avec l’authentification universelle Active Directory. Utilisez les valeurs propres à votre environnement.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importer une base de données à l’aide de SqlPackage

Pour importer une base de données SQL Server à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), consultez les [paramètres et propriétés d’importation](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage s’appuie sur les dernières versions de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt). Vous pouvez aussi télécharger la dernière version de [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Pour bénéficier d’une mise à l’échelle et de performances optimales, nous vous recommandons d’utiliser SqlPackage dans la plupart des environnements de production au lieu du portail Azure. Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers `BACPAC` (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

La commande SqlPackage ci-dessous importe la base de données **AdventureWorks2017** du stockage local vers un serveur Azure SQL Database. Elle crée une base de données appelée **myMigratedDatabase** avec un niveau de service **Premium** et un objectif de service **P6**. Changez ces valeurs en fonction de votre environnement.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Pour vous connecter à un serveur SQL Database qui gère une base de données unique derrière un pare-feu d’entreprise, le port de ce dernier doit être 1433 ouvert. Pour vous connecter à une instance gérée, vous devez avoir une [connexion point à site](sql-database-managed-instance-configure-p2s.md) ou une connexion Express Route.

Cet exemple montre comment importer une base de données à l’aide de SqlPackage avec l’authentification universelle Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Considérations relatives aux performances

Étant donné que la vitesse d’exportation varie selon divers facteurs (par exemple, la forme des données), il est impossible d’établir une prévision de vitesse. SqlPackage peut prendre beaucoup de temps, surtout pour les bases de données volumineuses.

Pour obtenir le meilleur niveau de performance, vous pouvez essayer les stratégies suivantes :

1. Vérifiez qu’aucune autre charge de travail n’est en cours d’exécution sur la base de données. Créer une copie avant l’exportation peut être la meilleure solution pour faire en sorte qu’aucune autre charge de travail ne s’exécute.
2. Augmentez l’objectif de niveau de service (SLO) de la base de données pour mieux gérer la charge de travail d’exportation (principalement les E/S en lecture). Si la base de données est actuellement de type GP_Gen5_4, un niveau critique pour l’entreprise peut s’avérer utile pour la charge de travail de lecture.
3. Vérifiez qu’il existe des index cluster, en particulier pour les tables volumineuses. 
4. Pour éviter les contraintes réseau, les machines virtuelles doivent se trouver dans la même région que la base de données.
5. Les machines virtuelles doivent être dotées d’un SSD dont la taille autorise la génération d’artefacts temporaires avant le chargement dans le stockage Blob.
6. Les machines virtuelles doivent disposer d’une configuration suffisante en matière de cœurs et de mémoire pour la base de données en question.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Stocker le fichier .BACPAC importé ou exporté

Le fichier .BACPAC peut être stocké dans des [objets blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ou [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Pour obtenir le meilleur niveau de performance, utilisez Azure Files. SqlPackage fonctionne avec le système de fichiers pour pouvoir accéder directement à Azure Files.

Pour réduire les coûts, utilisez des objets blob Azure, qui sont plus économiques qu’un partage de fichiers Azure Premium. Cependant, vous devrez copier le [fichier .BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) entre l’objet blob et le système de fichiers local avant de procéder à l’importation ou à l’exportation. Par conséquent, le processus prendra plus de temps.

Pour charger ou télécharger des fichiers .BACPAC, consultez [Transférer des données avec AzCopy et le stockage Blob](../storage/common/storage-use-azcopy-blobs.md) et [Transférer des données avec AzCopy et le stockage de fichiers](../storage/common/storage-use-azcopy-files.md).

Selon votre environnement, vous devrez peut-être [configurer des pare-feu et des réseaux virtuels dans Stockage Azure](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment vous connecter à une base de données SQL Database importée et comment l’interroger, consultez [Démarrage rapide : Azure SQL Database : Utiliser SQL Server Management Studio pour se connecter et interroger des données](sql-database-connect-query-ssms.md).
- Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Pour obtenir une description de l’ensemble du processus de migration de base de données SQL Server ainsi que des recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers Azure SQL Database](sql-database-single-database-migrate.md).
- Pour savoir comment gérer et partager de façon sécurisée les clés de stockage et les signatures d’accès partagé, consultez le [Guide de sécurité de Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
