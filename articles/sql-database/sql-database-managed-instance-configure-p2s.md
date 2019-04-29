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
ms.reviewer: sstein, carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 9ca9506ad9ca826349b2815e504e9ba8bf976faa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702025"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Démarrage rapide : Configurer une connexion point à site à Azure SQL Database Managed Instance à partir d’un emplacement local

Ce guide de démarrage rapide vous montre comment vous connecter à Azure SQL Database Managed Instance à l’aide de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) à partir d’un ordinateur client local via une connexion point à site. Pour plus d’informations sur les connexions point à site, consultez l’article [À propos des VPN point à site](../vpn-gateway/point-to-site-about.md).

## <a name="prerequisites"></a>Conditions préalables

Dans ce guide de démarrage rapide :

- Utilise les ressources créées dans [Créer une instance gérée](sql-database-managed-instance-get-started.md) comme point de départ.
- Requiert PowerShell 5.1 et AZ PowerShell 1.4.0 ou version ultérieure sur votre ordinateur de client local. Au besoin, consultez les instructions d’[installation du module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- La version la plus récente de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) doit être installée sur votre ordinateur client local.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Attacher une passerelle VPN à votre réseau virtuel Managed Instance

1. Ouvrez PowerShell sur votre ordinateur de client local.

2. Copiez ce script PowerShell. Ce script attache une passerelle VPN au réseau virtuel Managed Instance que vous avez créé dans le guide de démarrage rapide [Créer une instance gérée](sql-database-managed-instance-get-started.md). Ce script effectue les opérations suivantes :

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

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Pour utiliser le module Azure PowerShell Resource Manager plutôt que le module Az, utilisez l’applet de commande suivante : `attachVPNGateway.ps1` plutôt que `attachVPNGatewayAz.ps1` applet de commande.

3. Collez le script dans votre fenêtre PowerShell et indiquez les paramètres obligatoires. Les valeurs des paramètres `<subscriptionId>`, `<resourceGroup>` et `<virtualNetworkName>` doivent correspondre à celles que vous avez utilisées dans le guide de démarrage rapide [Créer une instance gérée](sql-database-managed-instance-get-started.md). Pour le paramètre `<certificateNamePrefix>`, vous pouvez saisir la chaîne de votre choix.

4. Exécutez le script PowerShell.

> [!IMPORTANT]
> Ne continuez pas jusqu'à ce que le script PowerShell terminé.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Créer une connexion VPN à votre instance Managed Instance

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Ouvrez le groupe de ressources dans lequel vous avez créé la passerelle de réseau virtuel, puis ouvrez la ressource de la passerelle de réseau virtuel.
3. Sélectionnez **Configuration de point à site**, puis **Télécharger le client VPN**.

    ![Téléchargement du client VPN](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Sur votre ordinateur client local, extrayez les fichiers à partir du fichier zip, puis ouvrez le dossier avec les fichiers extraits.
5. Ouvrez le «**WindowsAmd64** dossier et ouvrez le **VpnClientSetupAmd64.exe** fichier.
6. Si le message **Windows a protégé votre ordinateur** apparaît, cliquez sur **Informations complémentaires**, puis sur **Exécuter quand même**.

    ![Installer le client VPN](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Dans la boîte de dialogue contrôle de compte d’utilisateur, cliquez sur **Oui** pour continuer.
8. Dans la boîte de dialogue faisant référence à votre réseau virtuel, sélectionnez **Oui** pour installer le Client VPN pour votre réseau virtuel.

## <a name="connect-to-the-vpn-connection"></a>Établir la connexion VPN

1. Accédez à **VPN** dans **réseau & Internet** sur votre ordinateur de client sur site et sélectionnez votre réseau virtuel Managed Instance pour établir une connexion à ce réseau virtuel. Dans l’image suivante, le réseau virtuel est nommé **MyNewVNet**.

    ![Connexion VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Sélectionnez **Connecter**.
3. Dans la boîte de dialogue, sélectionnez **Connecter**.

    ![Connexion VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Lorsque vous êtes invité à accorder un privilège élevé au gestionnaire de connexions pour mettre à jour votre table d’itinéraires, choisissez **Continuer**.
5. Sélectionnez **Oui** dans la boîte de dialogue du contrôle de compte d’utilisateur pour continuer.

   Vous avez établi une connexion VPN à votre réseau virtuel Managed Instance.

    ![Connexion VPN](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Utiliser SSMS pour se connecter à l’instance managée

1. Sur l’ordinateur client local, ouvrez SQL Server Management Studio (SSMS).
2. Dans la boîte de dialogue **Se connecter au serveur**, entrez le **nom d’hôte** complet de votre instance managée dans la zone **Nom du serveur**.
3. Sélectionnez **Authentification SQL Server**, fournissez votre nom d’utilisateur et votre mot de passe, puis sélectionnez **Connecter**.

    ![connecter ssms](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Une fois que vous êtes connecté, vous pouvez afficher vos bases de données système et utilisateur dans le nœud Bases de données. Vous pouvez également afficher les différents objets des nœuds Sécurité, Objets serveur, Réplication, Gestion, SQL Server Agent et XEvent Profiler.

## <a name="next-steps"></a>Étapes suivantes

- Pour consulter le guide de démarrage rapide permettant de comprendre comment se connecter à partir d’une machine virtuelle Azure, consultez l’article [Configurer une connexion point à site](sql-database-managed-instance-configure-p2s.md).
- Pour obtenir une vue d’ensemble des options de connexion pour les applications, consultez [Connecter vos applications à Managed Instance](sql-database-managed-instance-connect-app.md).
- Pour restaurer une base de données SQL Server locale existante dans une instance managée, vous pouvez utiliser [Azure Database Migration Service (DMS) pour la migration](../dms/tutorial-sql-server-to-managed-instance.md) ou la [commande T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) pour effectuer la restauration à partir d’un fichier de sauvegarde de base de données.
