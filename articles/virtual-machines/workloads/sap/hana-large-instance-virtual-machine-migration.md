---
title: Migration de SAP HANA sur Azure (grandes instances) vers des machines virtuelles Azure | Microsoft Docs
description: Migration de SAP HANA sur Azure (grandes instances) vers des machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2021
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e401ee0a8760033bfada5f054ad4c61904a62a6a
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136846"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Migration de SAP HANA sur grande instance Azure vers des machines virtuelles Azure
Cet article décrit les scénarios possibles de déploiement d’une grande instance Azure et propose une approche de planification et de migration qui réduit le temps d’arrêt lors de la transition.

## <a name="overview"></a>Vue d'ensemble
Les grandes instances Azure pour SAP HANA (HLI) ont été annoncées pour la première fois en septembre 2016. Depuis, beaucoup ont adopté ce matériel en tant que service pour leur plateforme de calcul en mémoire. Pourtant, ces dernières années, l’extensibilité des machines virtuelles Azure et la prise en charge du déploiement scale-out HANA ont permis de dépasser la demande de capacité de base de données ERP de la plupart des entreprises clientes. Un grand nombre d’entre elles s’intéressent à la migration de leur charge de travail SAP HANA de serveurs physiques vers des machines virtuelles Azure.

Cet article n’est pas un document de configuration pas à pas. Il décrit les modèles de déploiement courants et fournit des conseils de planification et de migration. Notre objectif est d’insister sur les éléments à prendre en compte lors de la préparation afin de réduire les temps d’arrêt liés à la transition.

## <a name="assumptions"></a>Hypothèses
Cet article repose sur les hypothèses suivantes :
- Nous considérons uniquement une migration homogène du service de calcul de base de données HANA d’un serveur HLI (Hana Large Instance) vers une machine virtuelle Azure sans mise à niveau logicielle ou mise à jour corrective significative. Ces mises à jour mineures incluent l’utilisation d’une version plus récente du système d’exploitation ou d’une version prise en charge de HANA, tel qu’explicitement indiqué dans les notes SAP pertinentes. 
- Vous ferez toutes les activités de mise à jour/mise à niveau avant ou après la migration.  Par exemple, conversion de SAP HANA MCOS en déploiement MDC. 
- La réplication du système SAP HANA (HSR) est l’approche de migration qui offrirait le moins de temps d’arrêt. Ce document ne couvre pas les autres méthodes de migration.
- Ce guide s'applique à la fois aux références SKU Rev3 et Rev4 de HLI.
- L'architecture de déploiement HANA reste pour l'essentiel inchangée pendant la migration.  Autrement dit, un système doté d’une seule instance de récupération d’urgence restera le même à l’emplacement de destination.
- Vous avez lu et compris le Contrat de niveau de service (SLA) de l’architecture cible (à venir). 
- Sur le plan commercial, il existe des différences entre les serveurs HLI et les machines virtuelles. Surveillez l’utilisation de vos machines virtuelles pour la gestion des coûts.
- Vous comprenez que HLI est une plateforme de calcul dédiée, tandis que les machines virtuelles s’exécutent sur une infrastructure partagée mais isolée.
- Vous avez confirmé que les machines virtuelles cibles prennent en charge l’architecture prévue. Pour une liste des références SKU de toutes les machines virtuelles certifiées pour le déploiement de SAP HANA, consultez le [Répertoire de matériel SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Vous avez validé le plan de conception et de migration.
- Planifiez la machine virtuelle de récupération d'urgence en même temps que le site principal.  Vous ne pouvez pas utiliser le nœud de récupération d’urgence HLI pour le site principal exécuté sur les machines virtuelles après la migration.
- Vous avez copié les fichiers de sauvegarde requis sur les machines virtuelles cibles, conformément aux exigences de récupération et de conformité de l’entreprise. Les sauvegardes accessibles aux machines virtuelles permettent une récupération jusqu’à une date et heure au cours de la période de transition.
- Pour la haute disponibilité (HA) de la réplication système SAP HANA (HSR), vous devez installer et configurer l’appareil STONITH conformément aux guides SAP HANA HA pour [SLES](./high-availability-guide-suse-pacemaker.md) et [RHEL](./high-availability-guide-rhel-pacemaker.md).  Elle n'est pas préconfigurée comme dans le cas de HLI.
- Cette approche de migration ne couvre pas les références SKU HLI avec configuration Optane.

## <a name="deployment-scenarios"></a>Scénarios de déploiement
Vous pouvez migrer vers des machines virtuelles Azure pour tous les scénarios HLI. Les modèles de déploiement courants associés à HLI sont résumés dans le tableau suivant. Pour bénéficier des services Azure complémentaires, vous devrez peut-être procéder à des modifications architecturales mineures.

| ID du scénario | Scénario HLI | Migrer vers une machine virtuelle ? | Remarque |
| --- | --- | --- | --- |
| 1 | [Nœud unique avec un SID](./hana-supported-scenario.md#single-node-with-one-sid) | Oui | - |
| 2 | [Nœud unique avec plusieurs composants dans un système (MCOS)](./hana-supported-scenario.md#single-node-mcos) | Oui | - |
| 3 | [Nœud unique avec récupération d'urgence à l'aide de la réplication du stockage](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Non | La réplication du stockage n’est pas disponible avec la plateforme virtuelle Azure. Remplacez la solution de récupération d’urgence actuelle par la réplication HSR ou par la sauvegarde/restauration. |
| 4 | [Nœud unique avec récupération d'urgence (polyvalente) à l'aide de la réplication du stockage](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Non | La réplication du stockage n’est pas disponible avec la plateforme virtuelle Azure. Remplacez la solution de récupération d’urgence actuelle par la réplication HSR ou par la sauvegarde/restauration. |
| 5 | [HSR avec STONITH pour une haute disponibilité](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Oui | Aucun SBD préconfiguré pour les machines virtuelles cibles.  Sélectionnez et déployez une solution STONITH.  Options possibles : Agent de délimitation Azure (pris en charge pour [RHEL](./high-availability-guide-rhel-pacemaker.md) et [SLES](./high-availability-guide-suse-pacemaker.md)), et appareil de bloc STONITH (SBD). |
| 6 | [Haute disponibilité avec réplication HSR, récupération d'urgence avec réplication du stockage](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Non | Remplacez la réplication du stockage pour les besoins de récupération d’urgence par la réplication HSR ou par la sauvegarde/restauration. |
| 7 | [Basculement automatique avec hôte (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | Oui | Utilisez Azure NetApp Files (ANF) pour le stockage partagé avec des machines virtuelles Azure. |
| 8 | [Scale-out avec nœud de secours](./hana-supported-scenario.md#scale-out-with-standby) | Oui | BW/4HANA avec machines virtuelles M128s, M416s et M416ms utilisant ANF pour le stockage uniquement. |
| 9 | [Scale-out sans nœud de secours](./hana-supported-scenario.md#scale-out-without-standby) | Oui | BW/4HANA avec machines virtuelles M128s, M416s, M416ms (avec ou sans utilisation d’ANF pour le stockage). |
| 10 | [Scale-out avec récupération d'urgence à l'aide de la réplication du stockage](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Non | Remplacez la réplication du stockage pour les besoins de récupération d’urgence par la réplication HSR ou par la sauvegarde/restauration. |
| 11 | [Nœud unique avec récupération d'urgence à l'aide de HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Oui | - |
| 12 | [Réplication HSR à nœud unique vers site de récupération d'urgence (à coût optimisé)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Oui | - |
| 13 | [Haute disponibilité et récupération d'urgence avec réplication HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Oui | - |
| 14 | [Haute disponibilité et récupération d'urgence avec réplication HSR (à coût optimisé)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Oui | - |
| 15 | [Scale-out avec récupération d'urgence à l'aide de la réplication HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Oui | BW/4HANA avec machines virtuelles M128s, M416s, M416ms (avec ou sans utilisation d’ANF pour le stockage). |


## <a name="source-hli-planning"></a>Planification de la source (HLI)
Lors de l’intégration de votre serveur HLI, vous et Microsoft Service Management avez planifié les paramètres spécifiques au calcul, au réseau, au stockage et au système d’exploitation pour l’exécution de la base de données SAP HANA. Une planification similaire doit être effectuée pour la migration vers une machine virtuelle Azure.

### <a name="sap-hana-housekeeping"></a>Tâches de nettoyage SAP HANA 
Il est recommandé de nettoyer le contenu de la base de données pour éviter de migrer des données indésirables/obsolètes et des journaux périmés vers la nouvelle base de données.  Les tâches de nettoyage consistent généralement à supprimer ou à archiver des données anciennes, périmées ou inactives.  Cette « hygiène des données » doit être testée en mode hors production afin de confirmer sa validité avant une utilisation en production.

### <a name="allow-network-connectivity-for-new-vms-and-virtual-network"></a>Activer la connectivité réseau pour les nouvelles machines virtuelles et le réseau virtuel 
Dans le cadre de votre déploiement HLI, le réseau a été configuré sur la base des informations décrites dans l’article [Architecture réseau de SAP HANA (grandes instances)](./hana-network-architecture.md). En outre, le routage du trafic réseau s’effectue comme décrit à la section [Routage dans Azure](./hana-network-architecture.md#routing-in-azure).
- La nouvelle cible de migration de la machine virtuelle est-elle placée dans le réseau virtuel existant avec des plages d’adresses IP déjà autorisées à se connecter à la HLI ? Aucune autre mise à jour de connectivité n’est requise.
- La nouvelle machine virtuelle Azure est-elle placée dans un nouveau réseau virtuel Microsoft Azure, peut-être dans une autre région, et homologuée avec le réseau virtuel existant ? Vous pouvez ensuite utiliser la clé et l’ID de ressource du service ExpressRoute à partir de l’approvisionnement HLI d’origine pour autoriser l’accès à cette nouvelle plage d’adresses IP de réseau virtuel. Coordonnez-vous avec Microsoft Service Management pour activer la connectivité entre le réseau virtuel et HLI.  
    > [!NOTE]
    > pour réduire le temps de réponse du réseau entre les couches d'application et de base de données, ces deux couches doivent se trouver sur le même réseau virtuel.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Groupe à haute disponibilité, zones de disponibilité et groupe de placement de proximité de la couche d’application existante (PPG)
Le modèle de déploiement actuel a été conçu pour respecter certains objectifs de niveau de service. Vous devez donc veiller à ce que l’infrastructure cible atteigne ou dépasse vos objectifs fixés.  
Généralement, vos serveurs d’application SAP sont placés dans un groupe à haute disponibilité. Si le niveau de service de déploiement actuel est satisfaisant et que la machine virtuelle cible utilise le nom d’hôte du nom logique HLI, la mise à jour de la résolution d’adresse DNS pointant vers l’adresse IP de la machine virtuelle fonctionne sans mettre à jour les profils SAP
- Si vous n'utilisez pas groupe de placement de proximité (PPG), veillez à placer tous les serveurs d'applications et de bases de données dans la même zone pour réduire le temps de réponse du réseau.
- Si vous utilisez PPG, reportez-vous à une section ultérieure de cet article, [Groupes à haute disponibilité, zones de disponibilité et groupes de placement de proximité](#availability-sets-availability-zones-and-proximity-placement-groups).

### <a name="storage-replication-discontinuance-process-if-used"></a>Processus d'interruption de la réplication du stockage (le cas échéant)
Si vous avez utilisé la réplication de stockage comme solution de récupération d’urgence, terminez-la après l’arrêt de l’application SAP. Avant cela, le dernier catalogue SAP HANA, vérifiez que le dernier fichier journal et les dernières sauvegardes de données ont été répliqués sur les volumes de stockage HLI DR distants. Cette réplication est importante dans le cas où un incident se produit pendant la transition du serveur physique à la machine virtuelle Azure.

### <a name="data-backups-preservation-consideration"></a>Considérations relatives à la conservation des sauvegardes de données
Après la transition vers SAP HANA sur votre machine virtuelle Azure, les sauvegardes de données et de journaux basées sur des instantanés sur la HLI ne seront pas facilement accessibles ou restaurables sur une machine virtuelle. Nous vous recommandons d’effectuer des sauvegardes et des instantanés au niveau du fichier sur le HLI, même des semaines avant le basculement. Veillez à ce que ces sauvegardes soient copiées sur un compte de stockage Azure accessible à la nouvelle machine virtuelle SAP HANA. Au cours de la période de transition précoce, avant que la sauvegarde Azure ne crée suffisamment d’historique pour satisfaire aux exigences de récupération jusqu’à une date et heure, effectuez des sauvegardes au niveau des fichiers. 

La sauvegarde du contenu HLI est critique. Il est également prudent d’avoir des sauvegardes complètes du paysage SAP facilement accessibles au cas où un retour en arrière serait nécessaire.

### <a name="adjusting-system-monitoring"></a>Ajustement de la surveillance du système 
Vous pouvez utiliser de nombreux outils différents afin de surveiller les systèmes de votre paysage SAP et d’envoyer des notifications d’alerte sur ceux-ci. N’oubliez pas d’effectuer l’action qui convient afin d’intégrer des modifications pour la surveillance et de mettre à jour les destinataires des notifications d’alerte, si nécessaire.

### <a name="microsoft-operations-team-involvement"></a>Implication de l'équipe des opérations de Microsoft 
Ouvrez un ticket à partir du portail Azure sur la base de l'instance HLI existante.  Une fois le ticket de support créé, un ingénieur du support vous contactera par e-mail.  

### <a name="engage-microsoft-account-team"></a>Impliquer l'équipe des comptes Microsoft
Planifiez la migration à l’approche de la date anniversaire du renouvellement du contrat HLI afin d’éviter les dépenses inutiles en termes de ressources de calcul. Pour désactiver le serveur HLI, coordonnez la fin du contrat et la fermeture de l’unité.

## <a name="destination-planning"></a>Planification de la destination
Une planification soignée est essentielle dans le déploiement d’une nouvelle infrastructure pour remplacer une infrastructure existante. Assurez-vous que le nouvel ajout répondra à vos besoins généraux. Voici quelques points clés à prendre en considération.

### <a name="resource-availability-in-the-target-region"></a>Disponibilité des ressources dans la région cible 
La région de déploiement actuelle du serveur d’applications SAP se trouve généralement à proximité immédiate des serveurs HLI associés. Toutefois, les emplacements disponibles pour les serveurs HLI sont moins nombreux que les régions Azure. La migration de serveurs HLI physiques vers des machines virtuelles Azure est également l’occasion d’affiner la distance de proximité de tous les services associés afin d’optimiser les performances.  Ce faisant, assurez-vous que la région choisie dispose de toutes les ressources nécessaires. Par exemple, vous souhaiterez peut-être vérifier la disponibilité d’une certaine famille de machines virtuelles ou des zones Azure offrant une configuration haute disponibilité.

### <a name="virtual-network"></a>Réseau virtuel 
Voulez-vous exécuter la nouvelle base de données HANA dans un réseau virtuel existant ou en créer un nouveau ? Ce choix dépendra principalement du schéma actuel de mise en réseau du paysage SAP. De même, lorsque l’infrastructure passe d’un déploiement à une zone à un déploiement à deux zones et utilise PPG, un changement architectural s’impose. Pour plus d'informations, consultez l'article [Groupes de placement de proximité Azure pour un temps de réponse optimal du réseau avec les applications SAP](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Sécurité
Que la nouvelle machine virtuelle SAP HANA s’exécute sur un réseau/sous-réseau virtuel nouveau ou existant, il s’agit d’un nouveau service essentiel pour votre entreprise. Il mérite d’être protégé. Assurez-vous que le contrôle d’accès est conforme à la stratégie de sécurité de votre entreprise.

### <a name="vm-sizing-recommendation"></a>Recommandation relative au redimensionnement des machines virtuelles
Cette migration est également l'occasion de redimensionner correctement votre moteur de calcul HANA. Vous pouvez utiliser les [vues du système](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA avec HANA Studio pour analyser la consommation des ressources système, ce qui permettra un redimensionnement adéquat et une amélioration de la rentabilité.

### <a name="storage"></a>Stockage 
Les performances de stockage comptent parmi les facteurs qui influencent l’expérience utilisateur des applications SAP. Des dispositions de stockage minimales sont publiées pour les références de machine virtuelle fournies. Pour plus d’informations, consultez [Configurations du stockage des machines virtuelles SAP HANA Azure](./hana-vm-operations-storage.md). Nous vous recommandons de prendre connaissance de ces spécifications et de les comparer aux statistiques de votre système HLI existant afin de garantir une capacité et des performances d’E/S adéquates pour votre nouvelle machine virtuelle HANA.

Configurerez-vous PPG pour la nouvelle machine virtuelle HANA et ses serveurs associés ? Envoyez ensuite un ticket de support pour inspecter et vérifier la co-localisation du stockage et de la machine virtuelle. Dans la mesure où votre solution de sauvegarde peut nécessiter des modifications, le coût de stockage doit également être revu afin d’éviter des dépenses opérationnelles imprévues.

### <a name="storage-replication-for-disaster-recovery"></a>Réplication du stockage pour la récupération d'urgence
Avec HLI, la réplication du stockage constituait l’option par défaut en termes de récupération d’urgence. Pour SAP HANA sur machine virtuelle Azure, cette fonctionnalité n’est pas l’option par défaut. Envisagez la réplication HSR, la sauvegarde/restauration ou d’autres solutions prises en charge en fonction des besoins de votre entreprise.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Groupes à haute disponibilité, zones de disponibilité et groupes de placement de proximité 
Vous pouvez raccourcir la distance entre la couche application et SAP HANA pour réduire au minimum la latence du réseau. Placez la nouvelle machine virtuelle de base de données et les serveurs d’application SAP actuels dans un PPG. Consultez [Groupe de placement de proximité](./sap-proximity-placement-scenarios.md) pour en savoir plus sur le fonctionnement du groupe à haute disponibilité et des zones de disponibilité Azure avec un PPG dans le cadre des déploiements SAP.

Si des membres de votre système HANA sont déployés dans plusieurs zones Azure, vous devez être conscient du profil de latence des zones choisies. Placez les composants du système SAP pour réduire la distance entre l’application SAP et la base de données. L'[outil de test de latence de la zone de disponibilité](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) du domaine public facilite la mesure.  


### <a name="backup-strategy"></a>Stratégie de sauvegarde
Beaucoup de nos clients utilisent déjà des solutions de sauvegarde tierces pour SAP HANA sur HLI.  Si c’est le cas, seules les bases de données de machine virtuelle et HANA protégées doivent être configurées. Les tâches de sauvegarde HLI en cours peuvent ne plus être planifiées si la machine est désactivée après la migration.

La fonctionnalité Sauvegarde Azure pour SAP HANA sur machine virtuelle est désormais généralement disponible.  Pour plus d’informations sur la sauvegarde de SAP HANA dans les machines virtuelles Azure, voir [Sauvegarder](../../../backup/backup-azure-sap-hana-database.md), [Restaurer](../../../backup/sap-hana-db-restore.md) et [Gérer](../../../backup/sap-hana-db-manage.md).

### <a name="dr-strategy"></a>Stratégie de récupération d'urgence
Si vos objectifs de niveau de service prennent en charge un temps de récupération plus long, la sauvegarde peut être facile. Une sauvegarde sur un Stockage Blob et une restauration sur place ou vers une nouvelle machine virtuelle est la stratégie de récupération d’urgence la plus simple et la moins coûteuse.
 
Sur la plateforme de grande instance, la récupération d’urgence HANA s’effectue généralement avec HSR. Sur une machine virtuelle Azure, HSR est également la solution de récupération d’urgence SAP HANA la plus naturelle et la plus native. Que le déploiement source corresponde à instance unique ou un cluster, un réplica de l’infrastructure source est requis dans la région de récupération d’urgence.
Ce réplica de récupération d'urgence sera configuré au terme de la migration du serveur HLI principal vers la machine virtuelle.  La base de données HANA de récupération d'urgence s'enregistrera auprès de l'instance principale SAP HANA sur machine virtuelle en tant que site de réplication secondaire.  

### <a name="sap-application-server-connectivity-destination-change"></a>Changement de la destination de connectivité du serveur d'applications SAP
La migration HSR se traduit par un nouvel hôte de base de données HANA et également par un nouveau nom d’hôte de base de données pour la couche d’application. Modifiez les profils SAP pour refléter le nouveau nom d’hôte. Si le changement s'effectue par résolution de noms en conservant le nom d'hôte, aucune modification de profil n'est requise.

### <a name="operating-system-os"></a>Système d’exploitation (OS)
Les images de système d’exploitation du serveur HLI et de la machine virtuelle, même si elles présentent le même niveau de version, SLES 12 SP4 par exemple, ne sont pas identiques. Validez les packages, correctifs logiciels, correctifs à chaud, noyaux et correctifs de sécurité requis sur le serveur HLI. Installez ensuite les mêmes packages sur la cible.  Vous pouvez utiliser HSR à des fins de réplication d’un ancien système d’exploitation vers une machine virtuelle dotée d’une version plus récente du système d’exploitation est prise en charge.  Vérifiez les versions prises en charge en consultant [Note SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Demande de nouvelle licence SAP
Il s'agit simplement de demander une nouvelle licence SAP pour le nouveau système HANA maintenant qu'il a été migré vers des machines virtuelles.

### <a name="service-level-agreement-sla-differences"></a>Différences de contrats de niveau de service (SLA)
Les auteurs tiennent à rappeler qu'il existe une différence de SLA de disponibilité entre un serveur HLI et une machine virtuelle Azure.  Par exemple, les paires haute disponibilité des serveurs HLI en cluster offrent une disponibilité de 99,99 %. Pour bénéficier du même contrat SLA, vous devez déployer les machines virtuelles dans des zones de disponibilité. [SLA pour les machines virtuelles](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) décrit la disponibilité pour diverses configurations de machine virtuelle afin que les clients puissent planifier leur infrastructure cible.


## <a name="migration-strategy"></a>Stratégie de migration
Ce document couvre uniquement l'approche de réplication du système HANA pour la migration entre un serveur HLI et une machine virtuelle Azure.  Selon la solution de stockage cible déployée, le processus peut être légèrement différent. Les principales étapes sont décrites ci-dessous.

### <a name="vm-with-premiumultra-disks-for-data"></a>Machine virtuelle avec disques Premium/Ultra pour les données
Pour les machines virtuelles déployées avec des disques Premium ou Ultra, la configuration standard de la réplication du système SAP HANA peut s’appliquer pour configurer la réplication HSR. Pour un aperçu des étapes de la configuration de la réplication du système, consultez l’[article d’aide SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html). L’article traite également de la prise de contrôle d’un système secondaire, du retour sur le système principal et de la désactivation de la réplication du système. Pour la migration, nous aurons uniquement besoin des étapes de configuration, de basculement et de désactivation de la réplication.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Machine virtuelle avec ANF pour les volumes de données et de journaux
À un niveau élevé, les derniers instantanés de stockage HLI des volumes complets de données et de journaux doivent être copiés sur le stockage Azure. À partir de là, ils sont accessibles et récupérables par la machine virtuelle HANA cible.  La copie peut être effectuée avec n'importe quel outil de copie Linux natif.  

> [!IMPORTANT]
> La copie et le transfert de données peuvent prendre des heures en fonction de la taille de la base de données HANA et de la bande passante réseau disponible. La majeure partie de la copie doit être effectuée avant le temps d’arrêt de la base de données HANA principale.

### <a name="mcos-to-mdc-conversion"></a>Conversion de MCOS en MDC
Le modèle de déploiement MCOS (Multiple Components in One System) a été utilisé par certains de nos clients HLI. Leur motivation était de contourner la limitation des captures instantanées de stockage MDC (Multiple Databases Container) des versions antérieures de SAP HANA.  Dans le modèle MCOS, plusieurs instances SAP HANA indépendantes sont empilées sur une même grande instance HANA. L’utilisation de la réplication HSR pour la migration fonctionnait bien, mais impliquait plusieurs machines virtuelles HANA dotées d’une base de données locataire chacune. Ce modèle peut constituer un paysage plus dense que vous le voulez. Le déploiement par défaut pour SAP HANA 2.0 est MDC. Une alternative est d’effectuer un [déplacement du locataire HANA](https://launchpad.support.sap.com/#/notes/2472478) après la migration HSR. Le déplacement de locataire HANA combine ces bases de données HANA indépendantes en colocataires dans un seul conteneur HANA.  

### <a name="application-layer-consideration"></a>Considérations relatives à la couche d'application
Le serveur de bases de données est considéré comme le centre d’un système SAP.  Tous les serveurs d’applications doivent se trouver à proximité de la base de données SAP HANA. Dans certains cas, lorsque vous souhaitez utiliser un nouveau PPG, vous devrez peut-être déplacer les serveurs d’applications existants vers le PPG où se trouve la machine virtuelle HANA.  La création de nouveaux serveurs d’applications peut être jugée plus facile si vous disposez déjà de modèles de déploiement.

Recherchez les serveurs d’applications existants et la nouvelle machine virtuelle HANA de manière optimale. Vous n’avez alors pas besoin de créer de nouveaux serveurs d’applications, sauf si vous souhaitez une capacité supérieure.

Lorsque vous créez une nouvelle infrastructure pour améliorer la disponibilité du service, vos serveurs d’applications existants peuvent devenir inutiles. Vous pouvez les arrêter et les supprimer.
Si le nom d’hôte de la machine virtuelle cible a changé et qu’il est différent du nom d’hôte HLI, les profils des serveurs d’applications SAP doivent être modifiés pour pointer vers le nouvel hôte. Si seule l’adresse IP de la base de données HANA a changé, une mise à jour de l’enregistrement DNS est nécessaire pour diriger les connexions entrantes vers la nouvelle machine virtuelle HANA.

### <a name="acceptance-test"></a>Test d'acceptation
La migration de HLI vers une machine virtuelle n’apporte aucun changement matériel au contenu de la base de données par rapport à une migration hétérogène. Toutefois, nous vous recommandons de vérifier les performances de la nouvelle configuration.  

### <a name="cutover-plan"></a>Plan de basculement
Bien que cette migration soit simple, elle implique toutefois la désactivation d’une base de données existante.  Une planification minutieuse visant à préserver le système source, son contenu et les images de sauvegarde est essentielle au cas où une solution de secours serait nécessaire. Une bonne planification permet une restauration plus rapide.   

## <a name="post-migration"></a>Après la migration
La tâche de migration n’est pas terminée tant que nous n’avons pas procédé à un découplage sécurisé des services ou de la connectivité du serveur HLI pour garantir l’intégrité des données. Nous vous recommandons également d’arrêter les services inutiles. Cette section rappelle quelques-uns des éléments les plus importants.

### <a name="decommissioning-the-hli"></a>Désactivation du serveur HLI
Après avoir correctement migré la base de données HANA vers une machine virtuelle Azure, assurez-vous qu’aucune transaction commerciale n’est exécutée sur la base de données HLI.  Toutefois, conserver le HLI en cours d’exécution pendant la durée de sa fenêtre de rétention de sauvegarde locale garantira une récupération plus rapide si nécessaire. Si la fenêtre de rétention de la sauvegarde locale est passée, vous devez désactiver la grande instance HANA. Concluez alors vos engagements contractuels HLI avec Microsoft en contactant leurs représentants Microsoft.

### <a name="remove-any-proxy-for-example-iptables-bigip-configured-for-hli"></a>Supprimer un proxy (par ex. iptables, BIGIP) configuré pour HLI 
Si un service proxy comme IPTables est utilisé pour acheminer le trafic local vers et depuis le serveur HLI, il n’est plus nécessaire après une migration réussie vers une machine virtuelle.  Cependant, ce service de connectivité doit être conservé aussi longtemps que HLI reste opérationnel.  N’arrêtez le service qu’une fois HLI entièrement désactivé.

### <a name="remove-global-reach-for-hli"></a>Supprimer Global Reach pour HLI 
Global Reach est utilisé pour connecter la passerelle ExpressRoute des clients à la passerelle ExpressRoute du serveur HLI.  Cela permet au trafic local des clients d'atteindre directement le locataire HLI, sans recourir à un service proxy. Cette connexion n’est plus nécessaire en l’absence de l’unité HLI après la migration. Cela étant, comme pour le service proxy IPTables, Global Reach doit aussi être conservé jusqu’à ce que HLI soit entièrement désactivé.

### <a name="operating-system-subscription--movereuse"></a>Abonnement au système d'exploitation - déplacer/réutiliser
À mesure que les serveurs de machines virtuelles sont déployés et que les HLI sont désactivés, les abonnements du système d’exploitation peuvent être remplacés ou réutilisés. Il n’est pas nécessaire de payer deux fois pour les licences de système d’exploitation.

## <a name="next-steps"></a>Étapes suivantes

Planifiez votre déploiement SAP.

> [!div class="nextstepaction"]
> [Check-list relative à la planification et au déploiement de la charge de travail SAP sur Azure](sap-deployment-checklist.md)
