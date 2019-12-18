---
title: À propos de l’agent MARS
description: Découvrir comment l’agent MARS prend en charge les scénarios de sauvegarde
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896875"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>À propos de l’agent Microsoft Azure Recovery Services (MARS)

Cet article décrit comment le service Sauvegarde Azure utilise l’agent Microsoft Azure Recovery Services (MARS) pour sauvegarder et restaurer des fichiers ou des dossiers, un volume ou un état du système à partir d’un ordinateur local sur Azure.

L’agent MARS prend en charge les scénarios de sauvegarde suivants :

![coffre recovery services tableau de bord](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fichiers et dossiers** : protection sélective des fichiers et dossiers Windows.
- **Au niveau du volume** : protection de l’ensemble d’un volume Windows de votre machine.
- **Au niveau du système** : protection de l’ensemble de l’état du système Windows.

L’agent MARS prend en charge les scénarios de restauration suivants :

![coffre recovery services tableau de bord](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Même serveur** : même serveur sur lequel la sauvegarde a été créée à l’origine.
    -    **Fichiers et dossiers** : vous pouvez parcourir et sélectionner les fichiers et dossiers individuels que vous souhaitez restaurer.
    -    **Au niveau du volume** : vous pouvez choisir le volume et le point de récupération que vous souhaitez restaurer, puis le restaurer au même emplacement ou à un autre emplacement sur le même ordinateur.  Vous pouvez créer une copie des fichiers existants, remplacer les fichiers existants ou ignorer la récupération des fichiers existants.
    -    **Au niveau du système** : vous pouvez choisir l’état du système et le point de récupération à restaurer sur le même ordinateur à un emplacement spécifié.


-   **Serveur alternatif** : autre serveur, c’est-à-dire serveur différent de celui où la sauvegarde a été effectuée.
    -    **Fichiers et dossiers** : vous pouvez parcourir et sélectionner les fichiers et dossiers individuels que vous souhaitez restaurer et le point de récupération sur un ordinateur cible.
    -    **Au niveau du volume** : vous pouvez choisir le volume et le point de récupération que vous souhaitez restaurer à un autre emplacement en créant une copie des fichiers existants, en remplaçant les fichiers existants ou en ignorant la récupération des fichiers existants.
    -    **Au niveau du système** : vous pouvez choisir l’état du système et le point de récupération à restaurer en tant que fichier d’état système sur un autre ordinateur.

## <a name="backup-process"></a>Processus de sauvegarde

1.  À partir du Portail Azure, créez un [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) et choisissez des fichiers et des dossiers et/ou l’état du système à partir des objectifs de sauvegarde.
2.  [Téléchargez](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) les informations d’identification du coffre Recovery Services et le programme d’installation de l’agent sur un ordinateur local. Pour protéger l’ordinateur local en choisissant l’option de sauvegarde, choisissez les fichiers et dossiers et l’état du système, puis téléchargez l’agent MARS.
3.  Préparer l’infrastructure :

    a.    Exécutez le programme d’installation pour [installer](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) l’agent.

    b.  Utilisez les informations d’identification du coffre qui ont été téléchargées pour inscrire l’ordinateur dans le coffre Recovery Services.
4.  À partir de la console de l’agent sur le client, utilisez [planifier la sauvegarde](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) pour configurer la sauvegarde. Spécifiez la stratégie de rétention de vos données de sauvegarde et commencez la protection.

![coffre recovery services tableau de bord](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Autres cas de figure
-   **Sauvegarder des fichiers et des dossiers spécifiques au sein d’une machine virtuelle Azure** : la méthode principale pour sauvegarder des machines virtuelles Azure consiste à utiliser une extension Sauvegarde Azure sur la machine virtuelle. La totalité de la machine virtuelle est ainsi sauvegardée. Si vous souhaitez sauvegarder des fichiers et des dossiers spécifiques au sein d’une machine virtuelle, vous pouvez installer l’agent MARS sur des machines virtuelles Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)

-   **Amorçage hors connexion** : les sauvegardes complètes initiales des données vers Azure transfèrent généralement de grandes quantités de données et requièrent davantage de bande passante par rapport aux sauvegardes suivantes qui transfèrent uniquement les données deltas/incrémentielles. La sauvegarde Azure compresse les sauvegardes initiales. Via le processus d’amorçage hors connexion, la sauvegarde Azure peut utiliser des disques pour charger les données de sauvegarde initiale compressées hors connexion dans Azure. [Plus d’informations](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)


## <a name="next-steps"></a>Étapes suivantes
[Matrice de prise en charge de l’agent MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[FAQ : L’agent MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
