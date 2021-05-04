---
title: Déploiement de la plateforme décisionnelle SAP BusinessObjects sur Azure pour Windows | Microsoft Docs
description: Déployer et configurer le plateforme décisionnelle SAP BusinessObjects sur Azure pour Windows
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: a13b62da91e3ce6f18ba4dcda2b3b0f22c815026
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144382"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Guide de déploiement de la plateforme SAP BusinessObjects BI pour Windows sur Azure

Cet article décrit la stratégie de déploiement de la plateforme SAP BusinessObjects BI sur Azure pour Windows. Dans cet exemple, deux machines virtuelles avec Disques managés SSD Premium comme répertoire d’installation sont configurées. Azure SQL Database (offre PaaS) est utilisé pour le service de gestion centralisée (CMS) et la base de données d’audit, ainsi que pour les fichiers Azure Premium (protocole SMB) en tant que magasin de fichiers partagé entre les deux machines virtuelles. L’application web Java par défaut Tomcat et l’application de plateforme BI sont installées ensemble sur les deux machines virtuelles. Pour équilibrer la charge de la demande de l’utilisateur, Azure Application Gateway est utilisé avec des capacités de déchargement TLS/SSL natives.

Ce type d’architecture est efficace pour un déploiement de petite taille ou un environnement de non production. Pour un déploiement de production ou à grande échelle, vous pouvez avoir des hôtes distincts pour l’application web ainsi que plusieurs hôtes d’applications BOBI permettant au serveur de traiter plus d’informations.

![Déploiement de SAP BOBI sur Azure pour Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

Dans cet exemple, la version du produit et la disposition du système de fichiers ci-dessous sont utilisées :

- SAP BusinessObjects Platform 4.3 SP01 Patch 1
- Windows Server 2019
- Azure SQL Database (Version : 12.0.2000.8)
- Pilote ODBC Microsoft - msodbcsql.msi (version : 13.1)

| Système de fichiers        | Description                                                                                                               | Taille (Go)             | Accès requis  | Stockage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | Système de fichiers pour l’installation de l’instance SAP BOBI, application web Tomcat par défaut et pilotes de base de données (le cas échéant) | Recommandations de dimensionnement SAP | Privilèges d’administration locaux | Disque Premium managé – SSD |
| \\\azusbobi.file.core.windows.net\frsinput  | Le répertoire de montage est destiné aux fichiers partagés entre tous les hôtes BOBI qui seront utilisés en tant que répertoire de magasin de fichiers d’entrée | Besoins de l’entreprise         | Privilèges d’administration locaux | Azure NetApp Files         |
| \\\azusbobi.file.core.windows.net\frsoutput | Le répertoire de montage est destiné aux fichiers partagés entre tous les hôtes BOBI qui seront utilisés en tant que répertoire de magasin de fichiers de sortie | Besoins de l’entreprise         | Privilèges d’administration locaux | Azure NetApp Files         |

## <a name="deploy-windows-virtual-machine-via-azure-portal"></a>Déployer une machine virtuelle Windows via le portail Azure

Dans cette section, nous allons créer deux machines virtuelles avec l’image du système d’exploitation Windows pour la plateforme SAP BOBI. Les principales étapes pour créer des machines virtuelles sont les suivantes :

1. Créez un [groupe de ressources](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups).

2. Créez un [réseau virtuel](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - N’utilisez pas un sous-réseau unique pour tous les services Azure dans le déploiement de la plateforme SAP BI. En fonction de l’architecture de la plateforme SAP BI, vous devez créer plusieurs sous-réseaux. Dans ce déploiement, nous allons créer deux sous-réseaux : un sous-réseau d’application BI et un sous-réseau de passerelle applicative.
   - Suivez la note SAP [2276646](https://launchpad.support.sap.com/#/notes/2276646) pour identifier les ports de communication de SAP BusinessObjects BI Platform sur différents composants.
   - Azure SQL Database communique par le biais du port 1433. Le trafic sortant sur le port 1433 doit donc être autorisé à partir de vos serveurs d’applications SAP BOBI.
   - Dans Azure, Application Gateway doit se trouver sur un sous-réseau distinct. Consultez l’article [Azure Application Gateway](../../../application-gateway/configuration-overview.md) pour plus de détails.
   - Si vous utilisez Azure NetApp Files pour le magasin de fichiers au lieu d’Azure Files, vous devez créer un sous-réseau distinct pour Azure NetApp Files. Pour plus d’informations, consultez les [Consignes pour planifier un réseau Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-network-topologies.md).

3. Créez un groupe à haute disponibilité.

   - Pour obtenir une redondance pour chaque niveau dans le cadre d’un déploiement à plusieurs instances, placez les machines virtuelles de chaque niveau dans un groupe à haute disponibilité. Veillez à séparer les groupes à haute disponibilité pour chaque niveau en fonction de votre architecture.

4. Créez la machine virtuelle 1 **(azuswinboap1).**

   - Vous pouvez utiliser l’image personnalisée ou choisir une image à partir de Place de marché Azure. Reportez-vous à [Déploiement d’une machine virtuelle à partir de Place de marché Azure pour SAP](deployment-guide.md) ou [Déploiement d’une machine virtuelle avec une image personnalisée pour SAP](deployment-guide.md) en fonction de vos besoins.

5. Créez la machine virtuelle 2 **(azuswinboap2).**
6. Ajoutez un disque SSD Premium. Il sera utilisé comme répertoire d’installation SAP BOBI.

## <a name="provision-azure-premium-files"></a>Approvisionner des fichiers Azure Premium

Avant de poursuivre la configuration d’Azure Files, familiarisez-vous avec la [documentation d’Azure Files](../../../storage/files/storage-files-introduction.md).

Azure Files propose des partages de fichiers Standard, hébergés sur un matériel équipé de disque HDD, et des partages de fichiers Premium, hébergés sur un matériel équipé de disque SSD. Pour le stockage de fichiers SAP BusinessObjects, nous vous recommandons d’utiliser des fichiers Azure Premium.

Les partages de fichiers Azure Premium sont disponibles avec la redondance locale et de zone dans un sous-ensemble de régions. Pour savoir si les partages de fichiers Premium sont actuellement disponibles dans votre région, consultez la page des [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage) pour Azure. Pour plus d’informations sur les régions qui prennent en charge ZRS, consultez [Redondance de Stockage Azure](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Déployer un compte de stockage de fichiers Azure et des partages NFS

Les partages de fichiers Azure sont déployés dans des comptes de stockage, qui sont des objets de niveau supérieur représentant un pool partagé de stockage. Ce pool de stockage peut être utilisé pour déployer plusieurs partages de fichiers. Azure prend en charge plusieurs types de comptes de stockage pour les différents scénarios de stockage que les clients peuvent avoir, mais pour le magasin de fichiers SAP BusinessObjects, vous devez créer un compte de stockage **FileStorage**. Il vous permettra de déployer des partages de fichiers Azure sur du matériel sur disque SSD Premium.

> [!NOTE]
> Les comptes FileStorage peuvent uniquement être utilisés pour stocker des partages de fichiers Azure. Aucune autre ressource de stockage (conteneurs d’objets blob, files d’attente, tables, etc.) ne peut être déployée dans un compte FileStorage.

Le compte de stockage est accessible via un [point de terminaison privé](../../../storage/files/storage-files-networking-endpoints.md), déployé dans le même réseau virtuel que la plateforme SAP BOBI. Avec cette configuration, le trafic de votre système SAP ne quitte jamais les limites de sécurité du réseau virtuel. Les systèmes SAP contiennent souvent des données sensibles et critiques. Le fait de rester dans les limites du réseau virtuel est donc important pour de nombreux clients.

Si vous avez besoin d’accéder au compte de stockage à partir d’un autre réseau virtuel, vous pouvez utiliser le [peering de réseaux virtuels Azure](../../../virtual-network/virtual-network-peering-overview.md).

#### <a name="azure-files-storage-account"></a>Compte de stockage Azure Files

1. Pour créer un compte de stockage via le portail Azure, accédez à **Créer un compte** > **Stockage** > **Stockage de ressources**.

2. Dans l’onglet **Général**, renseignez tous les champs obligatoires pour créer un compte de stockage.

   a. Sélectionnez un **Abonnement**, un **Groupe de ressources** et une **Région**.

   b. Entrez le **Nom du compte de stockage** (par exemple, **azusbobi**). Ce nom doit être globalement unique, mais il peut s’agir du nom de votre choix.

   c. Sélectionnez **Premium** comme niveau de performance, et **FileStorage** en tant que type de compte.

   d. Pour Étiquette de réplication, choisissez Niveau de redondance. sélectionnez **Stockage localement redondant (LRS)**.

   Pour FileStorage Premium, le stockage localement redondant (LRS) et le stockage redondant interzone (ZRS) sont les seules options disponibles. Par conséquent, en fonction de votre stratégie de déploiement (groupe à haute disponibilité ou zone de disponibilité), choisissez le niveau de redondance approprié. Pour plus d’informations, consultez [Redondance de Stockage Azure](../../../storage/common/storage-redundancy.md).

   e. Sélectionnez Suivant.

3. Sous l’onglet **Réseau**, sélectionnez [Point de terminaison privé](../../../storage/files/storage-files-networking-endpoints.md) comme méthode de connectivité. Pour plus d’informations, consultez [Considérations relatives aux réseaux Azure Files](../../../storage/files/storage-files-networking-overview.md).

   a. Sélectionnez **Ajouter** dans la section Point de terminaison privé.

   b. Sélectionnez un **Abonnement**, un **Groupe de ressources** et un **Emplacement**.

   c. Entrez le **Nom** du point de terminaison privé (par exemple, azusbobi-pe).

   d. Sélectionnez **Fichier** dans **Sous-ressource de stockage**.

   e. Dans la section **Mise en réseau**, sélectionnez le **Réseau virtuel** et le **Sous-réseau** sur lesquels l’application SAP BusinessObjects BI est déployée.

   f. Acceptez la **Valeur par défaut (oui)** pour **Intégrer avec une zone DNS privée**.

   g. Sélectionnez votre **zone DNS privée** dans la liste déroulante.

   h. Sélectionnez **OK** pour revenir à l’onglet Mise en réseau dans Créer un compte de stockage.

4. Dans l’onglet **Protection de données**, configurez la stratégie de suppression réversible pour les partages de fichiers Azure dans votre compte de stockage. Par défaut, la fonctionnalité de suppression réversible est désactivée. Pour en savoir plus sur la suppression réversible, consultez [Empêcher la suppression accidentelle de partages de fichiers Azure](../../../storage/files/storage-files-prevent-file-share-deletion.md).

5. Sous l’onglet **Avancé**, vérifiez les différentes options de sécurité.

   **Transfert sécurisé requis** : ce champ indique si le compte de stockage nécessite un chiffrement en transit pour la communication avec le compte de stockage. Si vous avez besoin d’une aide par rapport au protocole SMB 2.1, vous devez désactiver ce champ. Pour la plateforme SAP BusinessObjects BI, conservez la **Valeur par défaut (activé)** .

6. Continuez et créez le compte de stockage.

Pour plus d’informations sur la création d’un compte de stockage, consultez [Créer un compte de stockage FileStorage](../../../storage/files/storage-how-to-create-file-share.md).

#### <a name="create-azure-file-shares"></a>Créer des partages de fichiers Azure

L’étape suivante consiste à créer une instance Azure Files dans le compte de stockage. Azure Files utilise un modèle provisionné pour les partages de fichiers Premium. Dans un modèle provisionné, vous spécifiez de manière proactive au service Azure Files vos besoins en matière de stockage au lieu de payer en fonction de votre utilisation. Pour en savoir plus sur ce modèle, consultez [Modèle provisionné](../../../storage/files/understanding-billing.md#provisioned-model). Dans cet exemple, nous créons deux instances Azure Files : frsinput (256 Go) et frsoutput (256 Go) pour le magasin de fichiers SAP BOBI.

Accédez au compte de stockage **azusbobi** > **Partages de fichiers**

1. Sélectionnez **Nouveau partage de fichiers**.
2. Entrez le **Nom** du partage de fichiers (par exemple frsinput, frsouput)
3. Insérez la taille de partage de fichiers requise dans **Capacité allouée** (par exemple, 256 Go)
4. Choisissez le **Protocole** **SMB**.
5. Sélectionnez **Create** (Créer).

## <a name="configure-data-disk-on-windows-virtual-machine"></a>Configurer un disque de données sur une machine virtuelle Windows

Les étapes de cette section utilisent les préfixes suivants :

**[A]**  : L’étape s’applique à tous les hôtes.

### <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

L’application SAP BusinessObjects BI requiert une partition sur laquelle ses fichiers binaires peuvent être installés. Vous pouvez installer l’application SAP BOBI sur la partition du système d’exploitation (C:), mais vous devez disposer de suffisamment d’espace pour le déploiement et le système d’exploitation. Il est recommandé de disposer d’au moins 2 Go disponibles pour les fichiers temporaires et les applications web. En tenant compte de tous ces points, il est recommandé de séparer les fichiers binaires d’installation SAP BOBI dans une partition distincte.

Dans cet exemple, l’application SAP BOBI est installée sur une partition distincte (F:). Initialisez le disque SSD Premium que vous avez attaché lors de l’approvisionnement de la machine virtuelle.

1. **[A]** Si aucun disque de données n’est attaché à la machine virtuelle (azuswinboap1 et azuswinboap2), suivez les étapes indiquées dans [Ajouter un disque](../../windows/attach-managed-disk-portal.md#add-a-data-disk) pour attacher un nouveau disque de données managé.
2. **[A]** Une fois que le disque managé est attaché à la machine virtuelle, initialisez le disque en suivant les étapes indiquées dans [Initialiser un nouveau disque de données](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk).

### <a name="mount-azure-premium-files"></a>Monter des fichiers Azure Premium

Pour utiliser un fichier Azure comme magasin de fichiers, vous devez le monter, ce qui signifie lui attribuer une lettre de lecteur ou un chemin de point de montage.

1. **[A]** Pour monter un partage de fichiers Azure, suivez les étapes décrites dans [Monter le partage de fichiers Azure](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share).

Pour monter le partage de fichiers Azure sur Windows Server, assurez-vous que le port 445 est ouvert. Le protocole SMB nécessite que le port TCP 445 soit ouvert, les connexions échoueront si ce port est bloqué. Vous pouvez vérifier si le pare-feu bloque le port 445 avec la cmdlet `Test-NetConnection` mentionnée dans le guide de [résolution des problèmes](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked).

## <a name="configure-cms-database---azure-sql"></a>Configurer une base de données CMS - Azure SQL

Cette section fournit des informations sur l’approvisionnement d’Azure SQL à l’aide du portail Azure. Elle fournit également des instructions sur la création de bases de données CMS et audit pour la plateforme SAP BOBI et d’un compte d’utilisateur pour accéder à la base de données.

Les instructions s’appliquent uniquement si vous utilisez Azure SQL (offre DBaaS sur Azure). Pour les autres bases de données, consultez la documentation SAP ou la documentation propre à la base de données.

### <a name="create-sql-database-server"></a>Créer un serveur SQL Database

Azure SQL Database offre différentes options de déploiement : base de données unique, pool élastique et serveur de base de données. Pour SAP BOBI, nous avons besoin de deux bases de données (CMS et Audit). Ainsi, au lieu de créer deux bases de données uniques, vous pouvez créer un serveur de base de données SQL qui peut gérer le groupe de bases de données uniques et de pools élastiques.  Voici les étapes de création d’un serveur de base de données SQL -

1. Accédez à la page [Sélectionner l’option de déploiement SQL](https://portal.azure.com/#create/Microsoft.AzureSQL).
2. Sous **Bases de données SQL**, modifiez **Type de ressource** en **Serveur de base de données**, puis sélectionnez **Créer**.
3. Dans l’onglet **Général**, renseignez tous les champs obligatoires pour **Créer un serveur de base de données SQL**.

   a. Sélectionnez l’**Abonnement** et le **Groupe de ressources** sous **Détails du projet**.

   b. Entrez le **Nom du serveur** (par exemple azussqlbodb). Ce nom de serveur doit être globalement unique, mais il peut s’agir du nom de votre choix.

   c. Sélectionnez un **Emplacement**.

   d. Entrez la **Connexion d’administrateur du serveur** (par exemple, boadmin) et le **Mot de passe**.

4. Sous l’onglet **Mise en réseau**, modifiez **Autoriser les services et les ressources Azure à accéder à ce serveur** sur **Non** sous **Règles de pare-feu**.
5. Dans **Paramètres supplémentaires**, conservez les paramètres par défaut.
6. Continuez et créez le **Serveur de base de données SQL**.

À l’étape suivante, créez les bases de données CMS et d’audit dans le serveur de base de données SQL (azussqlbodb.database.windows.net).

### <a name="create-the-cms-and-the-audit-database"></a>Créer les bases de données CMS et d’audit

Après la configuration du serveur de base de données SQL, accédez à la ressource **azussqlbodb**, puis suivez les étapes ci-dessous pour créer les bases de données CMS et d’audit.

1. Sur la page **Vue d’ensemble** d’azussqlbodb, sélectionnez **Créer une base de données**.
2. Dans l’onglet **Général**, renseignez tous les champs obligatoires -

   a. Entrez le **Nom de la base de données** (par exemple bocms ou boaudit).

   b. Dans l’option **Calcul + stockage**, sélectionnez **Configurer la base de données** et choisissez le modèle approprié en fonction de votre résultat de dimensionnement. Reportez-vous à [Dimensionnement des modèles pour Azure SQL Database](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database) pour obtenir des informations sur les options.
3. Sous l’onglet **Réseau**, sélectionnez [Point de terminaison privé](../../../private-link/tutorial-private-endpoint-sql-portal.md) comme méthode de connectivité. Le point de terminaison privé est utilisé pour accéder à la base de données Azure SQL au sein du réseau virtuel configuré.

   a. Sélectionnez **Ajouter un point de terminaison privé**.

   b. Sélectionnez un **Abonnement**, un **Groupe de ressources** et un **Emplacement**.

   c. Entrez le **Nom** du point de terminaison privé (par exemple, azusbodb-pe).

   e. Sélectionnez **SqlServer** dans **Sous-ressource cible**.

   f. Dans la section **Mise en réseau**, sélectionnez le **Réseau virtuel** et le **Sous-réseau** sur lesquels l’application SAP BusinessObjects BI est déployée.

   g. Acceptez la **Valeur par défaut (oui)** pour **Intégrer avec une zone DNS privée**.

   h. Sélectionnez votre **zone DNS privée** dans la liste déroulante.

   i. Sélectionnez **OK** pour revenir à l’onglet Mise en réseau dans Créer une base de données SQL.
4. Dans l’onglet **Paramètres supplémentaires**, modifiez le **Classement** en **SQL_Latin1_General_CP850_BIN2**.
5. Continuez et créez la base de données CMS.

De même, vous pouvez créer une base de données **Audit** (par exemple boaudit).

### <a name="download-and-install-odbc-driver"></a>Télécharger et installer le pilote ODBC

Pour que le serveur d’applications SAP BOBI accède à la base de données CMS ou d’audit, il requiert des clients/pilotes de base de données. Le pilote Microsoft ODBC est utilisé pour accéder aux bases de données CMS et d’audit qui s’exécutent sur la base de données SQL Azure. Cette section fournit des instructions sur le téléchargement et la configuration du pilote ODBC sur Windows. 

1. Reportez-vous à la section **Prise en charge du référentiel CMS + Audit par système d’exploitation** dans la [Matrice de disponibilité des produits pour la plateforme SAP BusinessObjects BI](https://support.sap.com/pam) pour découvrir les connecteurs de base de données compatibles avec Azure SQL Database.
2. Téléchargez la version du pilote ODBC à partir du [lien](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15). Dans cet exemple, nous téléchargeons le pilote ODBC [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131).
3. Installez le pilote ODBC sur tous les serveurs BI (azuswinboap1 et azuswinboap2).
4. Après l’installation du pilote dans **azuswinboap1**, accédez à **Démarrer** > **Outils d’administration Windows** > **Sources de données ODBC (64 bits)** .  
5. Accédez à l’onglet **DSN système**. 
6. Sélectionnez **Ajouter** pour créer une connexion à la base de données CMS.
7. Sélectionnez **ODBC Driver 13 for SQL Server**, puis **Terminer**.
8. Entrez les informations de votre base de données CMS comme ci-dessous, puis sélectionnez **Suivant**.
   - **Nom :** *nom de la base de données créée dans la section Créer une base de données CMS et audit* (par exemple, bocms ou boaudit)
   - **Description :** *Description de la source de données* (par exemple Base de données CMS ou Base de données d’audit)
   - **Serveur :** *Nom du serveur SQL créé dans la section Créer un serveur de base de données SQL* (par exemple azussqlbodb.database.windows.net)
9. Sélectionnez « **Avec l’authentification au serveur SQL à l’aide d’un ID de connexion et d’un mot de passe entrés par l’utilisateur** » pour vérifier l’authenticité auprès du serveur SQL. Entrez les informations d’identification d’utilisateur qui ont été créées au moment de la création du serveur de base de données Azure SQL (par exemple, boadmin), puis sélectionnez **Suivant**.
10. **Remplacez la base de données par défaut** par **bocms** et conservez tout le reste aux valeurs par défaut. Sélectionnez **Suivant**.
11. Cochez la case **Utiliser le chiffrement renforcé pour les données** et conservez tout le reste aux valeurs par défaut. Sélectionnez **Terminer**.
12. La source de données pour la base de données CMS a été créée et vous pouvez maintenant sélectionner **Tester la source de données** pour valider la connexion à la base de données CMS à partir de l’application BI. Le test doit se terminer correctement. En cas d’échec, [corrigez](../../../azure-sql/database/troubleshoot-common-errors-issues.md) le problème de connectivité.

>[!Note]
>Azure SQL Database communique par le biais du port 1433. Le trafic sortant sur le port 1433 doit donc être autorisé à partir de vos serveurs d’applications SAP BOBI.

Répétez les étapes ci-dessus pour créer une connexion pour la base de données d’audit sur le serveur azuswinboap1. Installez et configurez de la même manière les sources de données ODBC (bocms et boaudit) sur tous les serveurs d’applications BI (azuswinboap2). 

## <a name="server-preparation"></a>Préparation du serveur

Suivez le dernier guide de SAP pour préparer les serveurs pour l’installation de la plateforme BI. Pour obtenir les informations les plus récentes, reportez-vous à la section Préparation du [Guide d’installation de Business Intelligence Platform pour Windows](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html).

## <a name="installation"></a>Installation

Pour installer la plateforme BI sur un hôte Windows, connectez-vous avec un utilisateur disposant de privilèges d’administrateur local.

Accédez au support de la plateforme SAP BusinsessObjects BI et exécutez `setup.exe`.

Suivez le [Guide d’installation de SAP Business Intelligence Platform pour Windows](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html) correspondant à votre version. Quelques points à noter lors de l’installation de la plateforme SAP BOBI sur Windows. 

- Dans l’écran **Configurer le dossier de destination**, indiquez le dossier de destination dans lequel vous souhaitez installer la plateforme BI. (par exemple, F:\SAP BusinessObjects\). 

- Sur l’écran **Configure Product Registration** (Configurer l’enregistrement du produit), vous pouvez soit utiliser une clé de licence temporaire pour les solutions SAP BusinessObjects à partir de la note SAP [1288121](https://launchpad.support.sap.com/#/notes/1288121), soit générer une clé de licence dans SAP Service Marketplace.

- Sur l’écran **Select Install Type** (Sélectionner le type d’installation), sélectionnez **Full** (Installation complète) sur le premier serveur (azuswinboap1). Pour l’autre serveur (azuswinboap2), sélectionnez **Custom / Expand** (Personnaliser/développer) qui développera la configuration BOBI actuelle.

- Sur l’écran **Select Default or Existing Database** (Sélectionner un base de données existante ou par défaut), sélectionnez **Configure an existing database** (Configurer une base de données existante), ce qui vous invite à sélectionner la base de données CMS et audit. Sélectionnez **Microsoft SQL Server utilisant ODBC** pour le type de base de données CMS et le type de base de données d’audit.

  Vous pouvez également sélectionner Aucune base de données d’audit si vous ne souhaitez pas configurer l’audit pendant l’installation.

- Sélectionnez les options appropriées sur l’écran **Select Java Web Application Server** (Sélectionner le serveur d’applications web Java) en fonction de votre architecture SAP BOBI. Dans cet exemple, nous avons sélectionné l’option 1, qui installe le serveur Tomcat sur la même plateforme SAP BOBI.

- Entrez les informations de la base de données CMS dans **Configure CMS Repository Database - SQL Server (ODBC)** (Configurer la base de données du référentiel CMS – SQL Server (ODBC)). Exemple d’entrée pour les informations de base de données CMS pour l’installation de Windows.
  
  ![Informations sur la base de données CMS pour Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)

- (Facultatif) Entrez les informations de la base de données d’audit dans **Configure Audit Repository Database - SQL Server (ODBC)** (Configurer la base de données du référentiel d’audit – SQL Server (ODBC)). Exemple d’entrée pour les informations de base de données d’audit pour l’installation de Windows.
  
  ![Informations sur la base de données d’audit pour Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- Suivez les instructions et saisissez les entrées requises pour terminer l’installation.

Pour un déploiement à plusieurs instances, exécutez la configuration de l’installation sur le deuxième hôte (azuswinboap2). Sur l’écran **Select Install Type** (Sélectionner le type d’installation), sélectionnez **Custom / Expand** (Personnaliser/développer) qui développera la configuration BOBI actuelle. Consultez le billet de blog SAP sur l’[Installation de la plateforme SAP BusinessObjects Business Intelligence avec Azure SQL DB](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/)

> [!IMPORTANT]
> Les numéros de version du moteur de base de données pour SQL Server et Azure SQL Database ne sont pas comparables les uns aux autres. Il s’agit plutôt de numéros de build internes pour ces produits distincts. Le moteur de base de données pour Azure SQL Database repose sur la même base de code que le moteur de base de données SQL Server. Plus important encore, le moteur de base de données dans Azure SQL Database a toujours les bits les plus récents du moteur de base de données SQL. La version 12 d’Azure SQL Database est plus récente que la version 15 de SQL Server.

Pour déterminer la version actuelle de la base de données SQL Azure, vous pouvez vérifier les paramètres de la console de gestion centrale (CMC) ou exécuter la requête ci-dessous à l’aide de [sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) ou de [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15). L’alignement des versions de SQL avec la compatibilité par défaut est disponible dans l’article [Niveau de compatibilité de la base de données](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15).

![Informations de base de données dans CMC](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

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

Après l’installation de plusieurs instances de la plateforme SAP BOBI, d’autres étapes post-configuration doivent être effectuées pour prendre en charge la haute disponibilité des applications.

### <a name="configuring-cluster-name"></a>Configurer le nom du cluster

Dans un déploiement à plusieurs instances de la plateforme SAP BOBI, vous souhaitez exécuter plusieurs serveurs CMS ensemble dans un cluster. Un cluster se compose d’au moins deux serveurs CMS qui travaillent conjointement sur une base de données système CMS commune. Si un nœud qui s’exécute sur CMS échoue, un nœud avec un autre CMS continue à traiter les requêtes de la plateforme BI. Par défaut, dans la plateforme SAP BOBI, un nom de cluster reflète le nom d’hôte du premier CMS que vous installez. 

Pour configurer le nom du cluster sous Windows, suivez les instructions indiquées dans le [Guide de l’administrateur de la plateforme SAP Business Intelligence](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US). Après avoir configuré le nom du cluster, suivez la note SAP [1660440](https://launchpad.support.sap.com/#/notes/1660440) pour définir l’entrée système par défaut sur la page de connexion Launchpad CMC ou BI. 

### <a name="configure-input-and-output-filestore-location-to-azure-premium-files"></a>Configuration de l’emplacement du magasin de fichiers d’entrée et de sortie pour les fichiers Azure Premium

Le magasin de fichiers fait référence aux répertoires de disque où se trouvent les fichiers SAP BusinessObjects réels. L’emplacement par défaut du serveur de référentiel de fichiers pour la plateforme SAP BOBI se trouve dans le répertoire d’installation local. Dans un déploiement à plusieurs instances, il est important de configurer le magasin de fichiers sur un stockage partagé, comme des fichiers Azure Premium ou Azure NetApp Files, afin qu’il soit accessible à partir de tous les serveurs de niveau de stockage.

1. S’il n’est pas créé, suivez les instructions fournies dans la section ci-dessus > **Approvisionner des fichiers Azure Premium** pour créer et monter des fichiers Azure Premium.

   > [!Tip]
   > Choisissez la redondance de stockage pour les fichiers Azure Premium (LRS ou ZRS) en fonction du déploiement de vos machines virtuelles (groupe à haute disponibilité ou zone de disponibilité).

2. Suivez la note SAP [2512660](https://launchpad.support.sap.com/#/notes/0002512660) pour modifier le chemin d’accès du référentiel de fichiers (entrée et sortie).

### <a name="tomcat-clustering---session-replication"></a>Clustering Tomcat – réplication de session

Tomcat prend en charge le clustering d’au moins deux serveurs d’applications pour la réplication et le basculement de session. Les sessions de la plateforme SAP BOBI sont sérialisées, mais une session utilisateur peut basculer en toute transparence vers une autre instance de Tomcat, même en cas de défaillance d’un serveur d’applications. Par exemple, si un utilisateur est connecté à un serveur web qui échoue alors qu’il navigue dans l’arborescence des dossiers d’une application SAP BI. Sur un cluster correctement configuré, l’utilisateur peut continuer à naviguer dans l’arborescence des dossiers sans être redirigé vers la page de connexion.

Dans la note SAP [2808640](https://launchpad.support.sap.com/#/notes/2808640), les étapes de configuration du clustering Tomcat sont fournies pour l’utilisation de la multidiffusion. Toutefois, dans Azure, la multidiffusion n’est pas prise en charge. Pour que le cluster Tomcat fonctionne dans Azure, vous devez utiliser [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (note SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Consultez [Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) (Clustering Tomcat à l’aide de l’appartenance statique pour la plateforme décisionnelle SAP BusinessObjects) sur le blog SAP pour configurer le cluster Tomcat dans Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Équilibrage de la charge du niveau web de la plateforme SAP BI

Dans le cadre d’un déploiement multi-instance de SAP BOBI, les serveurs d’applications web Java (niveau web) sont exécutés sur au moins deux hôtes. Pour répartir uniformément la charge des utilisateurs sur les serveurs web, vous pouvez utiliser un équilibreur de charge entre les utilisateurs finaux et les serveurs web. Dans Azure, vous pouvez utiliser Azure Load Balancer ou Azure Application Gateway pour gérer le trafic vers vos serveurs d’applications web. Les détails de chaque offre sont expliqués dans la section suivante.

1. [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) est un équilibreur de charge de couche 4 (TCP, UDP) à haute performance et à faible latence qui distribue le trafic entre les machines virtuelles saines. Une sonde d’intégrité d’équilibreur de charge surveille un port donné sur chaque machine virtuelle et ne distribue le trafic qu’aux machines virtuelles opérationnelles. Vous pouvez choisir un équilibreur de charge public ou un équilibreur de charge interne, selon que vous souhaitez que la plateforme SAP BI soit accessible ou non à partir d’Internet. Azure Load Balancer est redondant interzone, garantissant une haute disponibilité de la fonctionnalité Zones de disponibilité.

   Reportez-vous à la section Équilibreur de charge interne de la figure ci-dessous, où le serveur d’applications web s’exécute sur le port 8080, port HTTP par défaut de Tomcat, qui sera analysé par la sonde d’intégrité. Par conséquent, toute requête entrante émanant des utilisateurs finaux sera redirigée vers les serveurs d’applications web (azuswinboap1 ou azuswinboap2) dans le pool principal. L’équilibreur de charge ne prend pas en charge la terminaison TLS/SSL (également appelé déchargement TLS/SSL). Si vous utilisez un équilibreur de charge Azure pour distribuer le trafic entre les serveurs web, nous vous recommandons d’utiliser Standard Load Balancer.

   > [!NOTE]
   > Lorsque des machines virtuelles sans adresse IP publique sont placées dans le pool principal d’Azure Standard Load Balancer interne (aucune adresse IP publique), il n’y a pas de connectivité Internet sortante, sauf si une configuration supplémentaire est effectuée pour autoriser le routage vers des points de terminaison publics. Pour savoir plus en détails comment bénéficier d’une connectivité sortante, voir [Connectivité des points de terminaison publics pour les machines virtuelles avec Azure Standard Load Balancer dans les scénarios de haute disponibilité SAP](high-availability-guide-standard-load-balancer-outbound-connections.md).

   ![Azure Load Balancer pour équilibrer le trafic entre les serveurs web](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

2. [Azure Application Gateway](../../../application-gateway/overview.md) fournit le contrôleur de livraison d’applications (ADC) en tant que service, qui est utilisé pour aider l’application à diriger le trafic des utilisateurs vers un ou plusieurs serveurs d’applications web. Il offre différentes capacités d’équilibrage de charge de couche 7, notamment le déchargement TLS/SSL, le pare-feu d’application web (WAF), l’affinité de session basée sur les cookies et d’autres pour vos applications.

   Dans la plateforme SAP BI, la passerelle applicative dirige le trafic web des applications vers les ressources spécifiées dans un pool principal : azuswinboap1 ou azuswinboap2. Vous attribuez un écouteur au port, créez des règles et ajoutez des ressources à un pool principal. Dans la figure ci-dessous, la passerelle applicative avec adresse IP frontale privée (10.31.3.25) agit comme point d’entrée pour les utilisateurs, gère les connexions TLS/SSL (HTTPS-TCP/443) entrantes, déchiffre le TLS/SSL et transmet la requête (HTTP-TCP/8080) aux serveurs du pool principal. Avec la fonctionnalité de terminaison TLS/SSL intégrée, nous devons simplement gérer un certificat TLS/SSL sur la passerelle applicative, ce qui simplifie les opérations.

   ![Application Gateway pour équilibrer le trafic entre les serveurs web](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   Afin de configurer Application Gateway pour le serveur web SAP BOBI, vous pouvez vous reporter à [Load Balancing SAP BOBI Web Servers using Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) (Équilibrage de la charge des serveurs web SAP BOBI à l’aide d’Azure Application Gateway) sur le blog SAP.

   > [!NOTE]
   > Nous vous recommandons d’utiliser Azure Application Gateway pour équilibrer la charge du trafic vers le serveur web, car il offre des fonctionnalités similaires comme le déchargement SSL, la gestion centralisée du SSL pour réduire les frais de chiffrement et de déchiffrement sur le serveur, l’algorithme de tourniquet (round robin) pour distribuer le trafic, des capacités de pare-feu d’applications web (WAF), la haute disponibilité, entre autres.

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Fiabilité de la plateforme décisionnelle SAP BusinessObjects sur Azure

La plateforme décisionnelle SAP BusinessObjects comprend différents niveaux, qui sont optimisés pour des tâches et des opérations spécifiques. Lorsque le composant d’un niveau quelconque devient indisponible, l’application SAP BOBI devient inaccessible ou certaines fonctionnalités de l’application ne fonctionnent pas. Vous devez donc vous assurer que chaque niveau est conçu pour être fiable afin de garantir le bon fonctionnement de l’application sans aucune interruption de l’activité.

Ce guide explique comment les fonctionnalités natives d’Azure en association avec la configuration de la plateforme SAP BOBI améliorent la disponibilité du déploiement SAP. Cette section se concentre sur les options suivantes pour la plateforme SAP BOBI sur Azure -

- **Sauvegarde et restauration** : un processus qui consiste à créer des copies périodiques des données et des applications vers un emplacement distinct. Il est donc possible de restaurer ou de revenir à l’état antérieur si les données ou les applications d’origine sont perdues ou endommagées.

- **Haute disponibilité :** Une plateforme à haute disponibilité dispose d’au moins deux copies de chaque élément dans la région Azure pour que l’application reste opérationnelle si l’un des serveurs n’est plus disponible.
- **Reprise d’activité après sinistre :** Il s’agit du processus de restauration des fonctionnalités de votre application en cas de perte irrécupérable, par exemple si l’intégralité de la région Azur devenait indisponible en raison d’une catastrophe naturelle.

L’implémentation de cette solution varie selon la nature de la configuration du système dans Azure. Le client doit donc adapter la solution de sauvegarde/restauration, haute disponibilité et récupération d’urgence en fonction de ses besoins professionnels.

## <a name="back-up-and-restore"></a>Sauvegarde et restauration

La sauvegarde et la restauration sont un processus qui consiste à créer des copies périodiques des données et des applications vers un emplacement distinct. Il est donc possible de restaurer ou de revenir à l’état antérieur si les données ou les applications d’origine sont perdues ou endommagées. Il s’agit également d’un composant essentiel de toute stratégie de récupération d’urgence. Ces sauvegardes permettent de restaurer l’application et la base de données à un point dans le temps pendant la période de rétention configurée.

Pour développer une stratégie complète de sauvegarde et de restauration pour la plateforme SAP BOBI, identifiez les composants qui entraînent un temps d’arrêt du système ou une interruption de l’application. Dans la plateforme SAP BOBI, la sauvegarde des composants suivants est essentielle pour protéger l’application :

- Répertoire d’installation de SAP BOBI (Disques Premium managés)
- Magasin de fichiers (fichiers Azure Premium ou Azure NetApp Files pour une installation distribuée)
- Base de données CMS et d’audit (base de données SQL Azure, base de données Azure pour MySQL ou base de données sur une machine virtuelle Azure)

La section suivante décrit comment implémenter une stratégie de sauvegarde et de restauration pour chaque composant sur la plateforme SAP BOBI.

### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Sauvegarder et restaurer le répertoire d’installation de SAP BOBI

Dans Azure, la méthode la plus simple pour sauvegarder des machines virtuelles et tous les disques attachés consiste à utiliser le service [Sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vms-introduction.md). Il propose des sauvegardes indépendantes et isolées pour éviter une destruction involontaire des données de vos machines virtuelles. Les sauvegardes sont stockées dans un coffre Recovery Services avec gestion intégrée des points de récupération. La configuration et la mise à l’échelle sont simples, les sauvegardes sont optimisées et peuvent être facilement restaurées le cas échéant.

Dans le cadre du processus de sauvegarde, un instantané est créé et les données sont transférées vers le coffre Recovery Services sans impact sur les charges de travail de production. L’instantané offre différents niveaux de cohérence, comme décrit dans l’article [Cohérence des instantanés](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency). La sauvegarde Azure offre également une prise en charge côte à côte pour la sauvegarde des disques managés à l’aide de la [sauvegarde des disques Azure](../../../backup/disk-backup-overview.md) en plus des solutions de [sauvegarde des machines virtuelles Azure](../../../backup/backup-azure-vms-introduction.md). Cela est utile lorsque vous avez besoin de sauvegardes cohérentes de machines virtuelles une fois par jour et de sauvegardes plus fréquentes des disques du système d’exploitation, ou d’un disque de données spécifique cohérent en cas de panne. Pour plus d’informations, consultez [Sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vms-introduction.md), [Sauvegarde de disques Azure](../../../backup/disk-backup-overview.md) et [FAQ - Sauvegarde de machines virtuelles Azure](../../../backup/backup-azure-vm-backup-faq.yml).

### <a name="backup--restore-for-filestore"></a>Sauvegarder et restaurer un magasin de fichiers

En fonction de votre déploiement, le magasin de fichiers de la plateforme SAP BOBI peut se trouver sur Azure NetApp Files ou Azure Files. Choisissez parmi les options suivantes pour la sauvegarde et la restauration en fonction du stockage que vous utilisez pour le magasin de fichiers.

1. Pour **Azure NetApp Files**, vous pouvez créer des instantanés à la demande et planifier un instantané automatique en utilisant des stratégies d’instantané. Les instantanés fournissent une copie à un moment donné de votre volume ANF. Pour plus d’informations, consultez [Gérer les instantanés avec Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).
2. La sauvegarde **Azure Files** est intégrée au service [Sauvegarde Azure](../../../backup/backup-overview.md) natif, qui centralise la fonction de sauvegarde et de restauration ainsi que la sauvegarde de machines virtuelles et simplifie le travail d’exploitation. Pour plus d’informations, consultez [Sauvegarde des partages de fichiers Azure](../../../backup/azure-file-share-backup-overview.md) et [Forum aux questions – Sauvegarde des fichiers Azure](../../../backup/backup-azure-files-faq.yml).

Si vous avez créé un serveur NFS distinct, veillez à implémenter la même stratégie de restauration et de sauvegarde.

### <a name="backup--restore-for-cms-and-audit-database"></a>Sauvegarder et restaurer la base de données CMS et d’audit

Pour la plateforme SAP BOBI s’exécutant sur des machines virtuelles Windows, les bases de données CMS et d’audit peuvent s’exécuter sur n’importe quelle base de données prise en charge, comme décrit dans la [matrice de prise en charge](businessobjects-deployment-guide.md#support-matrix) du guide de planification et d’implémentation de la plateforme SAP BusinessObjects BI sur Azure. Il est donc important que vous adoptiez la stratégie de sauvegarde et de restauration basée sur la base de données utilisée pour le magasin de données CMS et d’audit.

1. **Azure SQL Database** utilise la technologie SQL Server pour créer des [sauvegardes complètes](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15) (chaque semaine), [différentielles](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15) (toutes les 12-24 heures) et du [journal des transactions](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15) (toutes les 5 à 10 minutes). La fréquence des sauvegardes du journal des transactions est basée sur la taille de calcul et le volume d’activité de la base de données. L’utilisateur peut choisir une option pour configurer la redondance du stockage de sauvegarde entre des objets blobs localement redondants, redondants interzone et géoredondants. La redondance de stockage stocke toujours plusieurs copies de vos données afin qu’elles soient protégées contre des événements planifiés ou non, notamment des défaillances matérielles temporaires, des pannes de réseau ou de courant et des catastrophes naturelles majeures. Par défaut, la base de données Azure SQL stocke la sauvegarde dans des [objets blob de stockage](../../../storage/common/storage-redundancy.md) géoredondants qui sont répliqués dans une [région jumelée](../../../best-practices-availability-paired-regions.md). Vous pouvez la modifier en fonction des besoins de l’entreprise en objets blob de stockage localement redondants ou redondants interzone. Pour plus d’informations à jour sur la planification des sauvegardes de bases de données SQL Azure, la rétention et la consommation du stockage, consultez [Sauvegardes automatisées - Azure SQL Database et SQL Managed Instance](../../../azure-sql/database/automated-backups-overview.md)

2. **Azure Database pour MySQL** crée automatiquement des sauvegardes de serveur et les conserve dans un stockage localement redondant ou géoredondant configuré par l’utilisateur. Azure Database pour MySQL effectue des sauvegardes des fichiers de données et du journal des transactions. Selon la taille de stockage maximale prise en charge, elle effectue des sauvegardes complètes et différentielles (serveurs de stockage de 4 To) ou des sauvegardes d’instantané (serveurs de stockage jusqu’à 16 To). Celles-ci vous permettent de restaurer un serveur à n’importe quel point dans le temps pendant la période de conservation des sauvegardes configurée. Par défaut, la période de conservation des sauvegardes est de sept jours, mais vous pouvez [éventuellement la configurer](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) jusqu’à 35 jours. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES de 256 bits. Ces fichiers de sauvegarde ne sont pas exposés aux utilisateurs et ne peuvent pas être exportés. Ces sauvegardes sont utilisables uniquement pour les opérations de restauration dans Azure Database pour MySQL. Vous pouvez utiliser [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) pour copier une base de données. Pour plus d’informations, consultez [Sauvegarde et restauration dans Azure Database pour MySQL](../../../mysql/concepts-backup.md).

3. Pour une base de données installée sur une machine virtuelle Azure, vous pouvez utiliser les outils de sauvegarde standard ou le service [Sauvegarde Azure](../../../backup/sap-hana-db-about.md) pour les bases de données prises en charge. En outre, si les services et outils Azure ne répondent pas à vos besoins, vous pouvez utiliser des outils de sauvegarde tiers pris en charge qui fournissent un agent pour la sauvegarde et la récupération de tous les composants de la plateforme SAP BOBI.

## <a name="high-availability"></a>Haute disponibilité

Haute disponibilité désigne un ensemble de technologies qui peuvent réduire les interruptions du service informatique en assurant la continuité de l’activité des services ou des applications par le biais de composants redondants, tolérants aux pannes ou protégés par basculement au sein du même centre de données. Dans notre cas, les centres de données se trouvent dans une région Azure. L’article [Scénarios et architecture de haute disponibilité pour SAP](sap-high-availability-architecture-scenarios.md) donne un aperçu des différentes techniques de haute disponibilité et des recommandations proposées sur Azure pour les applications SAP, qui compléteront les instructions de cette section.

En fonction du résultat de dimensionnement de la plateforme SAP BOBI, vous devez concevoir le paysage et déterminer la distribution des composants décisionnels entre les machines virtuelles et les sous-réseaux Azure. Le niveau de redondance dans l’architecture distribuée dépend de l’objectif de délai de récupération (RTO) et de l’objectif de point de récupération (RPO) requis par l’entreprise. La plateforme SAP BOBI comprend différents niveaux, et les composants de chaque niveau doivent être conçus de manière à assurer la redondance. Ainsi, en cas de défaillance d’un composant, votre application SAP BOBI sera peu ou pas interrompue. Par exemple,

- Serveurs d’applications redondants, tels que les serveurs d’applications décisionnelles et le serveur web
- Composants uniques, tels que la base de données CMS, le magasin de fichiers ou l’équilibreur de charge

La section suivante décrit la façon d’atteindre une haute disponibilité sur chaque composant de la plateforme SAP BOBI.

### <a name="high-availability-for-application-servers"></a>Haute disponibilité pour les serveurs d’applications

Pour les serveurs d’applications web et décisionnelles, qu’ils soient installés séparément ou ensemble, il n’est pas nécessaire de disposer d’une solution spécifique de haute disponibilité. Vous pouvez obtenir une haute disponibilité par redondance, c’est-à-dire en configurant plusieurs instances de serveurs d’applications web et décisionnelles dans différentes machines virtuelles Azure. Vous pouvez déployer cette machine virtuelle dans des [groupes à haute disponibilité](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set) ou des [zones de disponibilité](sap-high-availability-architecture-scenarios.md#azure-availability-zones) en fonction du RTO requis pour l’entreprise. Pour le déploiement sur des zones de disponibilité, assurez-vous que tous les autres composants de la plateforme SAP BOBI sont également conçus pour la redondance de zone.

Actuellement, certaines régions Azure n’offrent pas de zones de disponibilité. Vous devez donc adapter la stratégie de déploiement en fonction de votre région. Les régions Azure qui offrent des zones sont listées dans [Zones de disponibilité Azure](../../../availability-zones/az-overview.md).

> [!Important]
> Les concepts de Zones de disponibilité Azure et de groupes à haute disponibilité Azure sont incompatibles. Cela signifie que vous pouvez déployer deux ou plusieurs machines virtuelles dans une zone de disponibilité ou dans un groupe à haute disponibilité Azure, mais pas dans les deux.

### <a name="high-availability-for-cms-database"></a>Haute disponibilité pour la base de données CMS

Si vous utilisez le service Azure Database en tant que service (DBaaS) pour les bases de données CMS et d’audit, l’infrastructure de haute disponibilité localement redondante est fournie par défaut. Il vous suffit de sélectionner la région et les capacités de haute disponibilité, de redondance et de résilience inhérentes au service, sans avoir à configurer de composants supplémentaires. Si la stratégie de déploiement de la plateforme SAP BOBI se fait sur une zone de disponibilité, vous devez vous assurer que vous obtenez une redondance de zone pour vos bases de données CMS et d’audit. Pour plus d’informations sur l’offre de haute disponibilité pour une offre DBaaS prise en charge dans Azure, consultez [haute disponibilité pour Azure SQL Database](../../../azure-sql/database/high-availability-sla.md) et la [Haute disponibilité dans Azure Database pour MySQL](../../../mysql/concepts-high-availability.md). 

Pour les autres déploiements SGBD pour la base de données CMS, reportez-vous à l’article [Guides de déploiement SGBD pour charge de travail SAP](dbms_guide_general.md), qui fournit des informations sur différents déploiements SGBD et leur approche pour atteindre une haute disponibilité.

### <a name="high-availability-for-filestore"></a>Haute disponibilité pour les magasins de fichiers

Le magasin de fichiers désigne les répertoires de disque pour le stockage de contenu comme des rapports, univers et connexions. Il est partagé par tous les serveurs d’applications de ce système. Vous devez donc vous assurer qu’il est hautement disponible, tout comme les autres composants de la plateforme SAP BOBI.

Pour la plateforme SAP BOBI s’exécutant sur Windows, vous pouvez choisir des [fichiers Azure Premium](../../../storage/files/storage-files-introduction.md) ou [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) pour le cache de fichiers, qui est conçu pour être hautement disponible et hautement durable par nature. Les fichiers Azure Premium prennent en charge le stockage redondant interzone, qui peut être utile pour le déploiement entre zones de la plateforme SAP BOBI. Pour plus d’informations, consultez la section [Redondance](../../../storage/files/storage-files-planning.md#redundancy) pour Azure Files.

Ce service de partage de fichiers n’étant pas disponible dans toutes les régions, veillez à consulter le site [Disponibilité des produits par région](https://azure.microsoft.com/en-us/global-infrastructure/services/) pour bénéficier d’informations à jour. Si le service n’est pas disponible dans votre région, vous pouvez créer un serveur NFS à partir duquel vous pourrez partager le système de fichiers avec l’application SAP BOBI. Toutefois, vous devrez également tenir compte de sa haute disponibilité.

### <a name="high-availability-for-load-balancer"></a>Haute disponibilité pour l’équilibreur de charge

Pour distribuer le trafic sur le serveur web, vous pouvez utiliser Azure Load Balancer ou Azure Application Gateway. La redondance de l’un ou l’autre des équilibreurs de charge peut être obtenue en fonction du niveau tarifaire que vous choisissez pour le déploiement.

1. Pour Azure Load Balancer, la redondance peut être obtenue en configurant le serveur frontal de Standard Load Balancer comme redondant interzone. Pour en savoir plus, consultez [Standard Load Balancer et Zones de disponibilité](../../../load-balancer/load-balancer-standard-availability-zones.md).
2. Pour Application Gateway, la haute disponibilité peut être obtenue en fonction du type de niveau sélectionné lors du déploiement.
   1. Le SKU v1 prend en charge les scénarios de haute disponibilité lorsque vous avez déployé deux instances ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir qu'elles n’échouent pas toutes en même temps. Ainsi, grâce à ce SKU, la redondance peut être obtenue au sein de la zone.
   2. Le SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez une redondance de zone, les instances les plus récentes sont également réparties sur les zones de disponibilité pour offrir une résilience zonale en cas d’échec. Pour plus de détails, consultez [Application Gateway v2 avec mise à l’échelle automatique et redondance interzone](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md).

### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Architecture haute disponibilité de référence pour la plateforme décisionnelle SAP BusinessObjects

L’architecture de référence ci-dessous décrit l’installation de la plateforme SAP BOBI entre les zones de disponibilité s’exécutant sur Windows Server. L’architecture présente l’utilisation de différents services Azure, comme Azure Application Gateway, les fichiers Azure Premium (magasin de fichiers) et les bases de données Azure SQL (bases de données CMS et d’audit) pour la plateforme SAP BOBI, qui offre une redondance de zone intégrée, ce qui réduit la complexité de la gestion de différentes solutions de haute disponibilité.

Dans la figure ci-dessous, le trafic entrant (HTTPS-TCP/443) est équilibré à l’aide du SKU Azure Application Gateway v2, qui s’étend sur plusieurs zones de disponibilité. La passerelle applicative distribue la requête de l’utilisateur entre les serveurs web, qui sont distribués entre les zones de disponibilité. Le serveur web transfère la requête aux instances de serveur de gestion et de traitement qui sont déployées dans des machines virtuelles distinctes entre différentes zones de disponibilité. Les fichiers Azure Premium avec stockage redondant de zone sont attachés via une liaison privée à des machines virtuelles de niveau de stockage et de gestion pour accéder au contenu, comme des rapports un univers et des connexions. L’application accède à la base de données CMS et d’audit de l’application s’exécutant sur une base de données Azure SQL (DBaaS) redondante dans une zone qui réplique les bases de données sur plusieurs emplacements physiques dans la région Azure.

![Architecture de haute disponibilité pour la plateforme SAP BOBI sur Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

L’architecture ci-dessus fournit des informations sur la façon dont le déploiement de SAP BOBI sur Azure peut être effectué. Toutefois, elle ne couvre pas toutes les options de configuration possibles pour la plateforme SAP BOBI sur Azure. Le client peut adapter son déploiement en fonction de ses besoins professionnels, en choisissant différents produits ou services pour des composants tels que l’équilibreur de charge, le serveur de référentiel de fichiers et le système de gestion de base de données (SGBD).

Dans le cas où les zones de disponibilité ne sont pas disponibles dans la région sélectionnée, vous pouvez déployer des machines virtuelles Azure dans des groupes à haute disponibilité. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. Ainsi, si une défaillance matérielle ou logicielle se produit, seul un sous-ensemble de vos machines virtuelles est affecté et la solution globale reste opérationnelle.

## <a name="disaster-recovery"></a>Récupération d'urgence

Les instructions de cette section expliquent la stratégie visant à fournir une protection de récupération d’urgence pour la plateforme SAP BOBI. Elles complètent le document [Récupération d’urgence pour SAP](../../../site-recovery/site-recovery-sap.md), qui représente les principales ressources pour l’approche globale de la récupération d’urgence SAP. Pour la plateforme SAP BusinessObjects BI, reportez-vous à la note SAP [2056228](https://launchpad.support.sap.com/#/notes/2056228), qui décrit ci-dessous les méthodes d’implémentation de l’environnement de récupération d’urgence.

 1. Utilisation complète ou sélective de la gestion du cycle de vie (LCM) ou de la fédération pour promouvoir/distribuer le contenu à partir du système principal.
 2. Copie périodique du contenu CMS et FRS.

Dans ce guide, nous allons parler de la deuxième option d’implémentation de l’environnement de récupération d’urgence. Il ne couvre pas la liste exhaustive de toutes les options de configuration possibles pour la récupération d’urgence, mais couvre une solution qui utilise des services Azure natifs en association avec la configuration de la plateforme SAP BOBI.

>[!Important]
>La disponibilité de chaque composant dans la plateforme SAP BusinessObjects BI doit être prise en compte dans la région secondaire, et la stratégie de récupération d’urgence complète doit être testée minutieusement.

### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Architecture de récupération d’urgence de référence pour la plateforme décisionnelle SAP BusinessObjects

Cette architecture de référence exécute un déploiement multi-instance de la plateforme SAP BOBI avec des serveurs d’applications redondants. Pour la récupération d’urgence, vous devez basculer tous les composants de la plateforme SAP BOBI vers une région secondaire. Dans l’illustration ci-dessous, les fichiers Azure Premium sont utilisés en tant que magasins de fichiers, la base de données SQL Azure en tant que référentiel CMS/d’audit et Azure Application Gateway comme équilibreur de charge du trafic. La stratégie visant à assurer la protection de la récupération d’urgence pour chaque composant est différente. Nous nous pencherons dessus en détail dans la section suivante.

![Récupération d’urgence de la plateforme décisionnelle SAP BusinessObjects pour Windows](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>Équilibrage de charge

L’équilibreur de charge est utilisé pour distribuer le trafic sur les serveurs d’applications web de la plateforme SAP BOBI. Dans Azure, vous pouvez utiliser Azure Load Balancer ou Azure Application Gateway pour équilibrer la charge du trafic entre vos serveurs web. Pour obtenir la récupération d’urgence pour les services d’équilibrage de charge, vous devez implémenter une autre instance d’Azure Load Balancer ou d’Azure Application Gateway sur la région secondaire. Pour conserver la même URL après un basculement de récupération d’urgence, vous devez modifier l’entrée dans le DNS, en pointant sur le service d’équilibrage de charge en cours d’exécution sur la région secondaire.

### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Machines virtuelles exécutant des serveurs d’applications web et décisionnelles

Le service [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) peut être utilisé pour répliquer des machines virtuelles exécutant des serveurs d’applications web et décisionnelles sur la région secondaire. Il réplique les serveurs et leurs disques managés attachés sur la région secondaire afin que, en cas de catastrophe ou de panne, vous puissiez facilement basculer sur votre environnement répliqué et continuer à travailler. Pour commencer la réplication de toutes les machines virtuelles d’application SAP vers le centre de données de reprise d’activité Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md).

### <a name="filestore"></a>Magasin de fichiers

Le magasin de fichiers est un répertoire de disque où sont stockés les fichiers réels comme, les rapports ou les documents BI. Il est important que tous les fichiers du magasin de fichiers soient synchronisés avec la région de récupération d’urgence. Selon le type de service de partage de fichiers que vous utilisez pour la plateforme SAP BOBI exécutée sur Windows, la stratégie de récupération d’urgence nécessaire doit être adoptée pour synchroniser le contenu.

- **Les fichiers Azure Premium** prennent uniquement en charge le stockage localement redondant (LRS) et le stockage redondant interzone (ZRS). Pour la stratégie de récupération d’urgence des fichiers Azure Premium, vous pouvez utiliser [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) ou [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0) pour copier vos fichiers vers un autre compte de stockage dans une région différente. Pour plus d’informations, consultez [Récupération d’urgence et basculement de compte de stockage](../../../storage/common/storage-disaster-recovery-guidance.md).

- **Azure NetApp Files** fournit des volumes NFS et SMB, de sorte que tout outil de copie basé sur des fichiers peut être utilisé pour répliquer les données entre des régions Azure. Pour plus d’informations sur la façon de copier un volume ANF dans une autre région, consultez [Questions fréquentes (FAQ) sur Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

  Vous pouvez utiliser la réplication inter-région Azure NetApp Files, qui est actuellement disponible en [préversion](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) et qui utilise la technologie NetApp SnapMirror®. Ainsi, seuls les blocs modifiés sont envoyés sur le réseau dans un format fiable et compressé. Cette technologie propriétaire limite le volume de données nécessaire à la réplication dans les différentes régions, ce qui permet d’économiser les coûts de transfert de données. Elle réduit également le temps de réplication, ce qui vous permet d’obtenir un objectif de point de restauration (RPO) inférieur. Pour plus d’informations, consultez [Configuration requise et considérations pour la réplication inter-région](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md).

### <a name="cms-database"></a>Base de données CMS

Les bases de données CMS et d’audit dans la région de récupération d’urgence doivent être une copie des bases de données qui s’exécutent dans la région primaire. Selon le type de base de données, il est important de copier la base de données dans la région de récupération d’urgence en fonction du RTO et du RPO requis de l’entreprise. Cette section décrit les différentes options disponibles pour chaque service de base de données en tant que service (DBaaS) dans Azure, pris en charge pour une application SAP BOBI exécutée sur Windows.

#### <a name="azure-sql-database"></a>Base de données Azure SQL

Pour la stratégie de récupération d’urgence d’[Azure SQL Database](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md), deux options sont disponibles pour copier la base de données dans la région secondaire. Les deux options de récupération offrent un niveau de RTO et de RPO différent. Pour plus d’informations sur le RTO et le RPO pour chaque option de récupération, consultez [Récupérer une base de données sur un serveur existant](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server).

1. [Restauration de la sauvegarde de base de données géo-redondante](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   Par défaut, la base de données Azure SQL stocke les données dans des [objets blob de stockage](../../../storage/common/storage-redundancy.md) géoredondants qui sont répliqués dans une [région jumelée](../../../best-practices-availability-paired-regions.md). Pour une base de données SQL, la redondance du stockage de sauvegarde peut être configurée au moment de la création de la base de données du CMS et d’audit ou peut être mise à jour pour une base de données existante ; les modifications apportées à une base de données existante s’appliquent uniquement aux sauvegardes ultérieures. Vous pouvez restaurer une base de données sur toute base de données Azure SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière sauvegarde géo-répliquée. La géorestauration utilise une sauvegarde géorépliquée comme source. Mais il peut y avoir un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans un objet blob Azure dans une autre région. C’est pourquoi la base de données restaurée peut avoir jusqu’à une heure de retard par rapport à la base de données d’origine.

   >[!Important]
   >La géorestauration est disponible pour les bases de données Azure SQL configurées avec un [stockage de sauvegarde](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy) géoredondant.

2. [Géoréplication](../../../azure-sql/database/active-geo-replication-overview.md) ou un [groupe de basculement automatique](../../../azure-sql/database/auto-failover-group-overview.md)

   La géoréplication est une fonctionnalité Azure SQL Database qui vous permet de créer des bases de données secondaires lisibles à partir de bases de données individuelles sur un serveur dans une région identique ou différente. Si la géoréplication est activée pour les bases de données CMS et d’audit, l’application peut lancer le basculement vers une base de données secondaire dans une autre région Azure. La géoréplication est activée pour des bases de données individuelles, mais pour activer le basculement transparent et coordonné de plusieurs bases de données (CMS et audit) pour l’application SAP BOBI, il est recommandé d’utiliser le groupe de basculement automatique. Il fournit la sémantique de groupe en plus de la géoréplication active, ce qui signifie que l’intégralité du serveur SQL Azure (toutes les bases de données) est répliquée vers une autre région au lieu de bases de données individuelles. Vérifiez le tableau des fonctionnalités qui [compare la géoréplication avec les groupes de basculement](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups).

   Les groupes de basculement automatique fournissent des points de terminaison d’écouteur de lecture-écriture et de lecture seule qui restent inchangés pendant les basculements. Le point de terminaison de lecture-écriture peut être conservé en tant qu’écouteur dans l’entrée de connexion ODBC pour les bases de données CMS et d’audit. Ainsi, que vous utilisiez l’activation manuelle ou automatique du basculement, ce dernier bascule toutes les bases de données secondaires du groupe en bases de données primaires. Une fois le basculement des bases de données terminé, l’enregistrement DNS est automatiquement mis à jour pour rediriger les points de terminaison vers la nouvelle région. L’application est donc automatiquement connectée à la base de données CMS en tant que point de terminaison de lecture-écriture. Elle est conservée en tant qu’écouteur dans une connexion ODBC.

   Dans la figure ci-dessous, le groupe de basculement automatique pour Azure SQL Server (azussqlbodb) exécuté dans la région USA Est 2 est répliqué dans la région secondaire USA Est (site de récupération d’urgence). Le point de terminaison de l’écouteur de lecture/écriture est conservé en tant qu’écouteur dans une connexion ODBC pour le serveur d’application BI s’exécutant sur Windows. Après le basculement, le point de terminaison reste le même et aucune intervention manuelle n’est requise pour connecter l’application BI à la base de données SQL Azure sur la région secondaire.

   ![Groupes de basculement automatique Azure SQL Database](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   Cette option fournit un RTO et un RPO inférieurs à l’option 1. Pour plus d’informations sur cette option, consultez [Utiliser des groupes de basculement automatique pour permettre le basculement transparent et coordonné de plusieurs bases de données](../../../azure-sql/database/auto-failover-group-overview.md)

#### <a name="azure-database-for-mysql"></a>Azure Database pour MySQL

Azure Database pour MySQL offre plusieurs options de récupération de la base de données en cas de sinistre. Choisissez l’option qui convient le mieux à votre entreprise.

1. Activez les réplicas en lecture inter-régions pour améliorer la planification de la continuité d’activité et de la reprise d’activité. Vous pouvez effectuer une réplication du serveur source vers cinq réplicas au maximum. Les réplicas en lecture sont mis à jour de manière asynchrone à l’aide de la technologie de réplication des journaux des transactions de MySQL. Les réplicas sont de nouveaux serveurs que vous gérez de manière similaire aux serveurs Azure Database pour MySQL classiques. Pour plus d’informations sur les réplicas en lecture, les régions disponibles, les restrictions et le basculement, consultez cet [article sur les concepts relatifs aux réplicas en lecture](../../../mysql/concepts-read-replicas.md).

2. Utilisez la fonctionnalité de géorestauration d’Azure Database pour MySQL qui restaure le serveur à l’aide de sauvegardes géoredondantes. Ces sauvegardes sont accessibles même lorsque la région dans laquelle votre serveur est hébergé est hors connexion. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région et remettre votre serveur en ligne.

  > [!Important]
  > La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes. La modification des **options de redondance des sauvegardes** après la création du serveur n’est pas prise en charge. Pour plus d’informations, consultez l’article [Redondance des sauvegardes](../../../mysql/concepts-backup.md#backup-redundancy-options).

Voici la recommandation pour la récupération d’urgence de chaque niveau utilisé dans cet exemple.

| Niveaux de plateforme SAP BOBI                          | Recommandation                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure Application Gateway ou Azure Load Balancer | Configuration parallèle d’Application Gateway sur la région secondaire    |
| Serveurs d’applications web                          | Répliquer à l’aide de Site Recovery                             |
| Serveurs d’applications décisionnelles                           | Répliquer à l’aide de Site Recovery                             |
| Fichiers Azure Premium                              | AzCopy **ou** Azure PowerShell                               |
| Azure NetApp Files                               | Outil de copie basé sur des fichiers pour répliquer des données vers la région secondaire **ou** Réplication inter-région ANF (préversion) |
| Base de données Azure SQL                               | Géoréplication/groupes de basculement automatique **ou** géorestauration.     |
| Azure Database pour MySQL                         | Réplicas en lecture inter-régions **ou** Restaurer une sauvegarde à partir de sauvegardes géoredondantes |
## <a name="next-steps"></a>Étapes suivantes

- [Configurer la reprise d’activité pour un déploiement d’application SAP multiniveau](../../../site-recovery/site-recovery-sap.md)
- [Planification et implémentation de machines virtuelles Azure pour SAP](planning-guide.md)
- [Déploiement de machines virtuelles Azure pour SAP](deployment-guide.md)
- [Déploiement SGBD de machines virtuelles Azure pour SAP](./dbms_guide_general.md)