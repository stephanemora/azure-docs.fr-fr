---
title: Configurer une connexion point à site - Azure SQL Database Managed Instance | Microsoft Docs
description: Apprenez à vous connecter à Azure SQL Database Managed Instance à l’aide de SQL Server Management Studio via une connexion point à site à partir d’un ordinateur client local.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova, jovanpop
manager: craigg
ms.date: 09/06/2018
ms.openlocfilehash: fc51d7191deb8242075b28fbb42adc2ff9ae2739
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162999"
---
# <a name="configure-a-point-to-site-connection-to-connect-to-an-azure-sql-database-managed-instance-from-on-premises-computer"></a>Configurer une connexion point à site pour se connecter à Azure SQL Database Managed Instance à partir d’un ordinateur local

Ce guide de démarrage rapide vous montre comment vous connecter à Azure SQL Database Managed Instance à l’aide de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) à partir d’un ordinateur client local via une connexion point à site. Pour plus d’informations sur les connexions point à site, consultez l’article [À propos des VPN point à site](../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Prérequis

Dans ce guide de démarrage rapide :
- Nous utilisons la ressource créée dans le guide de démarrage rapide [Créer une instance gérée](sql-database-managed-instance-get-started.md) comme point de départ.
- PowerShell 5.1 et Azure PowerShell 5.4.2 (ou versions ultérieures) doivent être installés sur votre ordinateur client local.
- La version la plus récente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) doit être installée sur votre ordinateur client local.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Attacher une passerelle VPN à votre réseau virtuel Managed Instance

1. Ouvrez PowerShell sur votre ordinateur client local.
2. Copiez et collez ce script PowerShell. Ce script attache une passerelle VPN au réseau virtuel Managed Instance que vous avez créé dans le guide de démarrage rapide [Créer une instance gérée](sql-database-managed-instance-get-started.md). Le script effectue ces trois opérations :
  - Il crée et installe des certificats sur l’ordinateur client.
  - Il calcule la future plage d’adresses IP du sous-réseau de la passerelle VPN.
  - Il crée le sous-réseau de la passerelle.
  - Il déploie le modèle Azure Resource Manager qui attache la passerelle VPN au sous-réseau VPN.

   ```powershell
   $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

   $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

   Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase 
   ```

3. Indiquez les paramètres obligatoires dans le script PowerShell. Les valeurs des paramètres `<subscriptionId>`, `<resourceGroup>` et `<virtualNetworkName>` doivent correspondre à celles que vous avez utilisées dans le guide de démarrage rapide [Créer une instance gérée](sql-database-managed-instance-get-started.md). Pour le paramètre `<certificateNamePrefix>`, vous pouvez saisir la chaîne de votre choix.

4. Exécutez le script PowerShell.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Créer une connexion VPN à votre instance Managed Instance

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Ouvrez le groupe de ressources dans lequel vous avez créé la passerelle de réseau virtuel, puis ouvrez la ressource de la passerelle de réseau virtuel.

    ![ressource de la passerelle de réseau virtuel](./media/sql-database-managed-instance-configure-p2s/vnet-gateway.png)  

3. Cliquez sur **Configuration de point à site**, puis sur **Télécharger le client VPN**.

    ![Téléchargement du client VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Décompressez le fichier zip, puis ouvrez le dossier extrait. 
5. Accédez au dossier WindowsAmd64, puis ouvrez le fichier **VpnClientSetupAmd64.exe**.
6. Si le message **Windows a protégé votre ordinateur** apparaît, cliquez sur **Informations complémentaires**, puis sur **Exécuter quand même**.

    ![Installer le client VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Dans la boîte de dialogue du contrôle de compte d’utilisateur, cliquez sur **Oui** pour continuer.
8. Dans la boîte de dialogue MyNewVNet, cliquez sur **Oui** pour installer un client VPN pour MyNewVNet.

## <a name="connect-to-the-vpn-connection"></a>Établir la connexion VPN

1. Accédez aux connexions VPN sur votre ordinateur client et cliquez sur **MyNewVNet** pour établir la connexion à ce réseau virtuel.

    ![Connexion VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Cliquez sur **Connecter**.
3. Dans la boîte de dialogue MyNewVNet, cliquez sur **Se connecter**.

    ![Connexion VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Lorsque vous êtes invité à accorder un privilège élevé au gestionnaire de connexions pour mettre à jour votre table d’itinéraires, cliquez sur **Continuer**.
5. Dans la boîte de dialogue du contrôle de compte d’utilisateur, cliquez sur **Oui** pour continuer.

    ![Connexion VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

   Vous avez établi une connexion VPN à votre réseau virtuel Managed Instance.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Utiliser SSMS pour se connecter à Managed Instance

1. Sur l’ordinateur client local, ouvrez SQL Server Management Studio (SSMS).
 
2. Dans la boîte de dialogue **Connexion au serveur**, entrez le **nom d’hôte** complet de votre instance gérée dans la zone **Nom du serveur**, sélectionnez **Authentification SQL Server**, indiquez votre identifiant et votre mot de passe, puis cliquez sur **Se connecter**.

    ![connecter ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Après vous être connecté, vous pouvez voir votre système et vos bases de données utilisateur dans le nœud Bases de données, ainsi que divers objets dans les nœuds Sécurité, Objets serveur, Réplication, Gestion, SQL Server Agent et XEvent Profiler.

## <a name="next-steps"></a>Étapes suivantes

- Pour consulter le guide de démarrage rapide permettant de comprendre comment se connecter à partir d’une machine virtuelle Azure, consultez l’article [Configurer une connexion point à site](sql-database-managed-instance-configure-p2s.md).
- Pour une vue d’ensemble des options de connexion pour les applications, consultez [Connecter vos applications à Managed Instance](sql-database-managed-instance-connect-app.md).
- Pour restaurer une base de données SQL Server locale vers une instance gérée, vous pouvez utiliser [Azure Database Migration Service (DMS) pour la migration](../dms/tutorial-sql-server-to-managed-instance.md) ou la [commande T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) afin d’effectuer la restauration à partir d’un fichier de sauvegarde de base de données.
