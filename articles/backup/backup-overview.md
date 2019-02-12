---
title: Qu’est-ce qu’Azure Backup ?
description: Fournit une vue d’ensemble du service Sauvegarde Azure et de la façon de le déployer dans le cadre de votre stratégie de continuité d’activité et de reprise d’activité (BCDR).
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 298c9fabca9d1994e0b952fdf8b48b70370c3ec2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490667"
---
# <a name="what-is-azure-backup"></a>Qu’est-ce qu’Azure Backup ?

Le service Sauvegarde Azure sauvegarde des données dans le cloud Microsoft Azure. Vous pouvez sauvegarder des machines et charges de travail locales, ainsi que des machines virtuelles Azure.


## <a name="why-use-azure-backup"></a>Pourquoi utiliser Azure Backup ?

Le service Sauvegarde Azure offre les principaux avantages suivants :

- **Sauvegarde locale de déchargement** : Le service Sauvegarde Azure offre une solution simple pour la sauvegarde de vos ressources locales dans le cloud. Obtenez une sauvegarde à court terme et à long terme sans avoir besoin de déployer des solutions de sauvegarde locale complexes. Éliminez la nécessité de sauvegarde sur bande ou hors site.
- **Sauvegarder des machines virtuelles Azure IaaS** : Le service Sauvegarde Azure fournit des sauvegardes indépendantes et isolées pour éviter une destruction accidentelle des données d’origine. Les sauvegardes sont stockées dans un coffre Recovery Services avec la gestion intégrée des points de récupération. La configuration et la scalabilité sont simples : les sauvegardes sont optimisées, et vous pouvez facilement effectuer des restaurations en fonction des besoins.
- **Mettre à l’échelle facilement** : Sauvegarde Azure utilise la puissance sous-jacente et la mise à l’échelle illimitée du cloud Azure pour offrir une haute disponibilité, sans supplément de maintenance ou de supervision. Vous pouvez configurer des alertes pour fournir des informations sur les événements, mais vous n’avez pas à vous soucier de la haute disponibilité de vos données dans le cloud.
- **Obtenir un transfert de données illimitées** : Sauvegarde Azure ne limite pas la quantité de données entrantes ou sortantes transférées. Par ailleurs, les données transférées ne sont pas facturées par Azure Backup. Toutefois, si vous utilisez le service Azure Import/Export pour importer de grandes quantités de données, les données entrantes ont un coût. Pour plus d’informations sur ce coût, consultez [Flux de travail de la sauvegarde hors connexion dans Azure Backup](backup-azure-backup-import-export.md). Les données sortantes sont les données transférées à partir d’un coffre Recovery Services pendant une opération de restauration.
- **Sécuriser les données** : le chiffrement des données garantit une transmission et un stockage sécurisés de vos données dans le cloud public. La phrase secrète de chiffrement est stockée en local et n’est jamais transmise ou stockée dans Azure. Si vous avez besoin de restaurer des données, vous êtes le seul à disposer de la phrase secrète de chiffrement ou de la clé.
- **Obtenir des sauvegardes cohérentes au niveau application** : une sauvegarde cohérente au niveau application signifie qu’un point de récupération dispose de toutes les données nécessaires pour restaurer la copie de sauvegarde. Sauvegarde Microsoft Azure fournit des sauvegardes cohérentes avec les applications, qui garantissent qu’aucun correctif supplémentaire n’est requis pour restaurer les données. La restauration de données cohérentes avec les applications réduit le délai de restauration, ce qui permet de rétablir rapidement le fonctionnement normal.
- **Obtenir la conservation à court terme et à long terme** : **Rétention à long terme** : vous pouvez utiliser les coffres de Recovery Services pour la conservation de données à court terme et à long terme. Azure ne limite pas la durée de conservation des données dans un coffre Recovery Services. Vous pouvez conserver des données dans un coffre aussi longtemps que vous le souhaitez. Azure Backup est limité à 9 999 points de récupération par instance protégée. Consultez la section [Sauvegarde et rétention](backup-introduction-to-azure-backup.md#backup-and-retention) dans cet article pour savoir comment cette limite peut avoir un impact sur vos besoins de sauvegarde.
- **Gestion automatique du stockage** : les environnements hybrides impliquent souvent un stockage hétérogène (une partie en local et une autre dans le cloud). Avec Azure Backup, l’utilisation d’appareils de stockage locaux ne génère aucun coût. Azure Backup alloue et gère automatiquement le stockage de sauvegarde sur la base d’un modèle de paiement à l’utilisation. Avec le paiement à l’utilisation, vous payez uniquement le stockage que vous utilisez. Pour plus d’informations, consultez [l’article sur les tarifs Azure](https://azure.microsoft.com/pricing/details/backup).
- **Diverses options de stockage** : la réplication du stockage est l’un des facteurs de haute disponibilité. La solution Sauvegarde Azure propose deux types de réplications : le [stockage localement redondant](../storage/common/storage-redundancy-lrs.md) et le [stockage géoredondant](../storage/common/storage-redundancy-grs.md). Choisissez l’option de stockage de sauvegarde en fonction de vos besoins :
    - Le stockage localement redondant (LRS) réplique vos données trois fois (il crée trois copies de vos données) dans une unité d’échelle de stockage dans un centre de données. Toutes les copies des données existent dans la même région. Le stockage LRS est une option à faible coût qui protège vos données contre les défaillances matérielles locales.
    - Le stockage géo-redondant (GRS) est l’option de réplication recommandée par défaut. Le stockage géo-redondant réplique vos données vers une région secondaire, distante de plusieurs centaines de kilomètres de l’emplacement principal des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il offre une durabilité des données supérieure, même en cas de panne au niveau régional.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Quelle est la différence entre Sauvegarde Azure et Azure Site Recovery ?

Les services Sauvegarde Azure et Azure Site Recovery participent tous les deux à une stratégie de continuité d’activité et de reprise d’activité (BCDR) dans votre entreprise. BCDR comprend deux grands objectifs :

- Préserver la sécurité et la capacité de récupération de vos données métier en cas de pannes.
- Maintenir vos applications et charges de travail opérationnelles pendant les temps d’arrêt planifiés et non planifiés.

Les deux services fournissent des fonctionnalités complémentaires mais différentes.

- **Azure Site Recovery** : Site Recovery fournit une solution de reprise d’activité pour les machines locales et les machines virtuelles Azure. Vous répliquez des machines d’un emplacement principal vers un emplacement secondaire. Quand un incident survient, vous basculez les machines vers l’emplacement secondaire et vous y accédez à partir de cet emplacement. Quand tout est à nouveau opérationnel, vous rebasculez les machines pour les récupérer sur le site principal.
- **Sauvegarde Azure** : Le service Sauvegarde Azure sauvegarde les données de machines locales et de machines virtuelles Azure. Les données peuvent être sauvegardées et restaurées à un niveau précis, avec notamment la sauvegarde des fichiers, des dossiers, de l’état du système de la machine et des données tenant compte des applications. Sauvegarde Azure gère les données à un niveau plus détaillé que Site Recovery. Par exemple, si une présentation sur votre ordinateur portable est endommagée, vous utilisez Sauvegarde Azure pour la restaurer. Si vous voulez préserver la sécurité et l’accessibilité de la configuration et des données d’une machine virtuelle, vous pouvez utiliser Site Recovery.  

Utilisez les points du tableau pour vous aider à déterminer vos besoins en continuité d’activité et de reprise d’activité (BCDR). 

**Objectif** | **Détails** | **Comparaison**
--- | --- | --- | --- |
**Conservation/sauvegarde de données** | Les données de sauvegarde peuvent être conservées et stockées pendant des jours, des mois, voire même des années si nécessaire du point de vue de la conformité. | Les solutions de sauvegarde comme le service Sauvegarde Azure vous permettent de choisir précisément les données que vous voulez sauvegarder et de paramétrer avec précision les stratégies de sauvegarde et de conservation.<br/><br/> Site Recovery ne permet pas la même précision dans les ajustements.
**Objectif de point de récupération (RPO)** | Quantité de perte de données acceptable si la récupération doit être exécutée. | Les sauvegardes ont un objectif de point de récupération (RPO) plus variable.<br/><br/> Les sauvegardes de machines virtuelles ont généralement un RPO d’un jour, contre seulement 15 minutes pour les sauvegardes de base de données.<br/><br/> Site Recovery fournit un RPO faible car la réplication est continue ou fréquente, de sorte que le delta entre la source et la copie de réplica est faible.
**Objectif de délai de récupération (RTO)** |Quantité de temps nécessaire pour effectuer une récupération ou une restauration complète. | Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui rallonge d’autant le RTO. Par exemple, il peut falloir plusieurs jours pour restaurer des données à partir de bandes, selon le temps nécessaire au transport de la bande depuis un site externe. | Les solutions de reprise d’activité après sinistre telles que Site Recovery ont un RPO faible car une réplication continue/fréquente signifie généralement que la cible est plus hautement synchronisée avec la source. |

## <a name="what-backup-scenarios-are-supported"></a>Quels sont les scénarios de sauvegarde pris en charge ?

La Sauvegarde Azure peut sauvegarder aussi bien les machines locales que les machines virtuelles Azure.

**Machine** | **Scénario de sauvegarde**
--- | ---
**Machines locales (physiques/virtuelles)** |  Vous pouvez sauvegarder des machines locales individuelles.<br/><br/>Vous pouvez sauvegarder des machines locales qui sont protégées par System Center Data Protection Manager (DPM).<br/><br/> Vous pouvez sauvegarder des machines locales protégées par Microsoft Azure Backup Server (MABS).
**Machines virtuelles Azure** | Vous pouvez sauvegarder des machines virtuelles Azure individuelles.<br/><br/> Vous pouvez sauvegarder des machines virtuelles Azure protégées par DPM ou MABS.

### <a name="back-up-servers"></a>Sauvegarder des serveurs

Vous pouvez être amené à sauvegarder des charges de travail et des serveurs locaux, ou des machines virtuelles Azure et leurs charges de travail, d’abord sur un serveur de sauvegarde, puis dans un coffre Recovery Services. 

**Serveur de sauvegarde** | **Détails**
--- | ---
**System Center Data Protection Manager (DPM)** | Vous pouvez utiliser le service Sauvegarde Azure pour sauvegarder des données protégées par DPM :<br/><br/> - DPM peut être en cours d’exécution localement (sur une machine physique ou virtuelle) ou dans Azure.<br/><br/> - Vous pouvez protéger différents types de données en cours d’exécution sur des machines locales et sur des machines virtuelles Azure en sauvegardant les données sur le serveur DPM.<br/><br/> Ensuite, le serveur DPM peut être sauvegardé dans un coffre Recovery Services à l’aide du service Sauvegarde Azure.<br/><br/> Le serveur DPM et les machines qu’il protège doivent se trouver dans le même réseau. Les machines locales ne peuvent être protégées que par un serveur DPM local. De même, les machines virtuelles Azure ne peuvent être protégées que par DPM exécuté dans Azure.
**Microsoft Azure Backup Server (MABS)** | Vous pouvez utiliser le service Sauvegarde Azure pour sauvegarder des données protégées par MABS :<br/><br/> - MABS peut être en cours d’exécution localement (sur une machine physique ou virtuelle) ou dans Azure.<br/><br/> - Vous protégez différents types de données en cours d’exécution sur des machines locales et sur des machines virtuelles Azure en sauvegardant les données sur MABS.<br/><br/> - Ensuite, le serveur MABS peut être sauvegardé dans un coffre Recovery Services à l’aide du service Sauvegarde Azure.<br/><br/> - MABS fournit des fonctionnalités similaires à DPM, excepté qu’il ne vous permet pas de sauvegarder sur bande. MABS ne nécessite pas de licence System Center.<br/><br/> Comme DPM, les machines locales ne peuvent être protégées que par un MABS local. Les machines virtuelles Azure ne peuvent être protégées que par un MABS dans Azure.

Les avantages de la sauvegarde préalable dans DPM/MABS puis dans un coffre sont les suivants :

- La sauvegarde dans DPM/MAB fournit des sauvegardes tenant compte des applications et optimisées pour les applications courantes comme SQL Server, Exchange et SharePoint, en plus des sauvegardes de fichiers/dossiers/volumes et des sauvegardes de l’état de la machine (sauvegarde complète de l’état du système).
- Vous n’avez pas besoin d’installer l’agent Sauvegarde Azure sur chaque machine que vous voulez sauvegarder. Chaque machine exécute l’agent de protection DPM/MABS, et l’agent Microsoft Azure Recovery Services de Sauvegarde Azure s’exécute sur le serveur DPM/MABS uniquement.
- Vous disposez de davantage de flexibilité et d’options de planification plus précises pour l’exécution de sauvegardes.
- Vous pouvez gérer les sauvegardes de plusieurs machines que vous rassemblez en groupes de protection dans une console unique. C’est particulièrement utile quand les applications sont hiérarchisées entre plusieurs machines et que vous voulez les sauvegarder ensemble.

## <a name="what-can-be-backed-up"></a>Que peut-on sauvegarder ?

**Machine** | **Serveur de sauvegarde** | **Sauvegarder**
--- | --- | ---
Machines virtuelles Windows locales | Pas de sauvegarde dans DPM ou MABS | Sauvegardez des fichiers, des dossiers, l’état du système.
Machines virtuelles Azure (Windows et Linux) | Pas de sauvegarde dans DPM ou MABS | Sauvegardez des fichiers, des dossiers, l’état du système.<br/><br/> Les sauvegardes tiennent compte des applications pour les machines Windows et elles tiennent compte des fichiers pour les machines Linux.
Machines virtuelles locales/machines virtuelles Azure | Protégé par DPM | Sauvegardez tout ce qui est protégé par DPM, notamment les fichiers/dossiers/partages/volumes et les données spécifiques aux applications. [En savoir plus](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) sur ce que DPM peut sauvegarder.
Machines virtuelles locales/machines virtuelles Azure | Protégé par MABS | Sauvegardez tout ce qui est protégé par MABS, notamment les fichiers/dossiers/partages/volumes et les données spécifiques aux applications. [Découvrez](backup-mabs-protection-matrix.md) ce que MABS peut sauvegarder.

## <a name="what-backup-agents-do-i-need"></a>De quels agents de sauvegarde ai-je besoin ?
**Scénario** | **Agent** | **Détails**
--- | --- | ---
Machines locales (sans serveur de sauvegarde) | L’agent Microsoft Azure Recovery Services (MARS) s’exécute sur la machine Windows. | Vous téléchargez et installez l’agent MARS au cours du déploiement de Sauvegarde Azure.<br/><br/> Prise en charge des machines Windows.
Machines virtuelles Azure (sans serveur de sauvegarde) | Aucun agent explicite n’est nécessaire. L’extension de machine virtuelle Azure pour la sauvegarde s’exécute sur la machine virtuelle Azure. | L’extension est installée quand vous exécutez la première sauvegarde de machine virtuelle Azure.<br/><br/> Prise en charge pour les machines Windows et Linux.
Machines locales/machines virtuelles Azure protégées par DPM. | L’agent MARS s’exécute sur le serveur DPM. | Vous n’avez pas besoin de l’agent MARS sur les machines individuelles.<br/><br/> Quand vous déployez DPM, l’agent de protection DPM est installé sur chaque machine que vous protégez. 
Sauvegarder des machines locales et des machines virtuelles Azure protégées par MABS | L’agent MARS s’exécute sur le serveur MABS. | Vous n’avez pas besoin de l’agent MARS sur les machines individuelles.<br/><br/> Quand vous déployez MABS, l’agent de protection MABS est installé sur chaque machine que vous protégez. 


## <a name="which-backup-agent-should-i-use"></a>Quel agent de sauvegarde dois-je utiliser ?

**Sauvegarde** | **Solution** | **Limite**
--- | --- | ---
Je veux sauvegarder des machines Windows locales. Les machines ne sont pas protégées par DPM ou MABS | Installez l’agent MARS sur la machine. | Vous pouvez sauvegarder des fichiers, des dossiers et l’état du système dans Azure. Les sauvegardes ne tiennent pas compte des applications.
Je veux sauvegarder des machines Linux locales. Les machines ne sont pas protégées par DPM ou MABS. | Vous devez déployer DPM ou MABS pour effectuer une sauvegarde dans Azure.
Je veux sauvegarder des applications s’exécutant sur des machines Windows locales | Pour les sauvegardes tenant compte des applications, les machines Windows doivent être protégées par DPM ou MABS.
Je veux sauvegarder des machines virtuelles Azure | Exécutez une sauvegarde avec le service Sauvegarde Azure. L’extension de sauvegarde est automatiquement configurée sur la machine virtuelle Azure Windows ou Linux. | Les disques de machine virtuelle sont sauvegardés.<br/><br/> Pour les machines virtuelles Windows, la sauvegarde est cohérente au niveau application. Pour Linux, la sauvegarde est cohérente au niveau fichier. Si vous avez besoin que les applications soient prises en compte, vous devez le configurer avec des scripts personnalisés.
Je veux sauvegarder des machines virtuelles Azure avec la flexibilité granulaire des paramètres de sauvegarde et de récupération | Protégez les machines virtuelles Azure avec DPM ou MABS s’exécutant dans Azure afin de bénéficier d’une flexibilité supplémentaire pour la planification des sauvegardes et d’une flexibilité totale pour la protection et la restauration de fichiers, de dossiers, de volumes, d’applications et de l’état du système.


## <a name="next-steps"></a>Étapes suivantes

- [Examinez](backup-architecture.md) l’architecture et les composants de différents scénarios de sauvegarde.
- [Vérifiez](backup-support-matrix.md) les fonctionnalités et les paramètres pris en charge pour la sauvegarde.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

