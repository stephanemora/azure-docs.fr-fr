---
title: Tableau de prise en charge de Sauvegarde Azure pour la sauvegarde de SQL Server dans les machines virtuelles Azure
description: Propose un résumé des limitations et des paramètres de prise en charge de la sauvegarde de SQL Server dans les machines virtuelles Azure avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 4d197f8b3c1ed74ef45c1f7942ead52ccef0c14a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513181"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Tableau de prise en charge de la sauvegarde de SQL Server dans les machines virtuelles Azure

Vous pouvez utiliser le service Sauvegarde Azure pour sauvegarder des bases de données SQL Server dans des machines virtuelles Azure hébergées sur la plateforme cloud Microsoft Azure. Cet article résume les limitations et les paramètres généraux de prise en charge des scénarios et des déploiements de sauvegarde de SQL Server dans des machines virtuelles Azure.

## <a name="scenario-support"></a>Prise en charge du scénario

**Support** | **Détails**
--- | ---
**Déploiements pris en charge** | Les machines virtuelles Azure de la Place de marché SQL et les machines virtuelles autres que celles de la Place de marché (sur lesquelles SQL Server est installé manuellement) sont prises en charge.
**Régions prises en charge** | Australie Sud-Est (ASE), Australie Est (AE), Australie Centre (AC), Australie Centre 2 (AC) <br> Brésil Sud (BRS)<br> Canada Centre (CNC), Canada Est (CE)<br> Asie Sud-Est (SEA), Asie Est (EA) <br> USA Est (EUS), USA Est 2 (EUS2), West Central US (WCUS), USA Ouest (WUS), USA Ouest 2 (WUS 2), USA Centre Nord (NCUS), USA Centre (CUS), USA Centre Sud (SCUS) <br> Inde Centre (INC), Inde Sud (INS), Inde Ouest <br> Japon Est (JPE), Japon Ouest (JPW) <br> Corée Centre (KRC), Corée Sud (KRS) <br> Europe Nord (NE), Europe Ouest (WE) <br> Royaume-Uni Sud (UKS), Royaume-Uni Ouest (UKW) <br> US Gov Arizona, US Gov Virginie, US Gov Texas, US DoD Centre, US DoD Est <br> Allemagne Nord, Allemagne Centre-Ouest <br> Suisse Nord, Suisse Ouest <br> France Centre <br> Chine Est, Chine Est 2, Chine Nord, Chine Nord 2
**Systèmes d’exploitation pris en charge** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux n’est pas actuellement pris en charge.
**Versions de SQL Server prises en charge** | SQL Server 2019, SQL Server 2017 comme indiqué dans la [page Rechercher le cycle de vie des produits](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 et différents SP comme indiqué dans la [page Rechercher le cycle de vie des produits](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versions .NET prises en charge** | .NET Framework 4.5.2 ou ultérieur installé sur la machine virtuelle

## <a name="feature-consideration-and-limitations"></a>Considérations et limitations relatives aux fonctionnalités

* La sauvegarde SQL Server peut être configurée dans le portail Azure ou **PowerShell**. Nous ne prenons pas en charge l’interface CLI.
* La solution est prise en charge pour les deux types de [déploiements](../azure-resource-manager/management/deployment-models.md) : machines virtuelles Azure Resource Manager et machines virtuelles classiques.
* La machine virtuelle exécutant SQL Server nécessite une connexion Internet pour accéder aux adresses IP publiques Azure.
* L’**instance de cluster de basculement (FCI)** SQL Server n’est pas prise en charge.
* Les opérations de sauvegarde et de restauration des bases de données miroir et des instantanés de base de données ne sont pas prises en charge.
* L’utilisation de plusieurs solutions de sauvegarde pour sauvegarder votre instance SQL Server autonome ou votre groupe de disponibilité SQL AlwaysOn peut entraîner l’échec de la sauvegarde. Une telle utilisation est donc à éviter.
* La sauvegarde de deux nœuds d’un groupe de disponibilité individuellement avec les mêmes solutions ou des solutions différentes peut également entraîner l’échec de la sauvegarde.
* La Sauvegarde Azure prend uniquement en charge les types de sauvegarde Complète et Copie complète uniquement pour les bases de données **en lecture seule**.
* Les bases de données comprenant un grand nombre de fichiers ne peuvent pas être protégées. Le nombre maximal de fichiers pris en charge est d’**environ 1 000**.  
* Vous pouvez sauvegarder jusqu’à **environ 2 000** bases de données SQL Server dans un coffre. Vous pouvez créer plusieurs coffres au cas où vous auriez un plus grand nombre de bases de données.
* Vous pouvez configurer la sauvegarde pour au maximum **50** bases de données en une seule fois. Cette restriction permet d’optimiser les charges de sauvegardes.
* Nous prenons en charge les bases de données d’une taille maximale de **2 To**. Pour les tailles supérieures, des problèmes de performance peuvent survenir.
* Pour avoir une idée du nombre de bases de données qui peuvent être protégées par serveur, tenez compte de facteurs tels que la bande passante, la taille de machine virtuelle, la fréquence de sauvegarde, la taille de base de données, etc. [Téléchargez](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) le planificateur de ressources pour calculer le nombre approximatif de bases de données que vous pouvez avoir par serveur en fonction des ressources de machine virtuelle et de la stratégie de sauvegarde.
* Quand des groupes de disponibilité sont configurés, les sauvegardes sont effectuées à partir des différents nœuds en fonction de plusieurs facteurs. Le comportement des sauvegardes pour un groupe de disponibilité est récapitulé ci-dessous.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportement de sauvegarde avec les groupes de disponibilité Always On

Il est recommandé de configurer la sauvegarde sur un seul nœud d’un groupe de disponibilité. Configurez toujours la sauvegarde dans la même région que le nœud principal. En d’autres termes, le nœud principal doit toujours se trouver dans la région où vous configurez la sauvegarde. Si tous les nœuds du groupe de disponibilité se trouvent dans la région où la sauvegarde est configurée, cela ne pose pas de problème.

#### <a name="for-cross-region-ag"></a>Groupe de disponibilité interrégional

* Quelle que soit la préférence de sauvegarde, les sauvegardes ne s’exécuteront que sur les nœuds qui se trouvent dans la région où la sauvegarde est configurée. Cela est dû au fait que les sauvegardes interrégionales ne sont pas prises en charge. Si vous ne disposez que de deux nœuds et que le nœud secondaire se trouve dans l’autre région, les sauvegardes continuent de s’exécuter sur le nœud principal (sauf si votre préférence de sauvegarde est « Secondaire uniquement »).
* Si un nœud bascule vers une région différente de celle où la sauvegarde est configurée, les sauvegardes échouent sur les nœuds de la région ayant basculé.

En fonction de la préférence de sauvegarde et des types de sauvegardes (complète/différentielle/de fichier journal/copie complète uniquement), les sauvegardes sont effectuées à partir d’un nœud particulier (principal/secondaire).

#### <a name="backup-preference-primary"></a>Préférence de sauvegarde : Principal

**Type de sauvegarde** | **Nœud**
--- | ---
Complète | Principal
Différentielle | Principal
Journal |  Principal
Copie complète uniquement |  Principal

#### <a name="backup-preference-secondary-only"></a>Préférence de sauvegarde : Secondaire uniquement

**Type de sauvegarde** | **Nœud**
--- | ---
Complète | Principal
Différentielle | Principal
Journal |  Secondary
Copie complète uniquement |  Secondary

#### <a name="backup-preference-secondary"></a>Préférence de sauvegarde : Secondary

**Type de sauvegarde** | **Nœud**
--- | ---
Complète | Principal
Différentielle | Principal
Journal |  Secondary
Copie complète uniquement |  Secondary

#### <a name="no-backup-preference"></a>Pas de préférence de sauvegarde

**Type de sauvegarde** | **Nœud**
--- | ---
Complète | Principal
Différentielle | Principal
Journal |  Secondary
Copie complète uniquement |  Secondary

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [sauvegarder une base de données SQL Server](backup-azure-sql-database.md) s'exécutant sur une machine virtuelle Azure.
