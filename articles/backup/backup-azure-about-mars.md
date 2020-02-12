---
title: À propos de l’agent MARS
description: Découvrir comment l’agent MARS prend en charge les scénarios de sauvegarde
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 348980b840b814c09ce46627f286489d4caa9f8f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023954"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>À propos de l’agent Microsoft Azure Recovery Services (MARS)

Cet article décrit comment le service Sauvegarde Azure utilise l’agent Microsoft Azure Recovery Services (MARS) pour sauvegarder et restaurer des fichiers, des dossiers et le volume ou l’état du système, depuis un ordinateur local sur Azure.

L’agent MARS prend en charge les scénarios de sauvegarde suivants :

![Scénarios de sauvegarde MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fichiers et dossiers** : protection sélective des fichiers et dossiers Windows.
- **Au niveau du volume** : protection de l’ensemble d’un volume Windows de votre machine.
- **Au niveau du système** : protection de l’ensemble de l’état du système Windows.

L’agent MARS prend en charge les scénarios de restauration suivants :

![Scénarios de récupération MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Même serveur** : le serveur sur lequel la sauvegarde a été créée à l’origine.
    -    **Fichiers et dossiers** : choisissez les fichiers et dossiers individuels que vous souhaitez restaurer.
    -    **Au niveau du volume** : choisissez le volume et le point de récupération que vous souhaitez restaurer, puis procédez à la restauration au même emplacement ou à un autre emplacement sur la même machine.  Créez une copie des fichiers existants, remplacez les fichiers existants ou ignorez la récupération des fichiers existants.
    -    **Au niveau du système** : choisissez l’état du système et le point de récupération à restaurer sur la même machine à un emplacement spécifié.


-   **Serveur alternatif** : serveur autre que celui sur lequel la sauvegarde a été effectuée.
    -    **Fichiers et dossiers** : choisissez les fichiers et dossiers individuels dont vous souhaitez restaurer le point de récupération sur une machine cible.
    -    **Au niveau du volume** : choisissez le volume et le point de récupération que vous souhaitez restaurer à un autre emplacement. Créez une copie des fichiers existants, remplacez les fichiers existants ou ignorez la récupération des fichiers existants.
    -    **Au niveau du système** : choisissez l’état du système et le point de récupération à restaurer en tant que fichier d’état système sur une autre machine.

## <a name="backup-process"></a>Processus de sauvegarde

1. Dans le portail Azure, créez un [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) et choisissez des fichiers, des dossiers et l’état du système à partir des objectifs de sauvegarde.
2. [Téléchargez les informations d’identification du coffre Recovery Services et le programme d’installation de l’agent](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) sur une machine locale. 

    Pour protéger la machine locale en sélectionnant l’option de sauvegarde, choisissez les fichiers, les dossiers et l’état du système, puis téléchargez l’agent MARS.

3. Préparer l’infrastructure :

    a. Exécutez le programme d’installation pour [installer l’agent](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent).

    b. Utilisez vos informations d’identification du coffre qui ont été téléchargées pour inscrire la machine dans le coffre Recovery Services.
4. À partir de la console de l’agent sur le client, [configurez la sauvegarde](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy). Spécifiez la stratégie de rétention de vos données de sauvegarde pour commencer la protection.

![Diagramme de l’agent Sauvegarde Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Autres cas de figure
-   **Sauvegarder des fichiers et des dossiers spécifiques au sein de machines virtuelles Azure** : la méthode principale pour sauvegarder des machines virtuelles Azure consiste à utiliser une extension Sauvegarde Azure sur la machine virtuelle. L’extension sauvegarde la totalité de la machine virtuelle. Si vous souhaitez sauvegarder des fichiers et des dossiers spécifiques au sein d’une machine virtuelle, vous pouvez installer l’agent MARS sur les machines virtuelles Azure. Pour plus d’informations, consultez [Architecture : Sauvegarde de machine virtuelle Azure prédéfinie](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Amorçage hors connexion** : les sauvegardes complètes initiales des données dans Azure transfèrent généralement de grandes quantités de données et nécessitent plus de bande passante réseau. Les sauvegardes suivantes transfèrent uniquement le volume de données delta (ou incrémentiel). La sauvegarde Azure compresse les sauvegardes initiales. Par l’intermédiaire du processus d’*amorçage hors connexion*, la sauvegarde Azure peut utiliser des disques pour charger les données de sauvegarde initiale compressées hors connexion dans Azure. Pour plus d’informations, consultez [Sauvegarde Azure hors connexion avec Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Étapes suivantes
[Matrice de prise en charge de l’agent MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Forum aux questions sur l’agent MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
