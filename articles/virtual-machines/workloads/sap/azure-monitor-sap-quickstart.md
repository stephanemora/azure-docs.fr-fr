---
title: Déploiement d’Azure Monitor pour SAP Solutions avec le Portail Azure
description: Découvrez comment utiliser une méthode par navigateur pour déployer Azure Monitor pour SAP Solutions.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 0f5b2c2d94a2b0e106bf0541e080cfa9d05b45ac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441131"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Déploiement d’Azure Monitor pour SAP Solutions avec le Portail Azure

Dans cet article, nous allons voir comment déployer Azure Monitor pour SAP Solutions sur le [Portail Azure](https://azure.microsoft.com/features/azure-portal). À l’aide de l’interface basée sur le navigateur du portail, nous allons déployer Azure Monitor pour SAP Solutions et configurer des fournisseurs.

## <a name="sign-in-to-the-portal"></a>Se connecter au portail

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-monitoring-resource"></a>Création d’une ressource de monitoring

1. Sous **Place de marché**, sélectionnez **Azure Monitor pour SAP Solutions**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Capture d’écran montrant la sélection de l’offre Azure Monitor pour SAP Solutions sur la Place de marché Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Dans l’onglet **De base**, indiquez les valeurs requises. Le cas échéant, vous pouvez utiliser un espace de travail Log Analytics existant.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Capture d’écran montrant les options de configuration de l’onglet De base." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   Lorsque vous sélectionnez un réseau virtuel, assurez-vous que les systèmes dont vous souhaitez effectuer le monitoring y sont accessibles. 

   > [!IMPORTANT]
   > Si vous sélectionnez **Partager** pour **Partager des données avec le Support Microsoft**, nos équipes de support pourront vous aider à résoudre les problèmes.

## <a name="configure-providers"></a>Configurer des fournisseurs

### <a name="sap-netweaver-provider"></a>Fournisseur SAP NetWeaver

Le service de démarrage SAP propose différents services, notamment le monitoring du système SAP. Nous utilisons SAPControl, une interface de services web SOAP qui expose ces fonctionnalités. Elle fait la distinction entre les méthodes de services web [protégées et non protégées](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv). 

Pour extraire des métriques spécifiques, vous devez ôter la protection de certaines méthodes pour la version actuelle. Suivez ces étapes pour chaque système SAP :

1. Ouvrez une connexion GUI SAP au serveur SAP.
2. Connectez-vous avec un compte d’administration.
3. Exécutez la transaction RZ10.
4. Sélectionnez le profil approprié (*DEFAULT.PFL*).
5. Sélectionnez **Maintenance étendue** > **Modifier**. 
6. Sélectionnez le paramètre de profil « service/protectedwebmethods », modifiez-le pour qu’il ait la valeur suivante, puis cliquez sur Copier :  

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate ( replace the hostname , instance number and method name ) leverage the below powershell script 

```Powershell command to test unprotect method 
$SAPHostName = "<hostname>"
$InstanceNumber = "<instancenumber>"
$Function = "ABAPGetWPTable"
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
$sapcntrluri = "https://&quot; + $SAPHostName + &quot;:5&quot; + $InstanceNumber + &quot;14/?wsdl"
$sapcntrl = New-WebServiceProxy -uri $sapcntrluri -namespace WebServiceProxy -class sapcntrl
$FunctionObject = New-Object ($sapcntrl.GetType().NameSpace + ".$Function")
$sapcntrl.$Function($FunctionObject)

11. **Repeat Steps 3-10 for each instance profile **.

>[!Important] 
>It is critical that the sapstartsrv service is restarted on each instance of the SAP system for the SAPControl web methods to be unprotected.  These read-only SOAP API are required for the NetWeaver provider to fetch metric data from the SAP System and failure to unprotect these methods will lead to empty or missing visualizations on the NetWeaver metric workbook.
   
>[!Tip]
> Use an access control list (ACL) to filter the access to a server port. For more information, see [this SAP note](https://launchpad.support.sap.com/#/notes/1495075).

To install the NetWeaver provider on the Azure portal:

1. Make sure you've completed the earlier prerequisite steps and that the server has been restarted.
1. On the Azure portal, under **Azure Monitor for SAP Solutions**, select **Add provider**, and then:

   1. For **Type**, select **SAP NetWeaver**.

   1. For **Hostname**, enter the host name of the SAP system.

   1. For **Subdomain**, enter a subdomain if one applies.

   1. For **Instance No**, enter the instance number that corresponds to the host name you entered. 

   1. For **SID**, enter the system ID.
   
   ![Screenshot showing the configuration options for adding a SAP NetWeaver provider.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

>[!Important]
>If the SAP application servers (ie. virtual machines) are part of a network domain, such as one managed by Azure Active Directory, then it is critical that the corresponding subdomain is provided in the Subdomain text box.  The Azure Monitor for SAP collector VM that exists inside the Virtual Network is not joined to the domain and as such will not be able to resolve the hostname of instances inside the SAP system unless the hostname is a fully qualified domain name.  Failure to provide this will result in missing / incomplete visualizations in the NetWeaver workbook.
 
>For example, if the hostname of the SAP system has a fully qualified domain name of "myhost.mycompany.global.corp" then please enter a Hostname of "myhost" and provide a Subdomain of "mycompany.global.corp".  When the NetWeaver provider invokes the GetSystemInstanceList API on the SAP system, SAP returns the hostnames of all instances in the system.  The collector VM will use this list to make additional API calls to fetch metrics specific to each instance's features (e.g.  ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc…). If specified, the collector VM will then use the subdomain  "mycompany.global.corp" to build the fully qualified domain name of each instance in the SAP system.  
 
>Please DO NOT specify an IP Address for the hostname field if the SAP system is a part of network domain.

   
### SAP HANA provider 

1. Select the **Providers** tab to add the providers you want to configure. You can add multiple providers one after another, or add them after you deploy the monitoring resource. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot showing the tab where you add providers." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Select **Add provider**, and then:

   1. For **Type**, select **SAP HANA**. 

      > [!IMPORTANT]
      > Ensure that a SAP HANA provider is configured for the SAP HANA `master` node.

   1. For **IP address**, enter the private IP address for the HANA server.

   1. For **Database tenant**, enter the name of the tenant you want to use. You can choose any tenant, but we recommend using **SYSTEMDB** because it enables a wider array of monitoring areas. 

   1. For **SQL port**, enter the port number associated with your HANA database. It should be in the format of *[3]* + *[instance#]* + *[13]*. An example is **30013**. 

   1. For **Database username**, enter the username you want to use. Ensure the database user has the *monitoring* and *catalog read* roles assigned.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot showing configuration options for adding an SAP HANA provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

   
### Microsoft SQL Server provider

1. Before you add the Microsoft SQL Server provider, run the following script in SQL Server Management Studio to create a user with the appropriate permissions for configuring the provider.

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

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

   1. Pour **Type**, sélectionnez **Microsoft SQL Server**. 

   1. Renseignez les champs restants à l’aide des informations associées à votre instance SQL Server. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Capture d’écran montrant les options de configuration possibles pour ajouter un fournisseur Microsoft SQL Server." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

1. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

### <a name="high-availability-cluster-pacemaker-provider"></a>Fournisseur de cluster à haute disponibilité (Pacemaker)

Avant d’ajouter des fournisseurs pour les clusters haute disponibilité (Pacemaker), installez l’agent correspondant à votre environnement.

Pour les clusters **SUSE**, ha_cluster_provider doit être installé sur chaque nœud. Découvrez comment installer [l’exportateur de clusters haute disponibilité](https://github.com/ClusterLabs/ha_cluster_exporter#installation). Versions de SUSE prises en charge : SLES pour SAP 12 SP3 et versions ultérieures.  
   
Pour les clusters **RHEL**, Performance Co-Pilot (PCP) et le sous-package pcp-pmda-hacluster doivent être installés sur chaque nœud. Découvrez comment installer [l’agent PCP HACLUSTER] (https://access.redhat.com/articles/6139852). Versions de RHEL prises en charge : 8.2, 8.4 et versions ultérieures.
 
Une fois que vous avez effectué l’installation des composants requis, créez un fournisseur pour chaque nœud de cluster.

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

1. Pour **Type**, sélectionnez **Cluster haute disponibilité (Pacemaker)** . 
   
1. Configurez les fournisseurs pour chaque nœud de cluster en entrant l’URL du point de terminaison dans **Point de terminaison de l’exportateur de clusters haute disponibilité**. Pour les clusters **SUSE**, entrez **http://\<IP  address\>:9664/metrics**. Pour les clusters **RHEL**, entrez **http://\<IP address\>:44322/metrics?names=ha_cluster**.
 
1. Entrez l’ID système, le nom d’hôte et le nom de cluster dans les zones correspondantes.
   
   > [!IMPORTANT]
   > Le nom d’hôte fait référence au nom d’hôte réel dans la machine virtuelle. Utilisez la commande « hostname -s » pour les clusters SUSE comme les clusters RHEL.  

1. Lorsque vous avez terminé, sélectionnez **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez **Vérifier + créer** pour terminer le déploiement.

### <a name="os-linux-provider"></a>Fournisseur de système d’exploitation (Linux) 

1. Sélectionnez **Ajouter un fournisseur**, puis suivez les étapes ci-dessous :

   1. Pour **Type**, sélectionnez **Système d’exploitation (Linux)** . 

      >[!IMPORTANT]
      > Pour configurer un fournisseur de système d’exploitation (Linux), assurez-vous que la [dernière version de node_exporter](https://prometheus.io/download/#node_exporter) est installée sur chacun des hôtes (système nu ou machine virtuelle) à analyser. [Plus d’informations](https://github.com/prometheus/node_exporter)

   1. Pour **Nom**, entrez un nom. Ce sera l’identificateur de l’instance système nu.

   1. Pour **Point de terminaison node_exporter**, entrez **http://IP:9100/metrics** .

      >[!IMPORTANT]
      >Utilisez l’adresse IP privée de l’hôte Linux. L’hôte et la ressource Azure Monitor pour SAP Solutions doivent se trouver dans le même réseau virtuel. 
      >
      >Le port du pare-feu « 9100 » doit être ouvert sur l’hôte Linux. Si vous utilisez `firewall-cmd`, exécutez les commandes suivantes : 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >Si vous utilisez `ufw`, exécutez les commandes suivantes :
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > Si l’hôte Linux est une machine virtuelle Azure, assurez-vous que tous les groupes de sécurité réseau applicables autorisent le trafic entrant sur le port 9100 à partir de la source `VirtualNetwork`.
 
1. Lorsque vous avez terminé, sélectionnez  **Ajouter un fournisseur**. Continuez à ajouter des fournisseurs si besoin, ou sélectionnez  **Vérifier + créer**  pour terminer le déploiement. 


## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail Azure Monitor pour SAP Solutions.

> [!div class="nextstepaction"]
> [Superviser SAP sur Azure](monitor-sap-on-azure.md)
