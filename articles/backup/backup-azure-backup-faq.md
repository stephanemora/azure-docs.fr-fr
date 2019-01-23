---
title: Forum aux questions sur Sauvegarde Azure
description: 'Réponses aux questions courantes sur : les fonctionnalités de la sauvegarde Azure, y compris les coffres Recovery Services ce qu’il peut sauvegarder, son fonctionnement, son chiffrement, et ses limites. '
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: a5619db18ef326b160bf9f4a1fbacbd6b3e9f012
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319458"
---
# <a name="azure-backup---frequently-asked-questions"></a>Sauvegarde Azure - Forum Aux Questions
Cet article répond aux questions courantes sur le service Sauvegarde Azure.

## <a name="recovery-services-vault"></a>Coffre Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Le nombre de coffres pouvant être créés dans chaque abonnement Azure est-il limité ?
Oui. Vous pouvez créer jusqu’à 500 coffres Recovery Services par région de Sauvegarde Microsoft Azure prise en charge et par abonnement. Au-delà, créez un autre abonnement.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Le nombre de serveurs/ordinateurs pouvant être inscrits dans chaque coffre est-il limité ?
Vous pouvez inscrire jusqu’à 1 000 machines virtuelles Azure par coffre. Si vous utilisez l’agent Sauvegarde Microsoft Azure, vous pouvez inscrire jusqu’à 50 agents MAB par coffre. Et vous pouvez inscrire 50 serveurs MAB/DPM dans un coffre.


### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Si mon organisation possède un coffre, comment isoler les données de différents serveurs lors de la restauration des données ?

Les données que vous souhaitez récupérer ensemble doivent utiliser la même phrase secrète lorsque vous configurez la sauvegarde. Si vous souhaitez isoler la récupération vers un serveur ou des serveurs spécifiques, utilisez une phrase secrète unique aux serveurs voulus. Par exemple, les serveurs des ressources humaines peuvent utiliser une phrase secrète de chiffrement, les serveurs de comptabilité peuvent en utiliser une autre et les serveurs de stockage une troisième.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Puis-je déplacer mon coffre d’un abonnement vers un autre ?

Non. Le coffre est créé au niveau de l’abonnement et ne peut pas être réaffecté à un autre abonnement.

### <a name="can-i-move-backup-data-to-another-vault"></a>Puis-je déplacer des données de sauvegarde vers un autre coffre ?

Non. Les données de sauvegarde stockées dans un coffre ne peuvent pas être déplacées vers un autre coffre.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Puis-je passer du stockage GRS au stockage LRS après une sauvegarde ?

Non. Dans un coffre Recovery Services, vous pouvez uniquement changer les options de stockage avant de commencer le stockage des sauvegardes.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Puis-je effectuer une restauration de niveau élément (ILR) pour les machines virtuelles sauvegardées dans un coffre Recovery Services ?
ILR n’est pas pris en charge.


## <a name="azure-backup-agent"></a>Agent Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Où puis-je trouver des questions courantes sur l’agent Sauvegarde Azure pour la sauvegarde de machines virtuelles Azure ?

- Pour les agents sur des machines virtuelles Azure, consultez ce [FAQ](backup-azure-vm-backup-faq.md).
- Pour les agent permettant de sauvegarde des dossiers de fichiers Azure, lisez ce [FAQ](backup-azure-file-folder-backup-faq.md).


## <a name="vmware-and-hyper-v-backup"></a>Sauvegarde VMware et Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Puis-je sauvegarder des serveurs VMware vCenter dans Azure ?

Oui. Vous pouvez utiliser le serveur de sauvegarde Azure pour sauvegarder des serveurs VMware vCenter et des hôtes ESXi dans Azure.

- [en savoir plus](backup-mabs-protection-matrix.md) sur les versions prises en charge.
- [Suivez ces étapes](backup-azure-backup-server-vmware.md) pour sauvegarder un serveur VMware.

### <a name="do-i-need-a-separate-license-to-recover-an-full-on-premises-vmwarehyper-v-cluster"></a>Ai-je besoin d’une licence distincte pour récupérer un cluster VMware/Hyper-V entièrement local ?

Vous n’avez pas besoin de licence distincte pour la protection VMware/Hyper-V.

- Si vous êtes client System Center, utilisez System Center Data Protection Manager (DPM) pour protéger les machines virtuelles VMware.
- Si vous n’êtes pas client System Center, vous pouvez utiliser le serveur de sauvegarde Azure (paiement à l’utilisation) pour protéger les machines virtuelles VMware.

## <a name="dpm-and-azure-backup-server-backup"></a>Sauvegarde DPM et serveur de sauvegarde Azure

### <a name="which-dpm-versions-are-supported"></a>Quelles sont les versions de DPM prises en charge ?

Les versions de DPM prises en charge sont résumées dans la [matrice de prise en charge](backup-azure-dpm-introduction.md#prerequisites-and-limitations). Nous vous recommandons d’installer les dernières mises à jour de DPM et d’exécuter le [version la plus récente](https://aka.ms/azurebackup_agent) de l’agent de sauvegarde Azure sur le serveur DPM.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Puis-je enregistrer mon serveur dans plusieurs coffres ?

Non. Chaque serveur DPM ou serveur Sauvegarde Azure peut être inscrit auprès d’un seul coffre.



### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Puis-je utiliser Azure Backup Server pour créer une sauvegarde de la récupération complète (BMR) pour un serveur physique ? <br/>
Oui.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Puis-je utiliser DPM pour sauvegarder des applications dans Azure Stack ?
Non. Vous pouvez utiliser la sauvegarde Azure pour protéger Azure Stack, mais la sauvegarde Azure ne prend pas en charge actuellement l’utilisation de DPM pour sauvegarder des applications dans Azure Stack.


### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Si j’ai installé l’agent Sauvegarde Azure pour protéger mes fichiers et dossiers, puis-je installer System Center DPM pour sauvegarder les charges de travail locales vers Azure ?
Oui. Mais vous devez configurer tout d’abord DPM et puis installer l’agent Sauvegarde Azure.  L’installation des composants dans cet ordre garantit le fonctionnement de l’agent Sauvegarde Azure avec DPM. L’installation de l’agent Sauvegarde Azure avant l’installation de DPM n’est pas conseillée et n’est pas prise en charge.



## <a name="general-backup"></a>Sauvegarde générale

### <a name="are-there-limits-on-backup-scheduling"></a>Existe-t-il des limites sur la planification de la sauvegarde ?
Oui.
- Vous pouvez sauvegarder des machines Windows Server ou Windows jusqu’à trois fois par jour. Vous pouvez définir la stratégie de planification pour des planifications quotidiennes ou hebdomadaires.
- Vous pouvez sauvegarder DPM jusqu’à deux fois par jour. Vous pouvez définir la stratégie de planification pour des sauvegardes quotidiennes, hebdomadaires, mensuelles et annuelles.
- Vous sauvegardez les machines virtuelles Azure une fois par jour.

## <a name="what-operating-systems-are-supported-for-backup"></a>Quels sont les systèmes d’exploitation pris en charge par la sauvegarde ?

Sauvegarde Azure prend en charge les systèmes d’exploitation suivants pour la sauvegarde des dossiers et des fichiers, ainsi que des applications protégées par Azure Backup Server et DPM.

**SE**| **Référence (SKU)** |**Détails**
--- | --- | ---
Station de travail | |
Windows 10 64 bits | Entreprise, Professionnel, Familiale | Les machines virtuelles doivent utiliser les derniers service packs et les dernières mises à jour.
Windows 8.1 64 bits | Entreprise, Professionnel | Les machines virtuelles doivent utiliser les derniers service packs et les dernières mises à jour.
Windows 8 64 bits | Entreprise, Professionnel | Les machines virtuelles doivent utiliser les derniers service packs et les dernières mises à jour.
Windows 7 64 bits | Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium, Édition Familiale Basique, Édition Starter | Les machines virtuelles doivent utiliser les derniers service packs et les dernières mises à jour.
Serveur | |
Windows Server 2016 64 bits | Standard, Datacenter, Essentials | Avec les derniers Service packs et dernières mises à jour.
Windows Server 2012 R2 64 bits | Standard, Datacenter, Foundation | Avec les derniers Service packs et dernières mises à jour.
Windows Server 2012 64 bits | Datacenter, Foundation, Standard | Avec les derniers Service packs et dernières mises à jour.
Windows Storage Server 2016 64 bits | Standard, Workgroup | Avec les derniers Service packs et dernières mises à jour.
Windows Storage Server 2012 R2 64 bits | Standard, Workgroup, Essential | Avec les derniers Service packs et dernières mises à jour.
Windows Storage Server 2012 64 bits | Standard, Workgroup | Avec les derniers Service packs et dernières mises à jour.
Windows Server 2008 R2 SP1 64 bits | Standard, Entreprise, Datacenter, Foundation | Avec les dernières mises à jour.
Windows Server 2008 64 bits | Standard, Entreprise, Datacenter | Avec les dernières mises à jour.

Pour les sauvegardes Linux de machine virtuelle Azure, la Sauvegarde Azure prend en charge [la liste de distributions approuvées par Azure](../virtual-machines/linux/endorsed-distros.md), à l’exception de CoreOS Linux et du système d’exploitation 32 bits. Les autres distributions « Bring-Your-Own-Linux » peuvent fonctionner, tant que l’agent de machine virtuelle est disponible sur la machine virtuelle et que Python est pris en charge.


## <a name="are-there-size-limits-for-data-backup"></a>Y-a-t-il une limite de taille pour la sauvegarde de données ?

Les limites de tailles sont les suivantes :


Système d’exploitation/machine | Limite de taille de la source de données
--- | --- | ---
Windows 8 ou version ultérieure | 54 400 Go
Windows 7 |1 700 Go
Windows Server 2012 ou version ultérieure | 54 400 Go
Windows Server 2008, Windows Server 2008 R2 | 1 700 Go
Microsoft Azure | 16 disques de données<br/><br/> Disque de données jusqu’à 4 095 Go

## <a name="how-is-the-data-source-size-determined"></a>Comment la taille de la source de données est-elle déterminée ?

Le tableau suivant explique comment la taille de chaque source de données est déterminée.

**Source de données** | **Détails**
--- | ---
Volume |Quantité de données sauvegardées à partir d’une machine virtuelle à volume unique.
Base de données SQL Server |Taille d’une base de données SQL unique sauvegardée.
SharePoint | Somme des bases de données de contenu et de configuration dans une batterie de serveurs SharePoint sauvegardée.
Microsoft Exchange |Somme de toutes les bases de données Exchange sur un serveur Exchange en cours de sauvegarde.
État système/récupération complète |Chaque copie individuelle de l’état système/récupération complète de l’ordinateur sauvegardée.


### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>La quantité de données sauvegardées dans un coffre Recovery Services est-elle limitée ?

Il n’existe aucune limite pour la quantité de données que vous pouvez sauvegarder dans un coffre Recovery Services.

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted"></a>Si j’annule une opération de sauvegarde en cours, les données de sauvegarde transférées sont-elles supprimées ?
Non. Toutes les données transférées dans le coffre avant l’annulation de l’opération de sauvegarde sont conservées. Azure Backup utilise un mécanisme de point de contrôle pour ajouter occasionnellement des points de contrôle aux données de sauvegarde pendant la sauvegarde. En présence de points de contrôle dans les données de sauvegarde le processus de sauvegarde suivant est en mesure de valider l’intégrité des fichiers. La prochaine tâche de sauvegarde sera incrémentielle par rapport aux données précédemment sauvegardées. Les sauvegardes incrémentielles transfèrent uniquement les données nouvelles ou modifiées, ce qui équivaut à une meilleure utilisation de la bande passante.

Si vous annulez une tâche de sauvegarde pour une machine virtuelle Azure, toutes les données transférées sont ignorées. La tâche de sauvegarde suivante transfère des données incrémentielles à partir de la dernière sauvegarde réussie.

## <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Pourquoi la taille des données transférées dans le coffre Recovery Services est-elle plus réduite que celle des données sélectionnées pour la sauvegarde ?

 Les données sauvegardées à partir de l’agent Azure Backup, de DPM et du serveur de sauvegarde Azure sont compressées et chiffrées avant d’être transférées. Une fois la compression et le chiffrement appliqués, les données dans le coffre sont inférieures de 30 à 40 %.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Puis-je supprimer des fichiers individuels à partir d’un point de récupération dans le coffre ?
Non. La Sauvegarde Azure ne prend pas en charge la suppression ou le vidage des éléments individuels à partir de sauvegardes stockées.


### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Si j’annule une opération de sauvegarde après son démarrage, les données de sauvegarde transférées sont-elles supprimées ?

Non. Toutes les données transférées dans le coffre avant l’annulation de l’opération de sauvegarde sont conservées.
- Azure Backup utilise un mécanisme de point de contrôle pour ajouter occasionnellement des points de contrôle aux données de sauvegarde pendant la sauvegarde.
- En présence de points de contrôle dans les données de sauvegarde le processus de sauvegarde suivant est en mesure de valider l’intégrité des fichiers.
- La prochaine tâche de sauvegarde sera incrémentielle par rapport aux données précédemment sauvegardées. Les sauvegardes incrémentielles transfèrent uniquement les données nouvelles ou modifiées, ce qui équivaut à une meilleure utilisation de la bande passante.




## <a name="retention-and-recovery"></a>Rétention et récupération

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Les stratégies de rétention pour les ordinateurs DPM et Windows sans DPM sont-elles identiques ?
Oui, elles ont toutes les deux des stratégies quotidienne, hebdomadaire, mensuelle et annuelle de rétention.

### <a name="can-i-customize-retention-policies"></a>Puis-je personnaliser les stratégies de rétention ?
Oui, vous pouvez personnaliser les stratégies. Par exemple, vous pouvez configurer une rétention hebdomadaire et quotidienne, mais pas annuelle ou mensuelle.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Puis-je utiliser des heures différentes pour la planification de la sauvegarde et les stratégies de rétention ?
Non. Les stratégies de rétention ne peuvent être appliquées que sur les points de sauvegarde. Par exemple, ces images montrent une stratégie de rétention pour les sauvegardes effectuées à 0h00 et 18h00.

![Planification de sauvegarde et rétention](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Si une sauvegarde est conservée pendant longtemps, faut-il plus de temps pour récupérer un point de données plus ancien ? <br/>
Non, le délai de récupération reste identique pour les points de données récents ou anciens. Chaque point de récupération se comporte comme un point complet.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Si chaque point de récupération est un point complet, cela a-t-il un impact sur la quantité totale de stockage de sauvegarde facturable ?

Les produits avec points de rétention à long terme stockent les données de sauvegarde en tant que points complets.
    - Toutefois, même si ces points *occupent* de l’espace de stockage, ils sont plus faciles et plus rapides à récupérer.
    - Les copies incrémentielles *occupent moins d’espace de stockage* , mais vous devez restaurer une chaîne de données, ce qui rallonge le temps de récupération.

L’architecture de stockage d’Azure Backup vous offre le meilleur des deux en stockant les données de manière optimale pour des restaurations rapides et des coûts de stockage faibles. Cette approche garantit que votre bande passante entrante et sortante est utilisée de façon efficace. La quantité de stockage de données et le temps nécessaire pour récupérer les données sont tous les deux réduits au minimum. En savoir plus sur [les sauvegardes incrémentielles](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Le nombre de points de récupération pouvant être créés est-il limité ?

Vous pouvez créer jusqu’à 9 999 points de récupération par instance protégée. Une instance protégée est un ordinateur, un serveur (physique ou virtuel) ou une charge de travail sauvegardé dans Azure.

- En savoir plus sur la [sauvegarde et la rétention](./backup-introduction-to-azure-backup.md#backup-and-retention).
- En savoir plus sur les [instances protégées](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance) ?

### <a name="how-many-times-can-i-recovery-data-thats-backed-up-to-azure"></a>Combien de fois puis-je récupérer des données sauvegardées dans Azure ?
Il n’existe aucune limite concernant le nombre de récupérations dans Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>En cas de restauration de données, dois-je payer pour le trafic de sortie à partir d’Azure ?
Non. La récupération est gratuite et le trafic sortant ne vous est pas facturé.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Que se passe-t-il lorsque je modifie ma stratégie de sauvegarde ?

Lorsqu’une nouvelle stratégie est appliquée, le planning et la rétention de la nouvelle stratégie sont suivis.

- Si la rétention est étendue, les points de récupération existants sont marqués comme à conserver afin qu’ils soient conformes à la nouvelle stratégie.
- - Si la rétention est réduite, ils sont marqués comme à nettoyer lors de la prochaine tâche de nettoyage et sont ensuite supprimés.

## <a name="encryption"></a>Chiffrement

### <a name="is-the-data-sent-to-azure-encrypted"></a>Les données envoyées à Azure sont-elles chiffrées ?

Oui. Les données sont chiffrées sur l’ordinateur local avec AES256. Les données sont envoyées via une connexion HTTPS sécurisée.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Les données de sauvegarde sont-elles également chiffrées dans Azure ?

Oui. Les données dans Azure sont chiffrées au repos.
- Pour la sauvegarde sur site, le chiffrement au repos est assuré à l’aide de la phrase secrète que vous fournissez lorsque vous sauvegardez sur Azure.
- Pour les machines virtuelles Azure, les données sont chiffrées au repos à l’aide de Storage Service Encryption (SSE).

Microsoft ne déchiffre les données de sauvegarde à aucun moment.


### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Quelle est la longueur minimale de la clé de chiffrement utilisée pour chiffrer les données de sauvegarde ?

La clé de chiffrement doit comporter au moins 16 caractères lorsque vous utilisez l’agent de sauvegarde Azure. Pour les machines virtuelles Azure, il n’existe aucune limite à la longueur des clés utilisées par Azure KeyVault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Que se passe-t-il si j’ai égaré la clé de chiffrement ? Puis-je récupérer les données ? Microsoft peut-il récupérer les données ?
La clé utilisée pour chiffrer les données de sauvegarde est disponible uniquement sur votre site. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

Lire les autres forums aux questions :

- [Questions courantes](backup-azure-vm-backup-faq.md) sur la sauvegarde des machines virtuelles Azure.
- [Questions courantes](backup-azure-file-folder-backup-faq.md) sur l’agent Sauvegarde Azure.
