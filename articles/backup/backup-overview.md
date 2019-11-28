---
title: Qu’est-ce qu’Azure Backup ?
description: Fournit une vue d’ensemble du service Sauvegarde Azure et de la façon de le déployer dans le cadre de votre stratégie de continuité d’activité et de reprise d’activité (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 49ef90c874e920ef92dd5206447081a6aa4f7988
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172197"
---
# <a name="what-is-the-azure-backup-service"></a>Qu’est-ce que le service Sauvegarde Azure ?

Le service Sauvegarde Azure sauvegarde des données dans le cloud Microsoft Azure. Vous pouvez sauvegarder des machines et charges de travail locales, ainsi que des machines virtuelles Azure.

## <a name="why-use-azure-backup"></a>Pourquoi utiliser Azure Backup ?

Le service Sauvegarde Azure offre les principaux avantages suivants :

- **Sauvegarde locale de déchargement** : Le service Sauvegarde Azure offre une solution simple pour la sauvegarde de vos ressources locales dans le cloud. Obtenez une sauvegarde à court terme et à long terme sans avoir besoin de déployer des solutions de sauvegarde locale complexes.
- **Sauvegarder des machines virtuelles Azure IaaS** : Le service Sauvegarde Azure fournit des sauvegardes indépendantes et isolées pour éviter une destruction accidentelle des données d’origine. Les sauvegardes sont stockées dans un coffre Recovery Services avec la gestion intégrée des points de récupération. La configuration et la scalabilité sont simples : les sauvegardes sont optimisées, et vous pouvez facilement effectuer des restaurations en fonction des besoins.
- **Mettre à l’échelle facilement** : Sauvegarde Azure utilise la puissance sous-jacente et la mise à l’échelle illimitée du cloud Azure pour offrir une haute disponibilité, sans tâche supplémentaire de maintenance ou de supervision.
- **Obtenir un transfert de données illimitées** : La Sauvegarde Azure ne limite pas la quantité de données entrantes ou sortantes transférées, et ne génère pas de frais pour les données transférées.
  - Les données sortantes sont les données transférées à partir d’un coffre Recovery Services pendant une opération de restauration.
  - Si vous effectuez une sauvegarde initiale hors connexion avec le service Azure Import/Export pour importer de grandes quantités de données, des frais associés aux données entrantes sont facturés.  [Plus d’informations](backup-azure-backup-import-export.md)
- **Sécuriser les données** : Sauvegarde Azure fournit des solutions pour sécuriser les données en transit et au repos.
- **Obtenir des sauvegardes cohérentes au niveau application** : une sauvegarde cohérente au niveau application signifie qu’un point de récupération dispose de toutes les données nécessaires pour restaurer la copie de sauvegarde. Sauvegarde Microsoft Azure fournit des sauvegardes cohérentes avec les applications, qui garantissent qu’aucun correctif supplémentaire n’est requis pour restaurer les données. La restauration de données cohérentes avec les applications réduit le délai de restauration, ce qui permet de rétablir rapidement le fonctionnement normal.
- **Conserver des données à court et à long terme** : Vous pouvez utiliser des coffres Recovery Services pour la conservation de données à court et à long terme. Azure ne limite pas la durée de conservation des données dans un coffre Recovery Services. Vous pouvez les conserver dans un coffre aussi longtemps que vous le souhaitez. Azure Backup est limité à 9 999 points de récupération par instance protégée.
- **Gestion automatique du stockage** : les environnements hybrides impliquent souvent un stockage hétérogène (une partie en local et une autre dans le cloud). Avec Azure Backup, l’utilisation d’appareils de stockage locaux ne génère aucun coût. Sauvegarde Azure alloue et gère automatiquement le stockage des sauvegardes sur la base d’un modèle de paiement à l’utilisation : vous ne payez donc que le stockage que vous consommez. [Apprenez-en davantage sur la](https://azure.microsoft.com/pricing/details/backup) tarification.
- **Plusieurs options de stockage** : Sauvegarde Azure offre deux types de réplication pour conserver votre stockage/vos données hautement disponibles.
  - Le [stockage localement redondant (LRS)](../storage/common/storage-redundancy-lrs.md) réplique vos données trois fois (il crée trois copies de vos données) dans une unité d’échelle de stockage d’un centre de données. Toutes les copies des données existent dans la même région. Le stockage LRS est une option à faible coût qui protège vos données contre les défaillances matérielles locales.
  - Le [stockage géoredondant (GRS)](../storage/common/storage-redundancy-grs.md) est l’option de réplication par défaut : c’est l’option recommandée. Le stockage géo-redondant réplique vos données vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il offre une durabilité des données supérieure, même en cas de panne au niveau régional.

## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Quelle est la différence entre Sauvegarde Azure et Azure Site Recovery ?

Les services Sauvegarde Azure et Azure Site Recovery participent tous les deux à une stratégie de continuité d’activité et de reprise d’activité (BCDR) dans votre entreprise. BCDR comprend deux grands objectifs :

- Préserver la sécurité et la capacité de récupération de vos données métier en cas de pannes.
- Maintenir vos applications et charges de travail opérationnelles pendant les temps d’arrêt planifiés et non planifiés.

Les deux services fournissent des fonctionnalités complémentaires mais différentes.

- **Azure Site Recovery** : Site Recovery fournit une solution de reprise d’activité pour les machines locales et les machines virtuelles Azure. Vous répliquez des machines d’un emplacement principal vers un emplacement secondaire. Quand un incident survient, vous basculez les machines vers l’emplacement secondaire et vous y accédez à partir de cet emplacement. Quand tout est à nouveau opérationnel, vous rebasculez les machines pour les récupérer sur le site principal.
- **Sauvegarde Azure** : Le service Sauvegarde Azure sauvegarde les données de machines locales et de machines virtuelles Azure. Les données peuvent être sauvegardées et restaurées à un niveau précis, avec notamment la sauvegarde des fichiers, des dossiers, de l’état du système de la machine et des données tenant compte des applications. Sauvegarde Azure gère les données à un niveau plus détaillé que Site Recovery. Par exemple, si une présentation sur votre ordinateur portable est endommagée, vous utilisez Sauvegarde Azure pour la restaurer. Si vous voulez préserver la sécurité et l’accessibilité de la configuration et des données d’une machine virtuelle, vous pouvez utiliser Site Recovery.  

Utilisez les points du tableau pour vous aider à déterminer vos besoins en continuité d’activité et de reprise d’activité (BCDR).

**Objectif** | **Détails** | **Comparaison**
--- | --- | ---
**Conservation/sauvegarde de données** | Les données de sauvegarde peuvent être conservées et stockées pendant des jours, des mois, voire même des années si nécessaire pour des raisons de conformité. | Les solutions de sauvegarde comme le service Sauvegarde Azure vous permettent de choisir précisément les données que vous voulez sauvegarder et de paramétrer avec précision les stratégies de sauvegarde et de conservation.<br/><br/> Site Recovery ne permet pas la même précision dans les ajustements.
**Objectif de point de récupération (RPO)** | Quantité de perte de données acceptable si la récupération doit être exécutée. | Les sauvegardes ont un objectif de point de récupération (RPO) plus variable.<br/><br/> Les sauvegardes de machines virtuelles ont généralement un RPO d’un jour, contre seulement 15 minutes pour les sauvegardes de base de données.<br/><br/> Site Recovery fournit un RPO faible car la réplication est continue ou fréquente, de sorte que le delta entre la source et la copie de réplica est faible.
**Objectif de délai de récupération (RTO)** |Quantité de temps nécessaire pour effectuer une récupération ou une restauration complète. | Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui rallonge d’autant le RTO. Par exemple, il peut falloir plusieurs jours pour restaurer des données à partir de bandes, selon le temps nécessaire au transport de la bande depuis un site externe.

## <a name="what-backup-scenarios-are-supported"></a>Quels sont les scénarios de sauvegarde pris en charge ?

La Sauvegarde Azure peut sauvegarder aussi bien les machines locales que les machines virtuelles Azure.

**Machine** | **Scénario de sauvegarde**
--- | ---
**Sauvegarde locale** |  1) Exécutez l’agent Microsoft Azure Recovery Services (MARS) de Sauvegarde Azure sur des machines Windows locales pour sauvegarder des fichiers individuels et l’état du système. <br/><br/>2) Sauvegardez les machines locales sur un serveur de sauvegarde, comme DPM (System Center Data Protection Manager) ou MABS (Microsoft Azure Backup Server), puis configurez le serveur de sauvegarde pour la sauvegarde dans un coffre Recovery Services du service Sauvegarde Azure dans Azure.
**Machines virtuelles Azure** | 1) Activez la sauvegarde pour les machines virtuelles Azure individuelles. Quand vous activez la sauvegarde, Sauvegarde Azure installe une extension de l’agent de machine virtuelle Azure qui s’exécute sur la machine virtuelle. L’agent sauvegarde la totalité de la machine virtuelle.<br/><br/> 2) Exécutez l’agent MARS sur une machine virtuelle Azure. C’est pratique si vous voulez sauvegarder des fichiers et des dossiers individuels sur la machine virtuelle.<br/><br/>

## <a name="why-use-a-backup-server"></a>Pourquoi utiliser un serveur de sauvegarde ?

Les avantages de la sauvegarde des machines et applications dans le stockage MABS/DPM, puis de la sauvegarde de ce stockage dans un coffre sont les suivants :

- La sauvegarde dans MABS/DPM fournit des sauvegardes tenant compte des applications et optimisées pour les applications courantes comme SQL Server, Exchange et SharePoint, en plus des sauvegardes de fichiers/dossiers/volumes et des sauvegardes de l’état de la machine (sauvegarde complète, état du système).
- Pour les machines locales, vous n’avez pas besoin d’installer l’agent MARS sur chaque machine que vous voulez sauvegarder. Chaque machine exécute l’agent de protection DPM/MABS, et l’agent MARS s’exécute seulement sur MABS/DPM.
- Vous disposez de davantage de flexibilité et d’options de planification plus précises pour l’exécution de sauvegardes.
- Vous pouvez gérer les sauvegardes de plusieurs machines que vous rassemblez en groupes de protection dans une console unique. C’est utile quand les applications sont hiérarchisées entre plusieurs machines et que vous voulez les sauvegarder ensemble.

Découvrez plus d’informations sur le [fonctionnement de la sauvegarde](backup-architecture.md#architecture-back-up-to-dpmmabs) lors de l’utilisation d’un serveur de sauvegarde et les [exigences de prise en charge](backup-support-matrix-mabs-dpm.md) pour les serveurs de sauvegarde.

## <a name="what-can-i-back-up"></a>Que puis-je sauvegarder ?

**Machine** | **Méthode de sauvegarde** | **Sauvegarder**
--- | --- | ---
**Machines virtuelles Windows locales** | Exécuter l’agent MARS | Sauvegardez des fichiers, des dossiers, l’état du système.<br/><br/> Machines Linux non prises en charge.
**Ordinateurs locaux** | sauvegarder dans DPM/MABS | Sauvegarder tout ce qui est protégé par [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) ou [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), notamment les fichiers/dossiers/partages/volumes et les données spécifiques aux applications.
**Machines virtuelles Azure** | Exécuter l’extension de sauvegarde de l’agent de machine virtuelle Azure | Sauvegarder la totalité de la machine virtuelle
**Machines virtuelles Azure** | Exécuter l’agent MARS | Sauvegardez des fichiers, des dossiers, l’état du système.<br/><br/> Machines Linux non prises en charge.
**Machines virtuelles Azure** | Sauvegarder sur MABS/DPM s’exécutant dans Azure | Sauvegarder tout ce qui est protégé par [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) ou [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807), notamment les fichiers/dossiers/partages/volumes et les données spécifiques aux applications.

## <a name="what-backup-agents-do-i-need"></a>De quels agents de sauvegarde ai-je besoin ?

**Scénario** | **Agent**
--- | ---
**Sauvegarder des machines virtuelles Azure** | Aucun agent n’est nécessaire. L’extension de machine virtuelle Azure pour la sauvegarde est installée sur la machine virtuelle Azure quand vous exécutez la première sauvegarde de la machine virtuelle Azure.<br/><br/> Prise en charge pour les machines Windows et Linux.
**Sauvegarde de machines Windows locales** | Téléchargez, installez et exécutez l’agent MARS directement sur la machine.
**Sauvegarder des machines virtuelles Azure avec l’agent MARS** | Téléchargez, installez et exécutez l’agent MARS directement sur la machine. L’agent MARS peut s’exécuter en même temps que l’extension de sauvegarde.
**Sauvegarder des machines locales et des machines virtuelles Azure vers DPM/MABS** | L’agent de protection DPM ou MABS s’exécute sur les ordinateurs que vous voulez protéger. L’agent MARS s’exécute sur le serveur DPM/MABS pour sauvegarder sur Azure.

## <a name="which-backup-agent-should-i-use"></a>Quel agent de sauvegarde dois-je utiliser ?

**Sauvegarde** | **Solution** | **Limite**
--- | --- | ---
**Je veux sauvegarder la totalité d’une machine virtuelle Azure** | Activez la sauvegarde pour la machine virtuelle. L’extension de sauvegarde est automatiquement configurée sur la machine virtuelle Azure Windows ou Linux. | La totalité de la machine virtuelle est sauvegardée <br/><br/> Pour les machines virtuelles Windows, la sauvegarde est cohérente au niveau application. Pour Linux, la sauvegarde est cohérente au niveau fichier. Si vous avez besoin que les applications soient prises en compte pour les machines virtuelles Linux, vous devez le configurer avec des scripts personnalisés.
**Je veux sauvegarder des fichiers/dossiers spécifiques sur la machine virtuelle Azure** | Déployez l’agent MARS sur la machine virtuelle.
**Je veux sauvegarder directement des machines Windows locales** | Installez l’agent MARS sur la machine. | Vous pouvez sauvegarder des fichiers, des dossiers et l’état du système dans Azure. Les sauvegardes ne tiennent pas compte des applications.
**Je veux sauvegarder directement des machines Linux locales** | Vous devez déployer DPM ou MABS pour effectuer une sauvegarde dans Azure. | La sauvegarde de l’hôte Linux n’est pas prise en charge. Vous pouvez uniquement sauvegarder les machines invitées Linux qui sont hébergées dans Hyper-V ou VMWare.
**Je veux sauvegarder des applications s’exécutant en local** | Pour les sauvegardes prenant en compte les applications, les machines doivent être protégées par DPM ou MABS.
**Je veux des paramètres de sauvegarde et de récupération précis et flexibles pour les machines virtuelles Azure** | Protégez les machines virtuelles Azure avec MABS/DPM s’exécutant dans Azure afin de bénéficier d’une flexibilité supplémentaire pour la planification des sauvegardes et d’une flexibilité totale pour la protection et la restauration de fichiers, de dossiers, de volumes, d’applications et de l’état du système.

## <a name="backup-and-retention"></a>Sauvegarde et rétention

La solution Sauvegarde Azure présente une limite de 9 999 points de récupération, également appelés copies ou instantanés de sauvegarde, par *instance protégée*.

- Une instance protégée est un ordinateur, un serveur (physique ou virtuel) ou une charge de travail configurés pour sauvegarder des données dans Azure. Une instance est protégée une fois qu’une copie de sauvegarde des données a été enregistrée.
- La copie de sauvegarde des données constitue la protection. Si les données sources sont perdues ou endommagées, la copie de sauvegarde peut les restaurer.

Le tableau ci-après indique la fréquence de sauvegarde maximale pour chaque composant. Votre configuration de votre stratégie de sauvegarde détermine la rapidité avec laquelle vous consommez les points de récupération. Par exemple, si vous créez un point de récupération chaque jour, vous pouvez conserver les points de récupération pendant 27 ans avant d’en manquer. Si vous optez pour un point de récupération par mois, vous pouvez conserver les points de récupération pendant 833 ans avant d’en manquer. Le service de sauvegarde ne définit pas de délai d’expiration pour un point de récupération.

|  | Agent Azure Backup (MARS)| System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Fréquence de sauvegarde<br/> (vers un coffre Recovery Services) |Trois sauvegardes par jour |Deux sauvegardes par jour |Deux sauvegardes par jour |Une sauvegarde par jour |
| Fréquence de sauvegarde<br/> (vers le disque) |Non applicable |Toutes les 15 minutes pour SQL Server<br/><br/> Toutes les heures pour les autres charges de travail |Toutes les 15 minutes pour SQL Server<br/><br/> Toutes les heures pour les autres charges de travail |Non applicable |
| Options de rétention |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |
| Nombre maximal de points de récupération par instance protégée |9 999|9 999|9 999|9 999|
| Période de rétention maximale |Dépend de la fréquence de sauvegarde |Dépend de la fréquence de sauvegarde |Dépend de la fréquence de sauvegarde |Dépend de la fréquence de sauvegarde |
| Points de récupération sur le disque local |Non applicable | 64 pour les serveurs de fichiers<br/><br/> 448 pour les serveurs d’applications | 64 pour les serveurs de fichiers<br/><br/> 448 pour les serveurs d’applications |Non applicable |
| Points de récupération sur bande |Non applicable |Illimité |Non applicable |Non applicable |

## <a name="how-does-azure-backup-work-with-encryption"></a>Comment fonctionne la Sauvegarde Azure avec le chiffrement ?

**Chiffrement** | **Sauvegarder en local** | **Sauvegarder des machines virtuelles Azure** | **Sauvegarder SQL sur une machine virtuelle Azure**
--- | --- | --- | ---
Chiffrement au repos<br/> (Chiffrement des données là où elles sont conservées/stockées) | Une phrase secrète spécifiée par le client est utilisée pour chiffrer les données | Le service Azure [Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) est utilisé pour chiffrer les données stockées dans le coffre.<br/><br/> La Sauvegarde chiffre automatiquement les données avant de les stocker. Le Stockage Azure déchiffre les données avant de les récupérer. L’utilisation de clés gérées par le client pour SSE n’est actuellement pas prise en charge.<br/><br/> Vous pouvez sauvegarder des machines virtuelles qui utilisent [le chiffrement de disque Azure (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) pour chiffrer les disques de données et le système d’exploitation. La sauvegarde Azure prend en charge les machines virtuelles chiffrées avec BEK uniquement, et avec à la fois BEK et [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Examinez les [limitations](backup-azure-vms-encryption.md#encryption-support). | La sauvegarde Azure prend en charge la sauvegarde de bases de données SQL Server ou de serveur avec [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) activé. La sauvegarde prend en charge le chiffrement transparent des données avec des clés gérées par Azure, ou avec des clés gérées par le client (BYOK).<br/><br/> La sauvegarde ne prend en charge aucun chiffrement SQL dans le cadre du processus de sauvegarde.
Chiffrement en transit<br/> (Chiffrement des données déplacées d’un emplacement vers un autre) | Les données sont chiffrées à l’aide de AES256 et envoyées vers le coffre dans Azure via HTTPS | Dans Azure, les données entre le stockage Azure et le coffre sont protégées par HTTPS. Ces données restent sur le réseau principal Azure.<br/><br/> Pour la récupération de fichier, iSCSI sécurise les données transmises entre le coffre et la machine virtuelle Azure. Le tunneling sécurisé protège le canal iSCSI. | Dans Azure, les données entre le stockage Azure et le coffre sont protégées par HTTPS.<br/><br/> La récupération de fichiers n’a aucune utilité pour le SQL.

## <a name="next-steps"></a>Étapes suivantes

- [Examinez](backup-architecture.md) l’architecture et les composants de différents scénarios de sauvegarde.
- [Vérifiez](backup-support-matrix.md) les exigences de prise en charge et les limitations pour la sauvegarde et la [sauvegarde de machine virtuelle Azure](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
