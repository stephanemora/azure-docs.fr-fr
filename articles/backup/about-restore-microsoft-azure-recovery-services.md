---
title: Options de restauration avec l’agent MARS (Microsoft Azure Recovery Services)
description: Découvrez les options de restauration disponibles avec l’agent MARS (Microsoft Azure Recovery Services).
ms.reviewer: mepand
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: cebe13f6e72a9d8db346171384182756fb474ecd
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970465"
---
# <a name="about-restore-using-the-microsoft-azure-recovery-services-mars-agent"></a>À propos de la restauration avec l’agent MARS (Microsoft Azure Recovery Services) 

Cet article décrit les options de restauration disponibles avec l’agent MARS (Microsoft Azure Recovery Services).

## <a name="before-you-begin"></a>Avant de commencer

- Vérifiez que la dernière version de l’[agent MARS](https://aka.ms/azurebackup_agent) est installée.
- Assurez-vous que la [limitation du réseau](backup-windows-with-mars-agent.md#enable-network-throttling) est désactivée.
- Vérifiez qu’un stockage haute vitesse avec suffisamment d’espace pour le [dossier de cache de l’agent](/azure/backup/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder) est disponible.
- Supervisez la mémoire et la ressource processeur, et assurez-vous que suffisamment de ressources sont disponibles pour décompresser et déchiffrer les données.
- Lors de l’utilisation de la fonctionnalité **Restauration instantanée** pour monter un point de récupération en tant que disque, utilisez **robocopy** avec l’option de copie multithread (commutateur /MT) pour copier les fichiers efficacement à partir du point de récupération monté.

## <a name="restore-options"></a>Options de restauration

L’agent MARS offre plusieurs options de restauration. Chaque option présente des avantages uniques qui la rendent appropriée pour certains scénarios.

À l’aide de l’agent MARS, vous pouvez :

- **[Restaurer la sauvegarde de l’état du système Windows Server](backup-azure-restore-system-state.md) :** cette option permet de restaurer l’état du système sous forme de fichiers à partir d’un point de récupération dans la sauvegarde Azure, et de les appliquer au serveur Windows à l’aide de l’utilitaire de sauvegarde Windows Server.  
- **[Restaurer tous les fichiers sauvegardés dans un volume](restore-all-files-volume-mars.md) :** cette option permet de récupérer toutes les données sauvegardées dans un volume spécifié à partir du point de récupération dans la sauvegarde Azure. Cette option permet d’obtenir une vitesse de transfert plus élevée (jusqu’à 40 Mbits/s).<br>Nous vous recommandons d’utiliser cette option pour récupérer de grandes quantités de données ou des volumes entiers.
- **[Restaurer un ensemble spécifique de fichiers et dossiers sauvegardés dans un volume à l’aide de PowerShell](backup-client-automation.md#restore-data-from-azure-backup) :** si les chemins des fichiers et dossiers relatifs à la racine du volume sont connus, cette option vous permet de restaurer l’ensemble spécifié de fichiers et de dossiers à partir d’un point de récupération, en utilisant la vitesse de transfert plus élevée de la restauration complète dans un volume. Toutefois, cette option ne permet pas de parcourir les fichiers et les dossiers dans le point de récupération à l’aide de l’option Restauration instantanée.
- **[Restaurer des fichiers et des dossiers individuels avec Restauration instantanée](backup-azure-restore-windows-server.md) :** cette option permet d’accéder rapidement aux données de sauvegarde en montant un volume dans le point de récupération en tant que lecteur. Vous pouvez ensuite parcourir et copier des fichiers et des dossiers. Cette option offre une vitesse de copie allant jusqu’à 6 Mbits/s, ce qui est adapté à la récupération de fichiers et de dossiers individuels d’une taille totale inférieure à 80 Go. Une fois les fichiers requis copiés, vous pouvez démonter le point de récupération.

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître d’autres questions fréquemment posées, consultez [Questions fréquentes (FAQ) sur l’agent MARS](backup-azure-file-folder-backup-faq.yml).
- Pour plus d’informations sur les scénarios pris en charge et les limitations, consultez [Tableau de prise en charge de la sauvegarde avec l’agent MARS](backup-support-matrix-mars-agent.md).