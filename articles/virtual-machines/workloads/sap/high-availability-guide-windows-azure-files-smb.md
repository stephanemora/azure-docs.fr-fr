---
title: Haute disponibilité des machines virtuelles Azure pour SAP NW sous Windows avec Azure Files (SMB) | Microsoft Docs
description: Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sous Windows avec Azure Files (SMB) pour les applications SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: stmuelle
ms.openlocfilehash: 458c63f27e9e17d71b9af97eda0de56f8f935a25
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251354"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>Haute disponibilité pour SAP NetWeaver sur des machines virtuelles Azure sous Windows avec Azure Files Premium SMB pour les applications SAP

## <a name="introduction"></a>Introduction
Azure Files Premium SMB est désormais entièrement pris en charge par Microsoft et SAP. **SWPM 1.0 SP32** et **SWPM 2.0 SP09** et versions ultérieures prennent en charge le stockage Azure Files Premium SMB.  Il existe des exigences particulières pour le dimensionnement des partages Azure Files Premium SMB. Cette documentation contient des recommandations spécifiques sur la distribution de la charge de travail sur Azure Files Premium SMB, sur le dimensionnement adéquat d’Azure Files Premium SMB et sur la configuration minimale requise pour Azure Files Premium SMB.

Les solutions SAP à haute disponibilité nécessitent un partage de fichiers hautement disponible pour héberger les **répertoires sapmnt**, **trans** et **interface**. Azure Files Premium SMB est une solution PaaS Azure simple pour les systèmes de fichiers partagés pour SAP dans les environnements Windows. Azure Files Premium SMB peut être utilisé conjointement avec des groupes à haute disponibilité et des zones de disponibilité. Azure Files Premium SMB peut également être utilisé pour les scénarios de récupération d’urgence vers une autre région.  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>Dimensionnement et distribution d’Azure Files Premium SMB pour les systèmes SAP

Les points suivants doivent être évalués lors de la planification du déploiement d’Azure Files Premium SMB :
* Le nom de partage de fichiers **sapmnt** peut être créé une seule fois pour chaque compte de stockage.  Il est possible de créer des identificateurs de sécurité (SID) supplémentaires en tant que répertoires sur le même partage **/sapmnt**, par exemple : **/sapmnt/\<SID1\>** et **/sapmnt/\<SID2\>** . 
* Choisissez une taille, des IOPS et un débit appropriés.  Une taille suggérée pour le partage est de 256 Go par SID.  La taille maximale d’un partage est de 5120 Go.
* Azure Files Premium SMB peut ne pas fonctionner de manière optimale pour les partages  **sapmnt** de très grande taille avec plus de 1 à 2 millions de fichiers par compte de stockage.Les clients qui ont des millions de programmes de traitement par lots créant des millions de fichiers journaux de travaux doivent régulièrement les réorganiser conformément à la [note SAP 16083][16083]. Le cas échéant, les anciens journaux de travaux peuvent être déplacés ou archivés vers un autre Azure Files Premium SMB. Si **sapmnt** est censé être très important, il convient d’envisager d’autres options (telles qu’Azure ANF).
* Il est recommandé d’utiliser un point de terminaison de réseau privé.
* Évitez de regrouper un trop grand nombre de SID sur un seul compte de stockage et son partage de fichiers.
* En règle générale, il n’est pas possible de regrouper plus de deux à quatre SID non liés à la production.
* Ne regroupez pas l’ensemble du paysage Développement, QAS et Production sur un seul compte de stockage ou partage de fichiers.La défaillance du partage entraînera l’arrêt de l’ensemble du paysage SAP.
* Il n’est pas recommandé de regrouper les **répertoires sapmnt** et **transport** sur le même compte de stockage, sauf pour les très petits systèmes. Lors de l’installation de l’instance SAP PAS, SAPInst demande un nom d’hôte de transport.  Le nom de domaine complet d’un autre compte de stockage doit être entré : <storage_account>.file.core.windows.net.
* Ne regroupez pas le système de fichiers utilisé pour les interfaces sur le même compte de stockage que **/sapmnt/\<SID>** . 
* Les utilisateurs ou groupes SAP doivent être ajoutés au partage « sapmnt » et cette autorisation doit être définie dans le portail Azure : **Contributeur élevé de partage SMB de données de fichier de stockage**.

Il existe des raisons importantes pour séparer **Transport**, **Interface** et **sapmnt** sur des comptes de stockage distincts.  La distribution de ces composants sur des comptes de stockage distincts améliore le débit et la résilience et simplifie l’analyse des performances.  Si de nombreux SID et autres systèmes de fichiers sont regroupés sur un seul compte de stockage Azure Files et que les performances du compte de stockage sont médiocres en raison des limites de débit, il est extrêmement difficile d’identifier quel SID ou quelle application est à l’origine du problème. 

## <a name="planning"></a>Planification 
> [!IMPORTANT]
> L’installation de systèmes à haute disponibilité SAP sur Azure Files Premium SMB avec intégration d’Active Directory nécessite la collaboration de plusieurs équipes.  Il est fortement recommandé que l’équipe De base, l’équipe Active Directory et l’équipe Azure travaillent ensemble pour effectuer ces tâches : 
>
* Équipe Azure : installation et configuration du compte de stockage, exécution de scripts et synchronisation d’annuaires AD.
* Équipe Active Directory : création de comptes d’utilisateur et de groupes.
* Équipe de base : exécution de SWPM et définition des listes de contrôle d’accès (le cas échéant).

Conditions préalables à l’installation de systèmes à haute disponibilité SAP NetWeaver sur Azure Files Premium SMB avec intégration Active Directory :

* Les serveurs SAP doivent être joints à un domaine Active Directory.
* Le domaine Active Directory contenant les serveurs SAP doit être répliqué dans Azure Active Directory à l’aide d’Azure AD Connect.
* Il est fortement recommandé de disposer d’au moins un contrôleur de domaine Active Directory dans le paysage Azure pour éviter de traverser l’itinéraire ExpressRoute pour contacter les contrôleurs de domaine locaux.
* L’équipe du support Azure consulter la documentation relative à l’[intégration d’Azure Files SMB à Active Directory](../../../storage/files/storage-files-identity-auth-active-directory-enable.md#videos). *La vidéo présente des options de configuration supplémentaires qui ont été modifiées (DNS) et ignorées (DFS-N) par souci de simplification.* Il s’agit néanmoins d’options de configuration valides. 
* L’utilisateur qui exécute le script Azure Files PowerShell doit avoir l’autorisation de créer des objets dans Active Directory.
* **SWPM 1.0 SP32 et SWPM 2.0 SP09 ou une version ultérieure sont requis. Le patch SAPInst doit être la version 749.0.91 ou ultérieure.**
* Une version à jour de PowerShell doit être installée sur le serveur Windows sur lequel le script est exécuté. 

## <a name="installation-sequence"></a>Séquence d’installation
 1. L’administrateur Active Directory doit créer au préalable trois utilisateurs de domaine disposant de droits d’**administrateur local** et un groupe global dans le serveur **Windows AD local** : **SAPCONT_ADMIN@SAPCONTOSO.local** possède des droits d’administrateur de domaine et est utilisé pour exécuter **SAPInst**, **\<sid>adm** et **SAPService\<SID>** en tant qu’utilisateurs du système SAP et le groupe **SAP_\<SAPSID>_GlobalAdmin**. Le guide d’installation SAP contient les détails spécifiques requis pour ces comptes.  **Les comptes d’utilisateur SAP ne doivent pas être des administrateurs de domaine**. Il est généralement recommandé de **ne pas utiliser \<sid>adm pour exécuter SAPInst**.
 2. L’administrateur Active Directory ou l’administrateur Azure doit vérifier **Azure AD Connect** Synchronization Service Manager. Par défaut, la réplication sur **Azure Active Directory** prend environ 30 minutes. 
 3. L’administrateur Azure doit effectuer les tâches suivantes :
     1. Créer un compte de stockage avec un **stockage redondant interzone** ou **stockage localement redondant Premium**. Les clients ayant un déploiement zonal doivent choisir le stockage redondant interzone. Vous devez ici choisir entre la configuration d’un **compte Standard** ou **Premium** : ![Capture d’écran du portail Azure pour la création d’un compte de stockage, étape 1](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png)Capture d’écran du portail Azure pour la création d’un compte de stockage, étape 1
         > [!IMPORTANT]
         > Pour une utilisation productive, il est recommandé d’utiliser un **compte Premium**. Pour une activité non productive, l’utilisation d’un **compte Standard** suffit. 
         >
         ![Capture d’écran du portail Azure pour la création d’un compte de stockage, étape 2](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)Capture d’écran du portail Azure pour la création d’un compte de stockage, étape 2
         
         Dans cet écran, les paramètres par défaut doivent être corrects.
        
         ![Capture d’écran du portail Azure pour la création d’un compte de stockage, étape 3](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)Capture d’écran du portail Azure pour la création d’un compte de stockage, étape 3 
         
         Dans cette étape, la décision d’utiliser un point de terminaison privé est prise.
     1. **Sélectionner un point de terminaison de réseau privé** pour le compte de stockage.
     Le cas échéant, ajoutez un enregistrement A DNS dans Windows DNS pour le **<storage_account_name>.file.core.windows.net** (il se peut que cela doive être dans une nouvelle zone DNS).  Discutez de ce sujet avec l’administrateur DNS.  La nouvelle zone ne doit pas être mise à jour en dehors d’une organisation.  
         ![pivate-endpoint-creation](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png)Capture d’écran du portail Azure pour la définition du point de terminaison privé.
         ![private-endpoint-dns-1](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png)Capture d’écran du serveur DNS pour la définition DNS du point de terminaison privé.    
     1. Créer le partage de fichiers **sapmnt** avec une taille appropriée.  La taille suggérée est de 256 Go, ce qui offre 650 IOPS, 75 Mo/sec en sortie et 50 Mo/sec en entrée.
         ![create-storage-account-5](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png)Capture d’écran du portail Azure pour la définition du partage SMB. 
      
     1. Télécharger le contenu [Azure Files GitHub](../../../storage/files/storage-files-identity-ad-ds-enable.md#download-azfileshybrid-module) et exécuter le [script](../../../storage/files/storage-files-identity-ad-ds-enable.md#run-join-azstorageaccountforauth).   
     Ce script crée un compte d’ordinateur ou un compte de service dans Active Directory.  L’utilisateur qui exécute le script doit avoir les propriétés suivantes : 
         * L’utilisateur qui exécute le script doit avoir l’autorisation de créer des objets dans le domaine Active Directory contenant les serveurs SAP. En règle générale, un compte d’administrateur de domaine est utilisé, par exemple : **SAPCONT_ADMIN@SAPCONTOSO.local** . 
         * Avant d’exécuter le script, vérifiez que ce compte d’utilisateur de domaine Active Directory est synchronisé avec Azure Active Directory (AAD).  Par exemple, vous pouvez ouvrir le portail Azure, accéder aux utilisateurs AAD, vérifier que l’utilisateur **SAPCONT_ADMIN@SAPCONTOSO.local** existe et vérifier le compte d’utilisateur AAD **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .
         * Accordez le rôle **RBAC Contributeur** à ce compte d’utilisateur Azure Active Directory pour le groupe de ressources contenant le compte de stockage qui détient le partage de fichiers.  Dans cet exemple, l’utilisateur **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** se voit attribuer le **rôle Contributeur** pour le groupe de ressources correspondant. 
         * Le script doit être exécuté en étant connecté à un serveur Windows à l’aide d’un compte d’utilisateur de domaine Active Directory avec l’autorisation spécifiée ci-dessus. Dans cet exemple, le compte **SAPCONT_ADMIN@SAPCONTOSO.local** est utilisé.
         >[!IMPORTANT]
         > Lors de l’exécution de la commande de script PowerShell **Connect-AzAccount**, il est vivement recommandé d’entrer le compte d’utilisateur Azure Active Directory qui correspond et qui est mappé au compte d’utilisateur de domaine Active Directory utilisé pour se connecter à un serveur Windows. Dans cet exemple, il s’agit du compte d’utilisateur **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .
         >
         Dans cet exemple de scénario, l’administrateur Active Directory se connecte au serveur Windows en tant que **SAPCONT_ADMIN@SAPCONTOSO.local** et, en utilisant la **commande PowerShell Connect-AzAccount**, se connecte en tant qu’utilisateur **SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com** .  Dans l’idéal, l’administrateur Active Directory et l’administrateur Azure doivent travailler ensemble sur cette tâche.
         ![powershell-script-1](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png)Capture d’écran du script PowerShell créant le compte AD local.

         ![smb-configured-screenshot](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)Capture d’écran du portail Azure après l’exécution réussie du script PowerShell. 

         Les éléments suivants doivent apparaître avec la mention « Configuré » :  
         Stockage -> Partages de fichiers « Active Directory : Configuré »
     1. Attribuez les utilisateurs SAP **\<sid>adm** et **SAPService\<SID>** et le groupe **SAP_\<SAPSID>_GlobalAdmin** au partage de fichiers Azure Files Premium SMB avec le rôle **Contributeur élevé de partage SMB de données de fichier de stockage** dans le portail Azure. 
     1. Vérifier la liste de contrôle d’accès sur le **partage de fichiers sapmnt** après l’installation et ajouter le compte **DOMAIN\CLUSTER_NAME$** , **DOMAIN\\\<sid>adm** et **DOMAIN\SAPService\<SID>** et le **groupe SAP_\<SID>_GlobalAdmin**. Ces comptes et ce groupe **doivent avoir le contrôle total du répertoire sapmnt**.

         > [!IMPORTANT]
         > Cette étape doit être réalisée avant l’installation de SAPInst, sinon il sera difficile, voire impossible, de modifier les listes de contrôle d’accès une fois que SAPInst a créé des répertoires et des fichiers sur le partage de fichiers.
         >
         ![Propriétés des listes de contrôle d’accès](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)Capture d’écran d’Explorateur Windows des droits des utilisateurs attribués.

         Les captures d’écran suivantes montrent comment ajouter des comptes d’ordinateur en sélectionnant l’option «Types d’objets » -> « Ordinateurs ». ![Capture d’écran de Windows Server montrant l’ajout du nom du cluster au répertoire AD local](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)Capture d’écran de Windows Server montrant l’ajout du nom du cluster au répertoire AD local.
         
         Vous pouvez trouver DOMAIN\CLUSTER_NAME$ en sélectionnant « Ordinateurs » dans « Types d’objets ».  
         ![Capture d’écran de l’ajout d’un compte d’ordinateur AD, étape 2](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-3.png)Capture d’écran de l’ajout d’un compte d’ordinateur AD, étape 2 ![Capture d’écran de l’ajout d’un compte d’ordinateur AD, étape 3](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-4.png)Capture d’écran de l’ajout d’un compte d’ordinateur AD, étape 3 ![Capture d’écran des propriétés d’accès du compte d’ordinateur](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-5.png)Capture d’écran des propriétés d’accès du compte d’ordinateur.

     8. Le cas échéant, déplacer le compte d’ordinateur créé pour Azure Files vers un conteneur Active Directory dont le compte n’expire pas.  Le nom du compte d’ordinateur sera le nom abrégé du compte de stockage. 

     
     > [!IMPORTANT]
     > Afin d’initialiser la liste de contrôle d’accès Windows pour le partage SMB, le partage doit être monté une fois sur une lettre de lecteur.
     >
     La clé de stockage est le mot de passe et l’utilisateur est **Azure\\\<SMB share name>** comme indiqué ici : ![montage à usage unique pour l’utilisation du net](media/virtual-machines-shared-sap-high-availability-guide/one-time-net-use-mount-1.png)Capture d’écran Windows du montage à usage unique pour l’utilisation du net du partage SMB.

 4. L’administrateur de base doit effectuer les tâches ci-dessous :
     1. [Installer le cluster Windows sur les nœuds ASCS/ERS et ajouter le témoin du cloud.](sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
     2. L’installation du premier nœud de cluster demandera le nom du compte de stockage SMB Azure Files.  Entrer le nom de domaine complet <storage_account_name>.file.core.windows.net.  Si SAPInst n’accepte pas plus de 13 caractères, alors la version de SWPM est trop ancienne.
     3. [Modifier le profil SAP de l’instance ASCS/SCS](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).
     4. [Mettre à jour le port de la sonde d’intégrité pour le rôle \<SID> SAP dans WSFC](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).
     5. Poursuivre l’installation de SWPM pour le deuxième nœud ASCS/ERS. SWPM demandera uniquement le chemin d’accès au répertoire du profil.  Entrer le chemin d’accès UNC complet au répertoire du profil.
     6. Entrer le chemin d’accès au profil UNC pour l’installation de la base de données et de PAS/AAS.
     7. L’installation de PAS demandera le nom d’hôte de Transport. Indiquer le nom de domaine complet d’un nom de compte de stockage distinct pour le répertoire de transport.
     8. Vérifier les listes de contrôle d’accès dans les répertoires SID et trans.

## <a name="disaster-recovery-setup"></a>Configuration de la récupération d’urgence
Les scénarios de récupération d’urgence ou de réplication interrégion sont pris en charge par Azure Files Premium SMB. Toutes les données des répertoires Azure Files Premium SMB peuvent être synchronisées en continu vers un compte de stockage de la région de récupération d’urgence à l’aide de ce lien. Après un événement de récupération d’urgence et un basculement de l’instance ASCS vers la région de récupération d’urgence, modifiez le paramètre de profil SAPGLOBALHOST pour qu’il pointe vers Azure Files SMB dans la région de récupération d’urgence. Les mêmes étapes de préparation doivent être effectuées sur le compte de stockage de récupération d’urgence pour joindre le compte de stockage à Active Directory et attribuer des rôles RBAC aux utilisateurs et aux groupes SAP.

## <a name="troubleshooting"></a>Résolution des problèmes
Parmi les scripts PowerShell téléchargés à l’étape 3.c, vous trouverez un script de débogage permettant d’effectuer quelques vérifications de base pour valider la configuration.
```powershell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
![Powershell-script-output](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-2.png)Capture d’écran PowerShell de la sortie du script de débogage.

![Powershell-script-technical-info](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-1.png)L’écran suivant montre les informations techniques permettant de valider une jonction de domaine réussie.
## <a name="useful-links--resources"></a>Liens et ressources utiles

* Note SAP [2273806][2273806] Prise en charge par SAP des solutions liées au stockage ou au système de fichiers 
* [Installer la haute disponibilité SAP NetWeaver sur un cluster de basculement Windows et un partage de fichiers pour des instances SAP ASCS/SCS sur Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Scénarios et architecture de haute disponibilité de machines virtuelles Azure pour SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Ajouter un port de sondage dans la configuration du cluster ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Installation d’une instance (A)SCS sur un cluster de basculement](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)

[16083]:https://launchpad.support.sap.com/#/notes/16083
[2273806]:https://launchpad.support.sap.com/#/notes/2273806