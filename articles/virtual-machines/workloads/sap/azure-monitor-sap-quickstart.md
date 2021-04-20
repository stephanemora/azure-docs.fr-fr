---
title: Déployer Azure Monitor pour les solutions SAP avec le portail Azure
description: Déployer Azure Monitor pour les solutions SAP avec le portail Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 096f0425a6893d68341b97c821481fa0adf2f95c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375268"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Déployer Azure Monitor pour les solutions SAP avec le portail Azure

Les ressources de Azure Monitor pour les solutions SAP peuvent être créées via le [portail Azure](https://azure.microsoft.com/features/azure-portal). Cette méthode fournit une interface utilisateur basée sur un navigateur pour déployer Azure Monitor pour les solutions SAP et configurer des fournisseurs.

## <a name="sign-in-to-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au portail Azure sur https://portal.azure.com

## <a name="create-monitoring-resource"></a>Créer des ressources de surveillance

1. Sélectionnez **Solutions Azure Monitor pour solutions SAP** dans la **place de marché Azure**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="L’image montre l’offre de sélection Azure Monitor pour l’offre de solutions SAP à partir de la place de marché Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Sous l’onglet **De base**, indiquez les valeurs requises. Le cas échéant, vous pouvez utiliser un espace de travail Log Analytics existant.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Affichage des options de configuration du portail Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Lorsque vous sélectionnez un réseau virtuel, assurez-vous que les systèmes que vous souhaitez surveiller sont accessibles à partir de ce réseau virtuel. 

   > [!IMPORTANT]
   > Si vous sélectionnez **Partager** pour le partage de données avec Microsoft, nos équipes de support technique peuvent fournir un support supplémentaire.

## <a name="configure-providers"></a>Configurer des fournisseurs

### <a name="sap-hana-provider"></a>Fournisseur SAP HANA 

1. Sélectionnez l’onglet **Fournisseur** pour ajouter les fournisseurs que vous souhaitez configurer. Vous pouvez ajouter plusieurs fournisseurs l’un après l’autre ou les ajouter après avoir déployé la ressource de surveillance. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Affiche l’onglet Fournisseur pour ajouter des fournisseurs supplémentaires à votre Azure Monitor pour solutions SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Sélectionnez **Ajouter un fournisseur** et choisissez **SAP HANA** dans la liste déroulante. 

   > [!IMPORTANT]
   > Assurez-vous qu’un fournisseur SAP HANA est configuré pour le nœud « master » SAP HANA.

3. Entrez l’adresse IP privée du serveur HANA.

4. Entrez le nom du locataire de la base de données que vous voulez utiliser. Vous pouvez choisir n’importe quel locataire. Toutefois, nous vous recommandons d’utiliser **SYSTEMDB**, qui offre un ensemble plus vaste de zones de surveillance. 

5. Entrez le numéro de port SQL associé à votre base de données HANA. Le numéro de port doit être au format **[3]**  +  **[instance#]**  +  **[13]** . Par exemple, 30013. 

6. Entrez le nom d'utilisateur de la base de données à utiliser. Assurez-vous que les rôles de **surveillance** et de **lecture de catalogue** sont attribués à l’utilisateur de la base de données. 

7. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs supplémentaires en fonction des besoins ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Image des options de configuration lors de l’ajout d’informations sur le fournisseur." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Fournisseur de cluster à haute disponibilité (Pacemaker)

1. Sélectionnez **Cluster à haute disponibilité (Pacemaker)** dans la liste déroulante. 

   > [!IMPORTANT]
   > Pour configurer le fournisseur de cluster à haute disponibilité (Pacemaker), assurez-vous que ha_cluster_provider est installé sur chaque nœud. Pour plus d’informations, consultez [Exportateur de cluster à haute disponibilité](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Entrez le point de terminaison Prometheus sous la forme http://IP:9664/metrics. 
 
3. Entrez l’ID système (SID), le nom d'hôte et le nom du cluster.

4. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs supplémentaires en fonction des besoins ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="L’image montre les options relatives au fournisseur de stimulateur de cluster à haute disponibilité Pacemaker." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Fournisseur de système d’exploitation (Linux) 

1. Sélectionnez Système d’exploitation (Linux) dans la liste déroulante. 

>[!IMPORTANT]
> Pour configurer le fournisseur de système d’exploitation (Linux), assurez-vous que la dernière version de Node_Exporter est installée sur chaque hôte (BareMetal ou machine virtuelle) que vous souhaitez analyser. Utilisez ce [lien] (https://prometheus.io/download/#node_exporter) pour trouver la dernière version. Pour plus d’informations,  [Node_Exporter](https://github.com/prometheus/node_exporter).

2. Entrez un nom, qui sera l’identificateur de l’instance BareMetal.
3. Entrez le point de terminaison Node Exporter sous la forme http://IP:9100/metrics.

>[!IMPORTANT]
> Utilisez l’adresse IP privée de l’hôte Linux. Vérifiez que les ressources hôte et AMS se trouvent dans le même réseau virtuel. 

>[!Note]
> Le port du pare-feu « 9100 » doit être ouvert sur l’hôte Linux.
>Si vous utilisez firewall-cmd : firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload Si vous utilisez ufw : ufw allow 9100/tcp ufw reload

>[!Tip]
> Si l’hôte Linux est une machine virtuelle Azure, vérifiez que tous les groupes applicables autorisent le trafic entrant sur le port 9100 à partir de « VirtualNetwork » en tant que source.
 
5. Lorsque vous avez terminé, sélectionnez  **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs supplémentaires en fonction des besoins ou sélectionnez  **Vérifier + créer**  pour terminer le déploiement. 


### <a name="microsoft-sql-server-provider"></a>Fournisseur Microsoft SQL Server

1. Avant d’ajouter le fournisseur Microsoft SQL Server, vous devez exécuter le script suivant dans SQL Server Management Studio pour créer un utilisateur avec les autorisations appropriées nécessaires à la configuration du fournisseur.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Sélectionnez **Ajouter un fournisseur** et choisissez **Microsoft SQL Server** dans la liste déroulante. 

3. Renseignez les champs à l’aide des informations associées à votre Microsoft SQL Server. 

4. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs supplémentaires en fonction des besoins ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="L’image montre des informations relatives à l’ajout du fournisseur Microsoft SQL Server." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="sap-netweaver-provider"></a>Fournisseur SAP NetWeaver

#### <a name="pre-requisites-for-adding-netweaver-provider"></a>Conditions préalables pour l’ajout du fournisseur NetWeaver

Le « Service de démarrage SAP » fournit une série de services, notamment la surveillance du système SAP. Nous utilisons « SAPControl », qui est une interface de service web SOAP qui expose ces fonctionnalités. Cette interface de service web SAPControl fait la distinction entre les méthodes [protégées et non protégées](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv). Pour pouvoir extraire des métriques spécifiques, vous devez ôter la protection de certaines méthodes. Pour ôter la protection des méthodes requises pour la version actuelle, veuillez suivre les étapes ci-dessous pour chaque système SAP : -

1. Ouvrez une connexion à l’interface graphique SAP sur le serveur SAP
2. Connectez-vous à l’aide d’un compte d’administrateur
3. Exécutez la transaction RZ10
4. Sélectionnez le profil approprié (DEFAULT.PFL)
5. Sélectionnez « Maintenance étendue », puis cliquez sur Modifier 
6. Modifiez la valeur du paramètre affecté « service/protectedwebmethods » en « SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList » sur le paramètre recommandé, puis cliquez sur Copier
7. Revenez et sélectionnez Profil->Enregistrer
8. Redémarrez le système pour que le paramètre prenne effet

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Installation du fournisseur NetWeaver sur le portail Azure
1.  Assurez-vous que les étapes préalables ont été effectuées et que le serveur a été redémarré
2.  Sur le portail Azure, sous AMS, sélectionnez Ajouter un fournisseur et choisissez SAP NetWeaver dans la liste déroulante
3.  Entrez le nom d’hôte du système SAP et le sous-domaine (le cas échéant)
4.  Entrez le numéro d’instance correspondant au nom d’hôte entré 
5.  Entrez l’ID système (SID)
6.  Lorsque vous avez terminé, sélectionnez Ajouter un fournisseur
7.  Continuez à ajouter des fournisseurs supplémentaires en fonction des besoins ou sélectionnez Passer en revue + créer pour terminer le déploiement

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Azure Monitor pour solutions SAP](azure-monitor-overview.md)
