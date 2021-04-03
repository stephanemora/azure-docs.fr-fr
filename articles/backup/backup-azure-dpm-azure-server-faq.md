---
title: Forum aux questions sur le serveur de sauvegarde Azure et DPM
description: Cet article fournit des réponses à des questions courantes sur la serveur Sauvegarde Microsoft Azure (MABS) et DPM (Gestionnaire de protection des données).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 1663a842b7e00c611543451d4caef96b5b5a913f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97954989"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Serveur de sauvegarde Azure et DPM - Forum aux questions

## <a name="general-questions"></a>Questions générales

Cet article répond aux questions fréquentes sur le serveur de sauvegarde Azure et DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Puis-je utiliser Azure Backup Server pour créer une sauvegarde de la récupération complète (BMR) pour un serveur physique ?

Oui.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Puis-je enregistrer mon serveur dans plusieurs coffres ?

Non. Chaque serveur DPM ou serveur Sauvegarde Azure peut être inscrit auprès d’un seul coffre.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Puis-je utiliser DPM pour sauvegarder des applications dans Azure Stack ?

Non. Vous pouvez utiliser la sauvegarde Azure pour protéger Azure Stack, mais la sauvegarde Azure ne prend pas en charge actuellement l’utilisation de DPM pour sauvegarder des applications dans Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Si j’ai installé l’agent Sauvegarde Azure pour protéger mes fichiers et dossiers, puis-je installer System Center DPM pour sauvegarder les charges de travail locales vers Azure ?

Oui. Mais vous devez configurer tout d’abord DPM et puis installer l’agent Sauvegarde Azure.  L’installation des composants dans cet ordre garantit le fonctionnement de l’agent Sauvegarde Azure avec DPM. L’installation de l’agent Sauvegarde Azure avant l’installation de DPM n’est pas conseillée et n’est pas prise en charge.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Pourquoi ne puis-je pas ajouter un serveur DPM externe après avoir installé UR7 et le dernier agent Azure Backup ?

Pour les serveurs DPM avec des sources de données protégées dans le cloud (en utilisant un correctif cumulatif antérieur au correctif cumulatif 7), vous devez attendre au moins un jour après l'installation d’UR7 et du dernier agent Azure Backup avant de démarrer **Ajouter un serveur DPM externe**. La période d’une journée est nécessaire pour charger les métadonnées des groupes de protection DPM dans Azure. Les métadonnées du groupe de protection sont chargées la première fois pendant une tâche nocturne.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>Existe-t-il des recommandations pour la configuration des exclusions pour les logiciels antivirus ?

Oui, il est recommandé de configurer les exclusions pour les antivirus. Pour les exclusions de DPM, voir [Exécution d'un logiciel antivirus sur le serveur DPM](/system-center/dpm/run-antivirus-server). Pour les exclusions de MABS, consultez [Configurer l’antivirus pour un serveur MABS](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server).

## <a name="vmware-and-hyper-v-backup"></a>Sauvegarde VMware et Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Puis-je sauvegarder des serveurs VMware vCenter dans Azure ?

Oui. Vous pouvez utiliser le serveur de sauvegarde Azure pour sauvegarder des serveurs VMware vCenter et des hôtes ESXi dans Azure.

- [en savoir plus](backup-mabs-protection-matrix.md) sur les versions prises en charge.
- [Suivez ces étapes](backup-azure-backup-server-vmware.md) pour sauvegarder un serveur VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Ai-je besoin d’une licence distincte pour récupérer un cluster VMware/Hyper-V entièrement local ?

Vous n’avez pas besoin de licence distincte pour la protection VMware/Hyper-V.

- Si vous êtes client System Center, utilisez System Center Data Protection Manager (DPM) pour protéger les machines virtuelles VMware.
- Si vous n’êtes pas client System Center, vous pouvez utiliser le serveur de sauvegarde Azure (paiement à l’utilisation) pour protéger les machines virtuelles VMware.

### <a name="can-i-restore-a-backup-of-a-hyper-v-or-vmware-vm-stored-in-azure-to-azure-as-an-azure-vm"></a>La sauvegarde d’une machine virtuelle Hyper-V ou VMware stockée dans Azure peut-elle être restaurée sur Azure en tant que machine virtuelle Azure ?

Non, ce n’est pas possible actuellement. Vous pouvez uniquement effectuer une restauration sur un hôte local.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Puis-je restaurer un élément SharePoint à l'emplacement d'origine si SharePoint est configuré à l'aide de SQL AlwaysOn (avec protection sur disque) ?

Oui, l'élément peut être restauré sur le site SharePoint d'origine.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Puis-je restaurer une base de données SharePoint à l'emplacement d'origine si SharePoint est configuré à l'aide de SQL AlwaysOn ?

Comme les bases de données SharePoint sont configurées dans SQL AlwaysOn, elles ne peuvent être modifiées que si le groupe de disponibilité est supprimé. Par conséquent, DPM ne peut pas restaurer la base de données à l'emplacement d'origine. Vous pouvez récupérer une base de données SQL Server sur une autre instance SQL Server.

## <a name="next-steps"></a>Étapes suivantes

Lire les autres forums aux questions :

- [Découvrez-en plus](backup-support-matrix-mabs-dpm.md) sur le tableau de prise en charge du serveur de sauvegarde Azure et DPM.
- [Découvrez-en plus](backup-azure-mabs-troubleshoot.md) sur la résolution des problèmes liés au serveur de sauvegarde Azure et à DPM.
