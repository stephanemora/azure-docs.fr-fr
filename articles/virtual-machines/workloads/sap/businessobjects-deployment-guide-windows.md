---
title: Déploiement de la plateforme SAP BusinessObjects BI sur Azure pour Windows | Microsoft Docs
description: Déployez et configurez la plateforme SAP BusinessObjects BI sur Azure pour Windows.
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: bd65c77d8be0cef06a81f0f7699b04134e0e1706
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563150"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Guide de déploiement de la plateforme SAP BusinessObjects BI pour Windows sur Azure

Cet article décrit la stratégie de déploiement de la plateforme SAP BusinessObjects Business Intelligence (SAP BOBI) sur Azure pour Windows. Dans cet exemple, deux machines virtuelles avec disques managés Azure Premium SSD comme répertoire d’installation sont configurées. Azure SQL Database, une offre PaaS (Platform as a service), est utilisée pour les bases de données d’audit et de serveur de gestion centralisée (CMS). Azure Premium Files, un protocole SMB, est utilisé comme magasin de fichiers, partagé entre les deux machines virtuelles. L’application web Java Tomcat par défaut et l’application de plateforme décisionnelle (BI) sont installées ensemble sur les deux machines virtuelles. Pour équilibrer la charge de la demande de l’utilisateur, Azure Application Gateway est utilisé avec des capacités de déchargement TLS/SSL natives.

Ce type d’architecture est efficace pour des environnements hors production ou de déploiement à petite échelle. Pour un déploiement de production ou à grande échelle, vous devriez séparer les hôtes pour les applications web et vous pouvez avoir plusieurs hôtes d’applications SAP BOBI permettant au serveur de traiter plus d’informations.

![Diagramme illustrant un déploiement SAP BOBI sur Azure pour Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

Dans cet exemple, les versions de produit et la disposition du système de fichiers suivants sont utilisés :

- SAP BusinessObjects Platform 4.3 SP01 Patch 1
- Windows Server 2019
- SQL Database (version 12.0.2000.8)
- Pilote ODBC Microsoft - msodbcsql.msi (version : 13.1)

| Système de fichiers        | Description                                                                                                               | Taille (Go)             | Accès requis  | Stockage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | Système de fichiers pour l’installation d’une instance SAP BOBI, de l’application web Tomcat par défaut et des pilotes de base de données (le cas échéant). | Recommandations de dimensionnement SAP | Privilèges d’administration locaux | Disques managés Azure Premium SSD|
| \\\azusbobi.file.core.windows.net\frsinput  | Le répertoire de montage est destiné aux fichiers partagés entre tous les hôtes SAP BOBI qui seront utilisés comme répertoire de magasin de fichiers d’entrée. | Besoin métier         | Privilèges d’administration locaux | Azure NetApp Files         |
| \\\azusbobi.file.core.windows.net\frsoutput | Le répertoire de montage est destiné aux fichiers partagés entre tous les hôtes SAP BOBI qui seront utilisés comme répertoire de magasin de fichiers de sortie. | Besoin métier         | Privilèges d’administration locaux | Azure NetApp Files         |

## <a name="deploy-a-windows-virtual-machine-via-the-azure-portal"></a>Déployer une machine virtuelle Windows via le portail Azure

Dans cette section, nous allons créer deux machines virtuelles avec une image du système d’exploitation Windows pour la plateforme SAP BOBI. Les principales étapes pour créer des machines virtuelles sont les suivantes :

1. Créez un [groupe de ressources](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups).

1. Créez un [réseau virtuel](../../../virtual-network/quick-create-portal.md#create-a-virtual-network) :

   - N’utilisez pas un sous-réseau unique pour tous les services Azure dans un déploiement de plateforme décisionnelle SAP. En fonction de l’architecture de la plateforme décisionnelle SAP, vous pouvez être amené à créer plusieurs sous-réseaux. Dans ce déploiement, nous allons créer deux sous-réseaux : un sous-réseau d’applications décisionnelles et un sous-réseau Application Gateway.
   - Suivez la note SAP [2276646](https://launchpad.support.sap.com/#/notes/2276646) pour identifier les ports de communication de plateforme SAP BOBI sur différents composants.
   - SQL Database communique par le biais du port 1433. Le trafic sortant sur le port 1433 doit être autorisé à partir de vos serveurs d’applications SAP BOBI.
   - Dans Azure, Application Gateway doit se trouver sur un sous-réseau distinct. Pour plus d’informations, consultez [Présentation de la configuration d’Application Gateway](../../../application-gateway/configuration-overview.md).
   - Si vous utilisez Azure NetApp Files pour un magasin de fichiers au lieu d’Azure Files, créez un sous-réseau distinct pour Azure NetApp Files. Pour plus d’informations, consultez [Consignes pour planifier un réseau Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-network-topologies.md).

1. Créez un groupe à haute disponibilité :

   - Pour assurer la redondance pour chaque niveau dans le cadre d’un déploiement à plusieurs instances, placez les machines virtuelles de chaque niveau dans un groupe à haute disponibilité. Veillez à séparer les groupes à haute disponibilité pour chaque niveau en fonction de votre architecture.

1. Créez la machine virtuelle 1 (azuswinboap1) :

   - Vous pouvez utiliser une image personnalisée ou choisir une image à partir de la Place de marché Azure. En fonction de vos besoins, consultez [Déployer une machine virtuelle à partir de la Place de marché Azure pour SAP](deployment-guide.md) ou [Déployer une machine virtuelle avec une image personnalisée pour SAP](deployment-guide.md).

1. Créez la machine virtuelle 2 (azuswinboap2).
1. Ajoutez un disque SSD Premium. Il sera utilisé comme répertoire d’installation SAP BOBI.

## <a name="provision-azure-premium-files"></a>Provisionner Azure Premium Files

Avant de poursuivre la configuration d’Azure Files, familiarisez-vous avec la [documentation d’Azure Files](../../../storage/files/storage-files-introduction.md).

Azure Files propose des partages de fichiers standard, hébergés sur disques HDD, et des partages de fichiers Premium, hébergés sur disques SSD. Pour un magasin de fichiers SAP BusinessObjects, utilisez Azure Premium Files.

Les partages de fichiers Azure Premium sont disponibles avec la redondance locale et de zone dans un sous-ensemble de régions. Pour savoir si les partages de fichiers Premium sont actuellement disponibles dans votre région, consultez [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Pour obtenir des informations sur les régions qui prennent en charge le stockage redondant interzone (ZRS), consultez [Redondance de Stockage Azure](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="deploy-an-azure-files-storage-account-and-nfs-shares"></a>Déployer un compte de stockage de fichiers Azure et des partages NFS

Les partages de fichiers Azure sont déployés dans des comptes de stockage, qui sont des objets de niveau supérieur représentant un pool partagé de stockage. Ce pool de stockage peut être utilisé pour déployer plusieurs partages de fichiers. Azure prend en charge plusieurs types de comptes de stockage pour différents scénarios de stockage que les clients peuvent rencontrer. Pour le stockage de fichiers SAP BusinessObjects, vous devez créer un compte FileStorage. Vous l’utilisez pour déployer des partages de fichiers Azure sur du matériel SSD Premium.

> [!NOTE]
> Les comptes FileStorage peuvent uniquement être utilisés pour stocker des partages de fichiers Azure. Aucune autre ressource de stockage, telle que des blobs, des conteneurs, des files d’attente ou des tables, ne peut être déployée dans un compte FileStorage.

Le compte de stockage est accessible via un [point de terminaison privé](../../../storage/files/storage-files-networking-endpoints.md) et déployé dans le même réseau virtuel d’une plateforme SAP BOBI. Avec cette configuration, le trafic provenant de votre système SAP ne quitte jamais les limites de sécurité du réseau virtuel. Les systèmes SAP contiennent souvent des données sensibles et critiques, et le fait de rester dans les limites du réseau virtuel est important en termes de sécurité pour de nombreux clients.

Si vous avez besoin d’accéder au compte de stockage à partir d’un autre réseau virtuel, vous pouvez utiliser l’[appairage de réseaux virtuels Azure](../../../virtual-network/virtual-network-peering-overview.md).

#### <a name="azure-files-storage-account"></a>Compte de stockage Azure Files

1. Pour créer un compte de stockage par le biais du portail Azure, sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage**.

1. Sous l’onglet **Informations de base**, renseignez tous les champs obligatoires pour créer un compte de stockage :

   1. Sélectionnez **Abonnement** > **Groupe de ressources** > **Région**.

   1. Entrez le **nom du compte de stockage**. Par exemple, entrez **azusbobi**. Ce nom doit être globalement unique, mais il peut s’agir du nom de votre choix.

   1. Sélectionnez **Premium** comme niveau de performance et **FileStorage** comme type de compte.

   1. Pour l’étiquette **Réplication**, choisissez un niveau de redondance. sélectionnez **Stockage localement redondant (LRS)**.

      Pour Premium FileStorage, LRS et ZRS sont les seules options disponibles. En fonction de votre stratégie de déploiement (groupe à haute disponibilité ou zone de disponibilité), choisissez le niveau de redondance approprié. Pour plus d’informations, consultez [Redondance de Stockage Azure](../../../storage/common/storage-redundancy.md).

   1. Sélectionnez **Suivant**.

1. Sous l’onglet **Réseau**, sélectionnez [Point de terminaison privé](../../../storage/files/storage-files-networking-endpoints.md) comme méthode de connectivité. Pour plus d’informations, consultez [Considérations relatives à la mise en réseau Azure Files](../../../storage/files/storage-files-networking-overview.md).

   1. Sélectionnez **Ajouter** dans la section Point de terminaison privé.

   1. Sélectionnez **Abonnement** > **Groupe de ressources** > **Emplacement**.

   1. Entrez le **nom** du point de terminaison privé. Par exemple, entrez **azusbobi-pe**.

   1. Sélectionnez **Fichier** dans **Sous-ressource de stockage**.

   1. Dans la section **Mise en réseau**, sélectionnez le **réseau virtuel** et le **sous-réseau** sur lesquels l’application SAP BusinessObjects BI est déployée.

   1. Acceptez la **Valeur par défaut (oui)** sous **Intégrer avec une zone DNS privée**.

   1. Sélectionnez votre **zone DNS privée** dans la liste déroulante.

   1. Sélectionnez **OK** pour revenir à l’onglet **Mise en réseau** dans **Créer un compte de stockage**.

1. Dans l’onglet **Protection de données**, configurez la stratégie de suppression réversible pour les partages de fichiers Azure dans votre compte de stockage. Par défaut, la fonctionnalité de suppression réversible est désactivée. Pour en savoir plus sur la suppression réversible, consultez [Empêcher la suppression accidentelle de partages de fichiers Azure](../../../storage/files/storage-files-prevent-file-share-deletion.md).

1. Sous l’onglet **Avancé**, sélectionnez des options de sécurité différentes.

   Le champ **Transfert sécurisé requis** indique si le compte de stockage nécessite un chiffrement en transit pour la communication avec le compte de stockage. Si vous avez besoin d’une aide par rapport au protocole SMB 2.1, vous devez désactiver ce champ. Pour la plateforme SAP BOBI, conservez la valeur **par défaut (activé)** .

1. Continuez et créez le compte de stockage.

Pour plus d’informations sur la création d’un compte de stockage, consultez [Créer un compte de stockage FileStorage](../../../storage/files/storage-how-to-create-file-share.md).

#### <a name="create-azure-file-shares"></a>Créer des partages de fichiers Azure

L’étape suivante consiste à créer des fichiers Azure dans le compte de stockage. Azure Files utilise un modèle provisionné pour les partages de fichiers Premium. Dans un modèle métier provisionné, vous spécifiez de manière proactive à Azure Files vos exigences de stockage au lieu de payer en fonction de votre utilisation. Pour en savoir plus sur ce modèle, consultez [Modèle provisionné](../../../storage/files/understanding-billing.md#provisioned-model). Dans cet exemple, nous créons deux fichiers Azure : frsinput (256 Go) et frsoutput (256 Go) pour le magasin de fichiers SAP BOBI.

1. Accédez au compte de stockage **azusbobi** > **Partages de fichiers**.
1. Sélectionnez **Nouveau partage de fichiers**.
1. Entrez le **nom** du partage de fichiers. Par exemple, entrez **frsinput** ou **frsouput**.
1. Insérez la taille de partage de fichiers requise dans **Capacité provisionnée**. Par exemple, entrez **256 Go**.
1. Choisissez le **Protocole** **SMB**.
1. Sélectionnez **Create** (Créer).

## <a name="configure-a-data-disk-on-a-windows-virtual-machine"></a>Configurer un disque de données sur une machine virtuelle Windows

Les étapes de cette section utilisent le préfixe suivant :

**[A]**  : L’étape s’applique à tous les hôtes.

### <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

L’application SAP BusinessObjects BI requiert une partition sur laquelle ses fichiers binaires peuvent être installés. Vous pouvez installer une application SAP BOBI sur la partition du système d’exploitation (C:), mais vous devez veiller à disposer de suffisamment d’espace pour le déploiement et le système d’exploitation. Nous vous recommandons de disposer d’au moins 2 Go pour les fichiers temporaires et les applications web. En outre, il est recommandé de séparer les fichiers binaires d’installation SAP BOBI dans des partitions distinctes.

Dans cet exemple, une application SAP BOBI est installée sur une partition distincte (F:). Initialisez le disque SSD Premium que vous avez attaché pendant le provisionnement des machines virtuelles :

1. **[A]** Si aucun disque de données n’est attaché à la machine virtuelle (azuswinboap1 et azuswinboap2), suivez les étapes de la section [Ajouter un disque de données](../../windows/attach-managed-disk-portal.md#add-a-data-disk) pour attacher un nouveau disque de données managé.
1. **[A]** Une fois que le disque managé est attaché à la machine virtuelle, initialisez le disque en suivant la procédure décrite dans [Initialiser un nouveau disque de données](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk).

### <a name="mount-azure-premium-files"></a>Monter des fichiers Azure Premium

Pour utiliser Azure Files comme magasin de fichiers, vous devez le monter, ce qui signifie lui attribuer une lettre de lecteur ou un chemin de point de montage.

**[A]** Pour monter le partage de fichiers Azure, suivez les étapes décrites dans [Monter le partage de fichiers Azure](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share).

Pour monter un partage de fichiers Azure sur un serveur Windows Server, assurez-vous que le port 445 est ouvert. Le protocole SMB nécessite que le port TCP 445 soit ouvert. Les connexions échoueront si le port 445 est bloqué. Vous pouvez vérifier si le pare-feu bloque le port 445 avec l’applet de commande `Test-NetConnection` mentionnée dans le guide de [résolution des problèmes](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked).

## <a name="configure-a-cms-database-azure-sql"></a>Configurer une base de données CMS : Azure SQL

Cette section fournit des informations sur le provisionnement d’Azure SQL à l’aide du portail Azure. Elle fournit également des instructions sur la création des bases de données d’audit et CMS pour une plateforme SAP BOBI et d’un compte d’utilisateur pour accéder à ces bases de données.

Les instructions s’appliquent uniquement si vous utilisez SQL Database. Pour les autres bases de données, consultez la documentation SAP ou la documentation propre à la base de données.

### <a name="create-a-sql-database-server"></a>Créer un serveur SQL Database

SQL Database offre différentes options de déploiement : base de données unique, pool élastique et serveur de base de données. Pour une plateforme SAP BOBI, nous avons besoin de deux bases de données, CMS et d’audit. Au lieu de créer deux bases de données uniques, vous pouvez créer un serveur SQL Database capable de gérer le groupe de bases de données uniques et de pools élastiques. Pour créer un serveur SQL Database, suivez la procédure suivante :

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL).
1. Sous **Bases de données SQL**, modifiez **Type de ressource** en **Serveur de base de données**. Sélectionnez **Create** (Créer).
1. Sous l’onglet **Informations de base**, renseignez tous les champs obligatoires pour **créer le serveur SQL Database** :

   1. Sous **Détails du projet**, sélectionnez l’**abonnement** et le **groupe de ressources**.

   1. Entrez un **nom de serveur**. Par exemple, entrez **azussqlbodb**. Le nom de serveur doit être globalement unique, mais il peut s’agir du nom de votre choix.

   1. Sélectionnez un **Emplacement**.

   1. Entrez la **connexion d’administrateur du serveur**. Par exemple, entrez **boadmin**. Ensuite, entrez un **mot de passe**.

1. Sous l’onglet **Mise en réseau**, modifiez **Autoriser les services et les ressources Azure à accéder à ce serveur** sur **Non** sous **Règles de pare-feu**.
1. Dans **Paramètres supplémentaires**, conservez les paramètres par défaut.
1. Continuez et créez le **Serveur de base de données SQL**.

À l’étape suivante, créez les bases de données CMS et d’audit dans le serveur SQL Database (azussqlbodb.database.windows.net).

### <a name="create-the-cms-and-the-audit-database"></a>Créer les bases de données CMS et d’audit

Une fois que vous avez provisionné le serveur SQL Database, accédez à la ressource azussqlbodb. Ensuite, procédez comme suit pour créer les bases de données CMS et d’audit.

1. Dans la page **Vue d’ensemble d’azussqlbodb**, sélectionnez **Créer une base de données**.
1. Sous l’onglet **Informations de base**, renseignez tous les champs requis :

   1. Entrez le **nom de la base de données**. Par exemple, entrez **bocms** ou **boaudit**.

   1. Dans l’option **Calcul + stockage**, sélectionnez **Configurer la base de données**. Choisissez le modèle approprié en fonction de votre résultat de dimensionnement. Pour obtenir des informations sur les options, consultez [Dimensionnement des modèles pour Azure SQL Database](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database).
1. Sous l’onglet **Mise en réseau**, sélectionnez [Point de terminaison privé](../../../private-link/tutorial-private-endpoint-sql-portal.md) comme méthode de connectivité. Le point de terminaison privé sera utilisé pour accéder à SQL Database au sein du réseau virtuel configuré.

   1. Sélectionnez **Ajouter un point de terminaison privé**.

   1. Sélectionnez **Abonnement** > **Groupe de ressources** > **Emplacement**.

   1. Entrez le **nom** du point de terminaison privé. Par exemple, entrez **azusbodb-pe**.

   1. Dans **Sous-ressource cible**, sélectionnez **SqlServer**.

   1. Dans la section **Mise en réseau**, sélectionnez le **réseau virtuel** et le **sous-réseau** sur lesquels l’application SAP BusinessObjects BI est déployée.

   1. Acceptez **par défaut (oui)** pour **Intégrer avec une zone DNS privée**.

   1. Sélectionnez votre **zone DNS privée** dans la liste déroulante.

   1. Sélectionnez **OK** pour revenir à l’onglet **Mise en réseau** dans **Créer une base de données SQL**.
1. Sous l’onglet **Paramètres supplémentaires**, modifiez le paramètre **Classement** en spécifiant **SQL_Latin1_General_CP850_BIN2**.
1. Continuez et créez la base de données CMS.

De même, vous pouvez créer la base de données d’audit. Par exemple, entrez **boaudit**.

### <a name="download-and-install-an-odbc-driver"></a>Télécharger et installer un pilote ODBC

Les serveurs d’applications SAP BOBI requièrent des clients/pilotes de base de données pour accéder aux bases de données CMS et d’audit. Un pilote Microsoft ODBC est utilisé pour accéder aux bases de données CMS et d’audit qui s’exécutent sur SQL Database. Cette section fournit des instructions pour télécharger et configurer un pilote ODBC sur Windows. 

1. Consultez la section **Prise en charge du référentiel CMS + Audit par système d’exploitation** dans [Tableau de disponibilité des produits pour la plateforme SAP BusinessObjects BI](https://support.sap.com/pam) pour découvrir les connecteurs de base de données compatibles avec SQL Database.
1. Téléchargez la version du pilote ODBC à partir du [lien](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15). Dans cet exemple, nous téléchargeons le pilote ODBC [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131).
1. Installez le pilote ODBC sur tous les serveurs BI (azuswinboap1 et azuswinboap2).
1. Après avoir installé le pilote dans **azuswinboap1**, accédez à **Démarrer** > **Outils d’administration Windows** > **Sources de données ODBC (64 bits)** .
1. Accédez à l’onglet **DSN système**. 
1. Sélectionnez **Ajouter** pour créer une connexion à la base de données CMS.
1. Sélectionnez **ODBC Driver 13 for SQL Server**, puis **Terminer**.
1. Entrez les informations de votre base de données CMS comme suit, puis sélectionnez **Suivant** :
   - **Nom** : nom de la base de données créée dans la section « Créer les bases de données CMS et d’audit ». Par exemple, entrez **bocms** ou **boaudit**.
   - **Description** : description de la source de données. Par exemple, entrez **Base de données CMS** ou **Base de données d’audit**.
   - **Serveur** : nom du serveur créé dans la section « Créer un serveur SQL Database ». Par exemple, entrez azussqlbodb.database.windows.net.
1. Sélectionnez **Avec l’authentification SQL Server utilisant un identificateur de connexion et un mot de passe entrés par l’utilisateur** pour vérifier l’authenticité auprès d’Azure SQL Server. Entrez les informations d’identification d’utilisateur qui ont été créées au moment de la création du serveur SQL Database. Par exemple, entrez **boadmin**. Sélectionnez **Suivant**.
1. Remplacez la **base de données par défaut** par **bocms** et conservez toutes les autres valeurs par défaut. Sélectionnez **Suivant**.
1. Cochez la case **Utiliser le chiffrement renforcé pour les données** et conservez toutes les autres valeurs par défaut. Sélectionnez **Terminer**.
1. La source de données de la base de données CMS a été créée. Vous pouvez maintenant sélectionner **Tester la source de données** pour valider la connexion à la base de données CMS à partir de l’application décisionnelle. Le test doit se terminer correctement. En cas d’échec, [corrigez](../../../azure-sql/database/troubleshoot-common-errors-issues.md) le problème de connectivité.

>[!Note]
>SQL Database communique par le biais du port 1433. Le trafic sortant sur le port 1433 doit être autorisé à partir de vos serveurs d’applications SAP BOBI.

Répétez les étapes précédentes pour créer une connexion pour la base de données d’audit sur le serveur azuswinboap1. De la même manière, installez et configurez les deux sources de données ODBC (bocms et boaudit) sur tous les serveurs d’applications décisionnelles (azuswinboap2). 

## <a name="server-preparation"></a>Préparation du serveur

Suivez le dernier guide de SAP pour préparer les serveurs pour l’installation de la plateforme décisionnelle. Pour obtenir les informations les plus récentes, consultez la section « Préparation » du [Guide d’installation de SAP Business Intelligence Platform pour Windows](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html).

## <a name="installation"></a>Installation

Pour installer la plateforme BI sur un hôte Windows, connectez-vous avec un utilisateur disposant de privilèges d’administrateur local.

Accédez au support de la plateforme SAP BusinessObjects BI et exécutez `setup.exe`.

Suivez les instructions du [Guide d’installation de la plateforme Business Intelligence SAP pour Windows](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html) qui sont spécifiques à votre version. Voici quelques points à noter lors de l’installation de la plateforme SAP BOBI sur Windows :

- Dans l’écran **Configurer le dossier de destination**, indiquez le dossier de destination dans lequel vous souhaitez installer la plateforme BI. Par exemple, entrez **F:\SAP BusinessObjects\*** . 
- Dans l’écran **Configure Product Registration** (Configurer l’enregistrement du produit), vous pouvez utiliser une clé de licence temporaire pour les solutions SAP BusinessObjects à partir de la note SAP [1288121](https://launchpad.support.sap.com/#/notes/1288121) ou générer une clé de licence dans SAP Service Marketplace.
- Dans l’écran **Select Install Type** (Sélectionner le type d’installation), sélectionnez **Full installation** (Installation complète) sur le premier serveur (azuswinboap1). Pour l’autre serveur (azuswinboap2), sélectionnez **Custom / Expand** (Personnalisé/développer), qui développera la configuration SAP BOBI existante.
- Dans l’écran **Select Default or Existing Database** (Sélectionner la base de données existante ou par défaut), sélectionnez **Configure an existing database** (Configurer une base de données existante), ce qui vous invite à sélectionner les bases de données CMS et d’audit. Sélectionnez **Microsoft SQL Server utilisant ODBC** comme type de **base de données CMS** et type de **base de données d’audit**.

  Vous pouvez également sélectionner **No auditing database** (Aucune base de données d’audit) si vous ne voulez pas configurer l’audit pendant l’installation.

- Sélectionnez les options appropriées dans l’écran **Select Java Web Application Server** (Sélectionner le serveur d’applications web Java) en fonction de votre architecture SAP BOBI. Dans cet exemple, nous avons sélectionné l’option 1, qui installe un serveur Tomcat sur la même plateforme SAP BOBI.
- Entrez les informations de la base de données CMS dans **Configure CMS Repository Database - SQL Server (ODBC)** (Configurer la base de données du référentiel CMS – SQL Server (ODBC)). L’image suivante montre un exemple d’entrée pour les informations de base de données CMS pour une installation Windows.
  
  ![Capture d’écran montrant les informations de base de données CMS pour Windows.](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)
- (Facultatif) Entrez les informations de la base de données d’audit dans **Configure Auditing Database - SQL Server (ODBC)** (Configurer la base de données d’audit – SQL Server (ODBC)). L’image suivante montre un exemple d’entrée pour les informations de base de données d’audit pour une installation Windows.
  
  ![Capture d’écran montrant les informations de base de données d’audit pour Windows.](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- Suivez les instructions et saisissez les entrées requises pour terminer l’installation.

Pour un déploiement à plusieurs instances, exécutez la configuration de l’installation sur le deuxième hôte (azuswinboap2). Dans l’écran **Select Install Type** (Sélectionner le type d’installation), sélectionnez **Custom / Expand** (Personnalisé/développer), qui développe la configuration SAP BOBI actuelle. Pour plus d’informations, consultez le blog SAP sur la [configuration de la plateforme SAP BusinessObjects Business Intelligence avec Azure SQL Database](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/).

> [!IMPORTANT]
> Les numéros de version du moteur de base de données pour SQL Server et SQL Database ne sont pas comparables. Il s’agit de numéros de build internes pour ces produits distincts. Le moteur de base de données pour SQL Database repose sur la même base de code que le moteur de base de données SQL Server. Plus important encore, le moteur de base de données dans SQL Database a toujours les bits les plus récents du moteur de base de données SQL. La version 12 de SQL Database est plus récente que la version 15 de SQL Server.

Pour déterminer la version actuelle de SQL Database, vous pouvez vérifier les paramètres de la console de gestion centralisée (CMC) ou exécuter la requête suivante à l’aide de [sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) ou de [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15). L’alignement des versions de SQL avec la compatibilité par défaut est disponible dans l’article sur le [niveau de compatibilité des bases de données](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15).

![Capture d’écran montrant les informations de base de données dans CMC](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

```sql
1> select @@version as version;
2> go
version
------------------------------------------------------------------------------------------
Microsoft SQL Azure (RTM) - 12.0.2000.8
        Feb 20 2021 17:51:58
        Copyright (C) 2019 Microsoft Corporation

(1 rows affected)

1> select name, compatibility_level from sys.databases;
2> go
name                                                                  compatibility_level
--------------------------------------------------------------------- -------------------
master                                                                                150
bocms                                                                                 150
boaudit                                                                               150

(3 rows affected)
```

## <a name="post-installation"></a>Après l'installation

Après une installation multi-instance de la plateforme SAP BOBI, il faut effectuer des étapes post-configuration supplémentaires pour prendre en charge la haute disponibilité des applications.

### <a name="configure-a-cluster-name"></a>Configurer un nom de cluster

Dans le cadre d’un déploiement multi-instance de la plateforme SAP BOBI, vous voulez exécuter plusieurs serveurs CMS ensemble dans un cluster. Un cluster se compose d’au moins deux serveurs CMS qui travaillent conjointement sur une base de données système CMS commune. Si un nœud qui s’exécute sur un CMS échoue, un nœud situé sur un autre CMS continue de traiter les demandes de la plateforme décisionnelle. Par défaut, sur une plateforme SAP BOBI, un nom de cluster reflète le nom d’hôte du premier CMS que vous installez. 

Pour configurer le nom du cluster dans Windows, suivez les instructions indiquées dans le [Guide de l’administrateur de la plateforme SAP Business Intelligence](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US). Après avoir configuré le nom du cluster, suivez la note SAP [1660440](https://launchpad.support.sap.com/#/notes/1660440) pour définir l’entrée système par défaut sur la page de connexion Launchpad CMC ou BI. 

### <a name="configure-the-input-and-output-filestore-location-to-azure-premium-files"></a>Configurer l’emplacement de magasin de fichiers d’entrée et de sortie sur Azure Premium Files

Le magasin de fichiers fait référence aux répertoires de disque où se trouvent les fichiers SAP BusinessObjects BI. L’emplacement par défaut du serveur de référentiel de fichiers de la plateforme SAP BOBI correspond au répertoire d’installation local. Dans un déploiement multi-instance, il est important de configurer un magasin de fichiers sur un stockage partagé, comme Azure Premium Files ou Azure NetApp Files, afin qu’il soit accessible à partir de tous les serveurs de niveau de stockage.

1. S’il n’est pas créé, suivez les instructions fournies dans la section précédente, « Provisionner Azure Premium Files », pour créer et monter Azure Premium Files.

   > [!Tip]
   > Choisissez la redondance de stockage pour Azure Premium Files (LRS ou ZRS) en fonction du déploiement de votre machine virtuelle (groupe à haute disponibilité ou zone de disponibilité).

1. Suivez la note SAP [2512660](https://launchpad.support.sap.com/#/notes/0002512660) pour modifier le chemin d’accès du référentiel de fichiers (entrée et sortie).

### <a name="tomcat-clustering-session-replication"></a>Clustering Tomcat : réplication de session

Tomcat prend en charge le clustering d’au moins deux serveurs d’applications pour la réplication et le basculement de session. Si les sessions de la plateforme SAP BOBI sont sérialisées, une session utilisateur peut basculer en toute transparence vers une autre instance de Tomcat, même en cas de défaillance d’un serveur d’applications. Par exemple, un utilisateur peut être connecté à un serveur web qui échoue alors qu’il parcourt une arborescence de dossiers dans une application décisionnelle SAP. Sur un cluster correctement configuré, l’utilisateur peut continuer à parcourir l’arborescence des dossiers sans être redirigé vers une page de connexion.

Dans la note SAP [2808640](https://launchpad.support.sap.com/#/notes/2808640), la procédure de configuration du clustering Tomcat est fournie au moyen de la multidiffusion. Toutefois, la multidiffusion n’est pas prise en charge dans Azure. Pour qu’un cluster Tomcat fonctionne dans Azure, vous devez utiliser [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (note SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Pour configurer un cluster Tomcat dans Azure, consultez l’article [Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) (Clustering Tomcat utilisant l’appartenance statique pour la plateforme SAP BusinessObjects BI) sur le blog SAP.

### <a name="load-balance-a-web-tier-of-an-sap-bi-platform"></a>Équilibrer la charge d’une couche web d’une plateforme décisionnelle SAP

Dans le cadre d’un déploiement multi-instance SAP BOBI, les serveurs d’applications web Java (couche web) s’exécutent sur au moins deux hôtes. Pour répartir uniformément la charge des utilisateurs entre les serveurs web, vous pouvez utiliser un équilibreur de charge entre les utilisateurs finaux et les serveurs web. Vous pouvez utiliser Azure Load Balancer ou Application Gateway pour gérer le trafic vers vos serveurs d’applications web. Les offres sont expliquées dans les sections suivantes :

* [Load Balancer](../../../load-balancer/load-balancer-overview.md) est un équilibreur de charge de couche 4 (TCP, UDP) à haute performance et à faible latence, qui répartit le trafic entre les machines virtuelles saines. Une sonde d’intégrité d’équilibreur de charge surveille un port donné sur chaque machine virtuelle et distribue le trafic uniquement aux machines virtuelles opérationnelles. Vous pouvez choisir un équilibreur de charge public ou un équilibreur de charge interne, selon que vous souhaitez que la plateforme décisionnelle SAP soit accessible ou non à partir d’Internet. Azure Load Balancer est redondant dans une zone, ce qui garantit une haute disponibilité dans l’ensemble des zones de disponibilité.

   Dans l’illustration suivante, consultez la section « Équilibreur de charge interne », où le serveur d’applications web s’exécute sur le port 8080 (port HTTP par défaut de Tomcat), qui sera surveillé par une sonde d’intégrité. Toute demande entrante émanant des utilisateurs sera redirigée vers les serveurs d’applications web (azuswinboap1 ou azuswinboap2) dans le pool de back-ends. Load Balancer ne prend pas en charge la terminaison TLS/SSL, également appelée déchargement TLS/SSL. Si vous utilisez Load Balancer pour distribuer le trafic entre les serveurs web, nous vous recommandons d’utiliser Standard Load Balancer.

   > [!NOTE]
   > Lorsque des machines virtuelles sans adresse IP publique sont placées dans le pool de back-ends de l’équilibreur Standard Load Balancer interne (aucune adresse IP publique), il n’y a pas de connectivité Internet sortante, sauf si une configuration supplémentaire est effectuée pour autoriser le routage vers des points de terminaison publics. Pour découvrir comment bénéficier d’une connectivité sortante, consultez [Connectivité de point de terminaison public pour les machines virtuelles avec Azure Standard Load Balancer dans les scénarios de haute disponibilité SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

   ![Capture d’écran montrant Load Balancer utilisé pour équilibrer le trafic entre les serveurs web.](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

* [Application Gateway](../../../application-gateway/overview.md) fournit un contrôleur de livraison d’applications en tant que service, qui aide les applications à diriger le trafic des utilisateurs vers un ou plusieurs serveurs d’applications web. Il offre différentes capacités d’équilibrage de charge de couche 7, telles que le déchargement TLS/SSL, le pare-feu d’applications web (WAF) et l’affinité de session basée sur les cookies pour vos applications.

   Dans une plateforme décisionnelle SAP, Application Gateway dirige le trafic web des applications vers les ressources spécifiées dans un pool de back-ends. Dans ce cas, il s’agit d’azuswinboap1 ou d’azuswinboap2. Vous attribuez un écouteur au port, créez des règles et ajoutez des ressources à un pool de back-ends. Dans l’illustration suivante, Application Gateway avec une adresse IP de front-end privée (10.31.3.25) agit comme point d’entrée pour les utilisateurs, gère les connexions TLS/SSL (HTTPS - TCP/443) entrantes, déchiffre les données TLS/SSL et transmet la demande (HTTP - TCP/8080) aux serveurs du pool de back-ends. Avec la fonctionnalité de terminaison TLS/SSL intégrée, vous devez conserver un seul certificat TLS/SSL sur la passerelle applicative, ce qui simplifie les opérations.

   ![Capture d’écran montrant Application Gateway utilisé pour équilibrer le trafic entre les serveurs web](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   Pour configurer Application Gateway pour un serveur web SAP BOBI, consultez [Load balancing SAP BOBI web servers by using Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) (Équilibrage de la charge des serveurs web SAP BOBI à l’aide d’Application Gateway) sur le blog SAP.

   > [!NOTE]
   > Utilisez Application Gateway pour équilibrer la charge du trafic vers le serveur web, car il offre des fonctionnalités comme le déchargement SSL, la gestion SSL centralisée pour réduire la surcharge de chiffrement et de déchiffrement sur le serveur, des algorithmes de tourniquet (round-robin) pour distribuer le trafic, des capacités de pare-feu d’applications web (WAF) et la haute disponibilité.

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Fiabilité de la plateforme SAP BusinessObjects BI sur Azure

La plateforme SAP BusinessObjects BI comprend différents niveaux, qui sont optimisés pour des tâches et des opérations spécifiques. Lorsqu’un composant d’un niveau quelconque devient indisponible, l’application SAP BOBI devient inaccessible ou certaines fonctionnalités de l’application ne fonctionnent pas. Vous devez vous assurer que chaque niveau est conçu pour être fiable afin de garantir le bon fonctionnement de l’application sans aucune interruption de l’activité.

Ce guide explique comment les fonctionnalités natives d’Azure, combinées à la configuration d’une plateforme SAP BOBI, améliorent la disponibilité d’un déploiement SAP. Cette section se concentre sur les options suivantes pour assurer la fiabilité de la plateforme SAP BOBI sur Azure :

- **Sauvegarde et restauration** : ce processus crée des copies périodiques des données et des applications dans des emplacements distincts. Si les données ou les applications d’origine sont perdues ou endommagées, ces copies peuvent être utilisées pour restaurer ou récupérer à l’état précédent.
- **Haute disponibilité** : une plateforme à haute disponibilité dispose d’au moins deux copies de chaque élément dans une région Azure pour que l’application reste opérationnelle si l’un des serveurs n’est plus disponible.
- **Reprise d’activité après sinistre** : ce processus restaure les fonctionnalités de votre application en cas de pertes catastrophiques. Par exemple, une région Azure entière peut devenir indisponible en raison d’une catastrophe naturelle.

L’implémentation de cette solution varie selon la nature de la configuration du système dans Azure. Vous devez adapter vos solutions de sauvegarde et de restauration, de haute disponibilité et de reprise d’activité après sinistre en fonction des besoins de votre entreprise.

## <a name="backup-and-restore"></a>Sauvegarde et restauration

La sauvegarde et la restauration sont un processus qui consiste à créer des copies périodiques des données et des applications dans un emplacement distinct pour pouvoir les restaurer ou récupérer dans un état antérieur, si les données ou les applications d’origine sont perdues ou endommagées. Il s’agit également d’un composant essentiel de toute stratégie de reprise d’activité après sinistre d’une entreprise. Ces sauvegardes permettent de restaurer l’application et la base de données à un point dans le temps au cours de la période de rétention configurée.

Pour développer une stratégie complète de sauvegarde et de restauration pour une plateforme SAP BOBI, identifiez les composants qui entraînent un temps d’arrêt du système ou une interruption de l’application. Sur une plateforme SAP BOBI, la sauvegarde des composants suivants est essentielle pour protéger l’application :

- Répertoire d’installation SAP BOBI (disques Premium managés)
- Magasin de fichiers (Azure Premium Files ou Azure NetApp Files pour une installation distribuée)
- Bases de données CMS et d’audit (SQL Database, Azure Database pour MySQL ou base de données sur Azure Virtual Machines)

La section suivante décrit comment implémenter une stratégie de sauvegarde et de restauration pour chaque composant sur une plateforme SAP BOBI.

### <a name="backup-and-restore-for-an-sap-bobi-installation-directory"></a>Sauvegarder et restaurer un répertoire d’installation SAP BOBI

Dans Azure, la méthode la plus simple pour sauvegarder des machines virtuelles et tous les disques attachés consiste à utiliser [Sauvegarde Azure](../../../backup/backup-azure-vms-introduction.md). Ce service propose des sauvegardes indépendantes et isolées pour éviter une destruction involontaire des données de vos machines virtuelles. Les sauvegardes sont stockées dans un coffre Recovery Services avec gestion intégrée des points de récupération. La configuration et la mise à l’échelle sont simples. Les sauvegardes sont optimisées et peuvent être facilement restaurées le cas échéant.

Dans le cadre du processus de sauvegarde, un instantané est pris. Les données sont transférées vers le coffre Recovery Services sans impact sur les charges de travail de production. L’instantané offre un niveau différent de cohérence, comme décrit dans [Cohérence des instantanés](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency). La sauvegarde offre également une prise en charge côte à côte pour la sauvegarde des disques managés à l’aide de la [sauvegarde des disques Azure](../../../backup/disk-backup-overview.md) en plus d’une solution de [sauvegarde des machines virtuelles Azure](../../../backup/backup-azure-vms-introduction.md). Cela est utile lorsque vous avez besoin de sauvegardes cohérentes de machines virtuelles une fois par jour et de sauvegardes plus fréquentes des disques du système d’exploitation, ou d’un disque de données spécifique cohérent en cas d’incident. Pour plus d’informations, consultez [Vue d’ensemble de la sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vms-introduction.md), [Sauvegarde de disques Azure](../../../backup/disk-backup-overview.md) et [FAQ – Sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vm-backup-faq.yml).

### <a name="backup-and-restore-for-filestore"></a>Sauvegarder et restaurer un magasin de fichiers

En fonction de votre déploiement, le magasin de fichiers d’une plateforme SAP BOBI peut se trouver sur Azure NetApp Files ou Azure Files. Choisissez parmi les options suivantes de sauvegarde et de restauration en fonction du stockage que vous utilisez pour le magasin de fichiers :

* **Azure NetApp Files** : pour Azure NetApp Files, vous pouvez créer des instantanés à la demande et planifier un instantané automatique en utilisant des stratégies d’instantané. Les instantanés fournissent une copie à un moment donné de votre volume Azure NetApp Files. Pour plus d’informations, consultez [Gérer les instantanés avec Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).
* **Azure Files** : la sauvegarde Azure Files est intégrée à une instance native de [Sauvegarde](../../../backup/backup-overview.md), qui centralise la fonction de sauvegarde et de restauration ainsi que la sauvegarde des machines virtuelles et simplifie le travail d’exploitation. Pour plus d’informations, consultez [Sauvegarde des partages de fichiers Azure](../../../backup/azure-file-share-backup-overview.md) et [Questions sur la sauvegarde des fichiers Azure](../../../backup/backup-azure-files-faq.yml).

Si vous avez créé un serveur NFS distinct, veillez à implémenter la stratégie de restauration et de sauvegarde pour ce dernier.

### <a name="backup-and-restore-for-the-cms-and-audit-database"></a>Sauvegarde et restauration des bases de données CMS et d’audit

Pour une plateforme SAP BOBI s’exécutant sur des machines virtuelles Windows, les bases de données CMS et d’audit peuvent s’exécuter sur n’importe quelle base de données prise en charge, comme décrit dans la [matrice de prise en charge](businessobjects-deployment-guide.md#support-matrix) du guide de planification et d’implémentation de la plateforme SAP BOBI sur Azure. Il est donc important que vous adoptiez la stratégie de sauvegarde et de restauration de la base de données que vous avez utilisée pour le stockage des données CMS et d’audit.

* **SQL Database** utilise la technologie SQL Server pour créer des [sauvegardes complètes](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15) chaque semaine, des [sauvegardes différentielles](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15) toutes les 12 à 24 heures et des sauvegardes du [journal des transactions](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15) toutes les 5 à 10 minutes. La fréquence des sauvegardes du journal des transactions est basée sur la taille de calcul et le volume d’activité de la base de données.
 
   Les utilisateurs peuvent choisir une option pour configurer la redondance du stockage de sauvegarde entre les objets blob LRS, ZRS ou GRS. Les mécanismes de redondance de stockage stockent plusieurs copies de vos données afin de les protéger contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant, et des catastrophes naturelles. Par défaut, SQL Database stocke les sauvegardes dans des [objets blob GRS](../../../storage/common/storage-redundancy.md) qui sont répliqués dans une [région jumelée](../../../best-practices-availability-paired-regions.md). En fonction des besoins de l’entreprise, vous pouvez remplacer cela par des objets blob LRS ou ZRS. Pour obtenir des informations plus à jour sur la planification des sauvegardes SQL Database, la rétention et la consommation du stockage, consultez [Sauvegardes automatisées – Azure SQL Database et Azure SQL Managed Instance](../../../azure-sql/database/automated-backups-overview.md).

* **Azure Database pour MySQL** crée automatiquement des sauvegardes de serveur et les stocke dans des stockages LRS ou GRS configurés par l’utilisateur. Azure Database pour MySQL effectue des sauvegardes des fichiers de données et du journal des transactions. Selon la taille de stockage maximale prise en charge, des sauvegardes complètes et différentielles (serveurs de stockage de 4 To au maximum) ou des sauvegardes d’instantanés (serveurs de stockage jusqu’à 16 To) sont réalisées. Ces sauvegardes vous permettent de restaurer un serveur à n’importe quel point dans le temps au cours de la période de conservation des sauvegardes configurée. Par défaut, la période de conservation des sauvegardes est de 7 jours, mais vous pouvez [éventuellement configurer](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) jusqu’à 35 jours. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES 256 bits. Ces fichiers de sauvegarde ne sont pas exposés aux utilisateurs et ne peuvent pas être exportés. Ces sauvegardes sont utilisables uniquement pour les opérations de restauration dans Azure Database pour MySQL. Vous pouvez utiliser [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) pour copier une base de données. Pour plus d’informations, consultez [Sauvegarde et restauration dans Azure Database pour MySQL](../../../mysql/concepts-backup.md).

* **Pour une base de données installée sur une machine virtuelle Azure**, vous pouvez utiliser les outils de sauvegarde standard ou [Sauvegarde](../../../backup/sap-hana-db-about.md) pour les bases de données prises en charge. De plus, si les services et outils Azure ne répondent pas à vos exigences, vous pouvez utiliser des outils de sauvegarde tiers pris en charge qui fournissent un agent pour la sauvegarde et la récupération de tous les composants de la plateforme SAP BOBI.

## <a name="high-availability"></a>Haute disponibilité

La haute disponibilité désigne un ensemble de technologies qui peuvent réduire les interruptions des services informatiques en assurant la continuité de l’activité des services ou des applications par le biais de composants redondants, tolérants aux pannes ou protégés par basculement au sein du même centre de données. Dans notre cas, les centres de données se trouvent dans une même région Azure. L’article [Scénarios et architecture de haute disponibilité pour SAP](sap-high-availability-architecture-scenarios.md) donne un aperçu des différentes techniques de haute disponibilité et des recommandations proposées sur Azure pour les applications SAP, qui compléteront les instructions de cette section.

En fonction du résultat de dimensionnement de la plateforme SAP BOBI, vous devez concevoir le paysage et déterminer la distribution des composants décisionnels entre les machines virtuelles et les sous-réseaux Azure. Le niveau de redondance dans l’architecture distribuée dépend de l’objectif de délai de récupération (RTO) et de l’objectif de point de récupération (RPO) requis par l’entreprise. La plateforme SAP BOBI comprend différents niveaux et les composants de chaque niveau doivent être conçus de manière à assurer la redondance. Ainsi, en cas de défaillance d’un composant, votre application SAP BOBI sera peu ou pas interrompue. Par exemple :

- Serveurs d’applications redondants, tels que des serveurs d’applications décisionnelles et des serveurs web
- Composants uniques, tels qu’une base de données CMS, un magasin de fichiers et un équilibreur de charge

La section suivante explique comment atteindre la haute disponibilité sur chaque composant d’une plateforme SAP BOBI.

### <a name="high-availability-for-application-servers"></a>Haute disponibilité pour les serveurs d’applications

Les serveurs d’applications web et décisionnelles, qu’ils soient installés séparément ou ensemble, n’ont pas besoin d’une solution spécifique de haute disponibilité. Vous pouvez obtenir la haute disponibilité par redondance, c’est-à-dire en configurant plusieurs instances des serveurs d’applications web et décisionnelles sur diverses machines virtuelles Azure. Vous pouvez déployer les machines virtuelles dans des [groupes à haute disponibilité](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set) ou des [zones de disponibilité](sap-high-availability-architecture-scenarios.md#azure-availability-zones) en fonction du RTO requis pour l’entreprise. Pour un déploiement entre plusieurs zones de disponibilité, assurez-vous que tous les autres composants de la plateforme SAP BOBI sont conçus pour être également redondants interzones.

Actuellement, certaines régions Azure n’offrent pas de zones de disponibilité. Vous devez donc adapter la stratégie de déploiement basée sur votre région. Les régions Azure qui offrent des zones sont listées dans [Zones de disponibilité Azure](../../../availability-zones/az-overview.md).

> [!Important]
> Les concepts de zones de disponibilité Azure et de groupes à haute disponibilité Azure s’excluent mutuellement. Cela signifie que vous pouvez déployer deux machines virtuelles ou plus dans une zone de disponibilité spécifique ou dans un groupe à haute disponibilité Azure, mais pas dans les deux.

### <a name="high-availability-for-the-cms-database"></a>Haute disponibilité pour la base de données CMS

Si vous utilisez une base de données Azure comme solution pour vos bases de données CMS et d’audit, une infrastructure de haute disponibilité localement redondante est fournie par défaut. Sélectionnez les capacités de haute disponibilité, de redondance et de résilience inhérentes au service et à la région, sans avoir à configurer de composants supplémentaires. Si la stratégie de déploiement d’une plateforme SAP BOBI repose sur une zone de disponibilité, veillez à assurer la redondance de zone pour vos bases de données CMS et d’audit. Pour plus d’informations sur la haute disponibilité pour les offres de base de données prises en charge dans Azure, consultez [Haute disponibilité pour Azure SQL Database](../../../azure-sql/database/high-availability-sla.md) et [Haute disponibilité dans Azure Database pour MySQL](../../../mysql/concepts-high-availability.md). 

Pour un autre déploiement d’un système de gestion de base de données (SGBD) pour une base de données CMS, consultez [Guides de déploiement SGBD pour une charge de travail SAP](dbms_guide_general.md) afin d’obtenir des informations sur un déploiement SGBD différent et son approche pour mettre en place la haute disponibilité.

### <a name="high-availability-for-filestore"></a>Haute disponibilité pour le magasin de fichiers

Le terme « magasin de fichiers » désigne les répertoires de disque où sont stockés les contenus tels que les rapports, les univers et les connexions. Il est partagé par tous les serveurs d’applications de ce système. Vous devez donc vous assurer qu’il est hautement disponible, tout comme les autres composants de la plateforme SAP BOBI.

Pour une plateforme SAP BOBI s’exécutant sur Windows, vous pouvez choisir [Azure Premium Files](../../../storage/files/storage-files-introduction.md) ou [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) comme magasin de fichiers, qui est conçu pour être hautement disponible et hautement durable par nature. Azure Premium Files prend en charge le stockage ZRS, qui peut être utile pour le déploiement entre zones d’une plateforme SAP BOBI. Pour plus d’informations, consultez la section [Redondance](../../../storage/files/storage-files-planning.md#redundancy) pour Azure Files.

Comme le service de partage de fichiers n’est pas disponible dans toutes les régions, veillez à consulter la liste des [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/) pour bénéficier d’informations à jour. Si le service n’est pas disponible dans votre région, vous pouvez créer un serveur NFS à partir duquel vous pourrez partager le système de fichiers avec une application SAP BOBI. Toutefois, vous devrez également tenir compte de sa haute disponibilité.

### <a name="high-availability-for-the-load-balancer"></a>Haute disponibilité pour l’équilibreur de charge

Pour distribuer le trafic sur un serveur web, vous pouvez utiliser Load Balancer ou Application Gateway. La redondance de l’un ou l’autre équilibreur de charge peut être obtenue en fonction de la référence SKU que vous choisissez pour le déploiement :

* **Load Balancer** : la redondance peut être obtenue en configurant le serveur front-end Standard Load Balancer comme redondant interzone. Pour plus d’informations, consultez [Standard Load Balancer et zones de disponibilité](../../../load-balancer/load-balancer-standard-availability-zones.md).
* **Application Gateway** : la haute disponibilité peut être obtenue en fonction du type de niveau sélectionné au cours du déploiement :
   * La référence SKU v1 prend en charge les scénarios de haute disponibilité lorsque vous avez déployé deux instances ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir qu'elles n’échouent pas toutes en même temps. Avec cette référence SKU, la redondance peut être obtenue au sein de la zone.
   * La référence SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez une redondance de zone, les instances les plus récentes sont également réparties sur les zones de disponibilité pour offrir une résilience zonale en cas d’échec. Pour plus d’informations, consultez [Application Gateway v2 avec mise à l’échelle automatique et redondance interzone](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md).

### <a name="reference-high-availability-architecture-for-the-sap-businessobjects-bi-platform"></a>Architecture à haute disponibilité de référence pour la plateforme SAP BusinessObjects BI

L’architecture de référence suivante décrit l’installation d’une plateforme SAP BOBI entre les zones de disponibilité s’exécutant sur un serveur Windows Server. L’architecture présente l’utilisation de différents services Azure comme Application Gateway, Azure Premium Files (magasin de fichiers) et SQL Database (bases de données CMS et d’audit). La plateforme SAP BOBI offre une redondance de zone intégrée, ce qui réduit la complexité de la gestion de différentes solutions à haute disponibilité.

Dans la figure ci-dessous, le trafic entrant (HTTPS - TCP/443) est équilibré à l’aide de la référence SKU Application Gateway v2, qui s’étend sur plusieurs zones de disponibilité. La passerelle applicative distribue la requête de l’utilisateur entre les serveurs web, qui sont distribués entre les zones de disponibilité. Le serveur web transfère la demande aux instances de serveur de gestion et de traitement qui sont déployées dans des machines virtuelles distinctes entre différentes zones de disponibilité. Les fichiers Azure Premium avec stockage ZRS sont attachés via une liaison privée à des machines virtuelles de niveau de stockage et de gestion pour accéder à du contenu tel que des rapports, un univers et des connexions. L’application accède aux bases de données CMS et d’audit s’exécutant sur une instance redondante interzone de SQL Database, qui réplique les bases de données sur plusieurs emplacements physiques dans une région Azure.

![Diagramme illustrant une architecture à haute disponibilité pour une plateforme SAP BOBI sur Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

L’architecture précédente fournit des informations sur la façon dont un déploiement SAP BOBI sur Azure peut être effectué. Toutefois, elle ne couvre pas toutes les options de configuration possibles pour une plateforme SAP BOBI sur Azure. Vous pouvez adapter votre déploiement en fonction de vos besoins professionnels, en choisissant différents produits ou services pour des composants tels que Load Balancer, File Repository Server et SGBD.

Si des zones de disponibilité ne sont pas disponibles dans votre région sélectionnée, vous pouvez déployer des machines virtuelles Azure dans des groupes à haute disponibilité. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. Si une défaillance matérielle ou logicielle se produit, seul un sous-ensemble de vos machines virtuelles est affecté et la solution globale reste opérationnelle.

## <a name="disaster-recovery"></a>Récupération d'urgence

Cette section explique la stratégie permettant de fournir une protection de reprise d’activité après sinistre pour une plateforme SAP BOBI. Elle complète le document [Reprise d’activité après sinistre pour SAP](../../../site-recovery/site-recovery-sap.md), qui représente les principales ressources d’une approche globale de la reprise d’activité après sinistre SAP. Pour la plateforme SAP BOBI, consultez la note SAP [2056228](https://launchpad.support.sap.com/#/notes/2056228), qui décrit les méthodes suivantes permettant d’implémenter en toute sécurité un environnement de reprise d’activité après sinistre :

 * Utiliser pleinement ou sélectivement la gestion du cycle de vie (LCM) ou la fédération pour promouvoir ou distribuer le contenu à partir du système principal.
 * Copier périodiquement le contenu CMS et FRS.

Dans ce guide, nous allons nous pencher sur la deuxième option d’implémentation d’un environnement de reprise d’activité après sinistre. Nous ne couvrirons pas une liste exhaustive de toutes les options de configuration possibles de reprise d’activité après sinistre. Nous couvrirons une solution qui intègre les services Azure natifs en association avec la configuration de la plateforme SAP BOBI.

>[!Important]
>La disponibilité de chaque composant sur la plateforme SAP BOBI doit être factorisée dans la région secondaire. L’intégralité de la stratégie de reprise d’activité après sinistre doit être testée minutieusement.

### <a name="reference-dr-architecture-for-an-sap-businessobjects-bi-platform"></a>Architecture de reprise d’activité après sinistre de référence pour une plateforme SAP BusinessObjects BI

Cette architecture de référence exécute un déploiement multi-instance de la plateforme SAP BOBI avec des serveurs d’applications redondants. Pour la reprise d’activité après sinistre, vous devez basculer tous les composants de la plateforme SAP BOBI vers une région secondaire. Dans l’illustration suivante, Azure Premium Files est utilisé comme magasin de fichiers, SQL Database est utilisé comme référentiel CMS et d’audit, et Application Gateway est utilisé pour équilibrer la charge du trafic. La stratégie visant à assurer la protection par reprise d’activité après sinistre pour chaque composant est différente. Elle est décrite dans la section suivante.

![Diagramme montrant la reprise d’activité après sinistre de la plateforme SAP BusinessObjects BI pour Windows](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

Load Balancer est utilisé pour distribuer le trafic entre les serveurs d’applications web d’une plateforme SAP BOBI. Dans Azure, vous pouvez utiliser Load Balancer ou Application Gateway pour équilibrer la charge du trafic entre les serveurs web. Afin de bénéficier de la reprise d’activité après sinistre pour les services d’équilibrage de charge, vous devez implémenter un autre équilibreur de charge ou passerelle applicative dans une région secondaire. Pour conserver la même URL après un basculement de reprise d’activité après sinistre, modifiez l’entrée dans DNS et pointez sur le service d’équilibrage de charge qui s’exécute dans la région secondaire.

### <a name="virtual-machines-that-run-web-and-bi-application-servers"></a>Machines virtuelles qui exécutent des serveurs d’applications web et décisionnelles

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) peut être utilisé pour répliquer des machines virtuelles exécutant des serveurs d’applications web et décisionnelles dans la région secondaire. Il réplique les serveurs et tous les disques managés attachés à ceux-ci dans la région secondaire. Ainsi, en cas de catastrophe ou de panne, vous pouvez facilement basculer vers votre environnement répliqué et continuer à travailler. Pour lancer la réplication de toutes les machines virtuelles d’application SAP vers le centre de données de reprise d’activité Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md).

### <a name="filestore"></a>Magasin de fichiers

Le magasin de fichiers est un répertoire de disque où sont stockés les fichiers réels comme les rapports et les documents décisionnels. Il est important que tous les fichiers du magasin de fichiers soient synchronisés avec la région de reprise d’activité. Selon le type de service de partage de fichiers que vous utilisez pour la plateforme SAP BOBI exécutée sur Windows, la stratégie nécessaire de reprise d’activité après sinistre doit être adoptée pour synchroniser le contenu. Par exemple :

- **Azure Premium Files** prend uniquement en charge LRS et ZRS. Pour la stratégie de récupération d’urgence des fichiers Azure Premium, vous pouvez utiliser [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) ou [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0) pour copier vos fichiers vers un autre compte de stockage dans une région différente. Pour plus d’informations, consultez [Récupération d’urgence et basculement de compte de stockage](../../../storage/common/storage-disaster-recovery-guidance.md).
- **Azure NetApp Files** fournit des volumes NFS et SMB, de sorte que tout outil de copie basé sur des fichiers peut être utilisé pour répliquer les données entre des régions Azure. Pour plus d’informations sur la façon de copier un volume Azure NetApp Files dans une autre région, consultez [Questions fréquentes (FAQ) sur Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

  Vous pouvez utiliser la réplication entre régions Azure NetApp Files, actuellement en [préversion](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/), qui utilise la technologie NetApp SnapMirror. Avec cette technologie, seuls les blocs modifiés sont envoyés sur le réseau dans un format efficace et compressé. Cette technologie propriétaire limite le volume de données nécessaire à la réplication dans les différentes régions, ce qui permet d’économiser les coûts de transfert de données. Elle réduit également la durée de réplication, ce qui vous permet d’obtenir un RPO inférieur. Pour plus d’informations, consultez [Configuration requise et considérations pour la réplication inter-région](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md).

### <a name="cms-database"></a>Base de données CMS

Les bases de données CMS et d’audit de la région de reprise d’activité après sinistre doivent être une copie des bases de données exécutées dans la région primaire. Selon le type de base de données, il est important de copier la base de données dans la région de reprise d’activité après sinistre en fonction du RTO et du RPO requis par l’entreprise. Cette section décrit les différentes options disponibles pour chaque solution de base de données dans Azure, pris en charge pour une application SAP BOBI exécutée sur Windows.

#### <a name="azure-sql-database"></a>Azure SQL Database

Pour une stratégie de reprise d’activité après sinistre [SQL Database](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md), deux options sont disponibles pour copier la base de données dans la région secondaire. Les deux options de récupération offrent différents niveaux de RTO et de RPO. Pour plus d’informations sur le RTO et le RPO pour chaque option de récupération, consultez [Récupérer une base de données sur un serveur existant](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server).

Option 1 : [Restauration de la sauvegarde de base de données géoredondante](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   Par défaut, SQL Database stocke les données dans des [objets blob GRS](../../../storage/common/storage-redundancy.md) qui sont répliqués dans une [région jumelée](../../../best-practices-availability-paired-regions.md). Pour une base de données SQL, la redondance du stockage de sauvegarde peut être configurée au moment de la création des bases de données CMS et d’audit, ou peut être mise à jour pour une base de données existante. Les modifications apportées à une base de données existante s’appliquent uniquement aux sauvegardes ultérieures. Vous pouvez restaurer une base de données sur toute base de données SQL dans n’importe quelle région Azure à partir des dernières sauvegardes géorépliquées. La géorestauration utilise une sauvegarde géorépliquée comme source. Il peut y avoir un délai entre le moment où une sauvegarde est effectuée et celui où elle est géorépliquée dans un objet blob Azure dans une autre région. C’est pourquoi la base de données restaurée peut avoir jusqu’à une heure de retard par rapport à la base de données d’origine.

   >[!Important]
   >La géorestauration est disponible pour les bases de données SQL configurées avec un [stockage de sauvegarde](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy) géoredondant.

Option 2 : [Géoréplication](../../../azure-sql/database/active-geo-replication-overview.md) ou [groupe de basculement automatique](../../../azure-sql/database/auto-failover-group-overview.md)

   La géoréplication est une fonctionnalité SQL Database qui vous permet de créer des bases de données secondaires lisibles de bases de données individuelles sur un serveur situé dans la même région ou dans une autre région. Si la géoréplication est activée pour les bases de données CMS et d’audit, l’application peut lancer le basculement vers une base de données secondaire dans une autre région Azure. La géoréplication est activée pour des bases de données individuelles, mais pour activer le basculement transparent et coordonné de plusieurs bases de données (CMS et audit) pour une application SAP BOBI, il est recommandé d’utiliser un groupe de basculement automatique. Il fournit la sémantique de groupe en plus de la géoréplication active, ce qui signifie que l’intégralité du serveur SQL (toutes les bases de données) est répliquée vers une autre région au lieu de bases de données individuelles. Vérifiez le tableau des fonctionnalités qui [compare la géoréplication aux groupes de basculement](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups).

   Les groupes de basculement automatique fournissent des points de terminaison d’écouteur de lecture-écriture et de lecture seule qui restent inchangés lors du basculement. Le point de terminaison de lecture-écriture peut être conservé en tant qu’écouteur dans l’entrée de connexion ODBC pour les bases de données CMS et d’audit. Ainsi, que vous utilisiez l’activation manuelle ou automatique du basculement, ce dernier bascule toutes les bases de données secondaires du groupe en bases de données primaires. Une fois le basculement des bases de données terminé, l’enregistrement DNS est automatiquement mis à jour pour rediriger les points de terminaison vers la nouvelle région. L’application est automatiquement connectée à la base de données CMS, car le point de terminaison de lecture/écriture est conservé en tant qu’écouteur dans la connexion ODBC.

   Dans l’illustration suivante, un groupe de basculement automatique pour le serveur SQL (azussqlbodb) exécuté dans la région USA Est 2 est répliqué dans la région secondaire USA Est (site de reprise d’activité après sinistre). Le point de terminaison de l’écouteur de lecture/écriture est conservé en tant qu’écouteur dans une connexion ODBC pour le serveur d’applications décisionnelles s’exécutant sur Windows. Après basculement, le point de terminaison reste le même. Aucune intervention manuelle n’est requise pour connecter l’application décisionnelle à la base de données SQL dans la région secondaire.

   ![Capture d’écran montrant les groupes de basculement automatique SQL Database](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   Cette option fournit un RTO et un RPO inférieurs à l’option 1. Pour plus d’informations sur cette option, consultez [Utiliser des groupes de basculement automatique pour permettre le basculement transparent et coordonné de plusieurs bases de données](../../../azure-sql/database/auto-failover-group-overview.md).

#### <a name="azure-database-for-mysql"></a>Azure Database pour MySQL

Azure Database pour MySQL offre des options pour récupérer une base de données en cas de sinistre. Choisissez l’option qui convient le mieux à votre entreprise :

* Activez les réplicas en lecture entre régions pour améliorer la planification de la continuité d’activité et de la reprise d’activité après sinistre. Vous pouvez effectuer une réplication à partir d’un serveur source vers cinq réplicas au maximum. Les réplicas en lecture sont mis à jour de façon asynchrone à l’aide de la technologie de réplication des journaux binaires Azure Database pour MySQL. Les réplicas sont de nouveaux serveurs que vous gérez de manière similaire aux serveurs Azure Database pour MySQL classiques. Pour plus d’informations sur les réplicas en lecture, les régions disponibles, les restrictions et le basculement, consultez [Réplicas en lecture dans Azure Database pour MySQL](../../../mysql/concepts-read-replicas.md).

* Utilisez la fonctionnalité de géorestauration d’Azure Database pour MySQL qui restaure le serveur à l’aide de sauvegardes géoredondantes. Ces sauvegardes sont accessibles même lorsque la région dans laquelle votre serveur est hébergé est hors connexion. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région et remettre votre serveur en ligne.

  > [!Important]
  > La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes. La modification des options de redondance des sauvegardes après la création du serveur n’est pas prise en charge. Pour plus d’informations, consultez [Redondance des sauvegardes](../../../mysql/concepts-backup.md#backup-redundancy-options).

Le tableau suivant liste les recommandations relatives de reprise d’activité après sinistre pour chaque niveau utilisé dans cet exemple.

| Niveaux de la plateforme SAP BOBI                          | Recommandation                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure Application Gateway ou Azure Load Balancer | Configuration parallèle d’Application Gateway dans une région secondaire    |
| Serveurs d'applications web                          | Réplication à l’aide d’Azure Site Recovery                             |
| Serveurs d’applications décisionnelles                           | Répliquer à l’aide de Site Recovery                             |
| Fichiers Azure Premium                              | AzCopy *ou* Azure PowerShell                               |
| Azure NetApp Files                               | Outil de copie basé sur des fichiers pour répliquer les données dans une région secondaire *ou* réplication entre régions Azure NetApp Files (préversion) |
| Azure SQL Database                               | Géoréplication/groupes de basculement automatique *ou* géorestauration     |
| Azure Database pour MySQL                         | Réplicas en lecture entre régions *ou* restauration de sauvegarde à partir de sauvegardes géoredondantes |

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la reprise d’activité pour un déploiement d’application SAP multiniveau](../../../site-recovery/site-recovery-sap.md)
- [Planification et implémentation de machines virtuelles Azure pour SAP](planning-guide.md)
- [Déploiement de machines virtuelles Azure pour SAP](deployment-guide.md)
- [Déploiement SGBD de machines virtuelles Azure pour SAP](./dbms_guide_general.md)
