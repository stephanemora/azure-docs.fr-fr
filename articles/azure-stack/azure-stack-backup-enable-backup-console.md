---
title: Activer la sauvegarde d’Azure Stack à partir du portail d’administration | Microsoft Docs
description: Activez le service de sauvegarde d’infrastructure via le portail d’administration pour permettre la restauration d’Azure Stack en cas de panne.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: f5610f03cb613c7da2f89da1a38b6bf058a4e0cc
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714404"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Activer la sauvegarde d’Azure Stack à partir du portail d’administration
Activez le service de sauvegarde d’infrastructure via le portail d’administration afin qu’Azure Stack puisse générer des sauvegardes. Vous pouvez utiliser ces sauvegardes pour restaurer votre environnement avec la récupération cloud en cas [d’erreur irrécupérable](./azure-stack-backup-recover-data.md). L’objectif d’une récupération cloud est de s’assurer que les opérateurs et utilisateurs peuvent se reconnecter au portail une fois la récupération terminée. Les utilisateurs ont leurs abonnements restaurés, avec notamment les autorisations d’accès en fonction du rôle et les rôles, les plans d’origine, les offres, le calcul défini précédemment, le stockage et les quotas réseau.

Toutefois, le service Infrastructure Backup ne sauvegarde pas les machines virtuelles IaaS, les configurations réseau et les ressources de stockage comme que les comptes de stockage, les objets blob, les tables et ainsi de suite. Ainsi, lorsque les utilisateurs se connectent après la récupération du cloud, ils ne verront aucune de leurs ressources existantes. Les ressources et données Platform as a service (PaaS) ne sont également pas sauvegardées par le service. 

Les administrateurs et utilisateurs sont responsables de la sauvegarde et de la restauration des ressources IaaS et PaaS séparément du processus de sauvegarde d’infrastructure. Pour plus d’informations sur la sauvegarde des ressources IaaS et PaaS, consultez les liens suivants :

- [Machines virtuelles](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Activer ou reconfigurer une sauvegarde

1. Ouvrez le [portail d’administration Azure Stack](azure-stack-manage-portals.md).
2. Sélectionnez **Tous les services** puis, sous la catégorie **ADMINISTRATION**, sélectionnez **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Utilisez une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour le service, vous pouvez utiliser une adresse IP. Pour garantir la disponibilité des données de sauvegarde après un sinistre, l’appareil doit se trouver dans un emplacement distinct.

    > [!Note]  
    > Si votre environnement prend en charge la résolution de noms à partir du réseau d’infrastructure Azure Stack vers votre environnement d’entreprise, vous pouvez utiliser un nom de domaine qualifié complet plutôt que l’adresse IP.
    
4. Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`.
5. Saisissez le **mot de passe** de l’utilisateur.
6. Saisissez une nouvelle fois le mot de passe pour le **confirmer**.
7. La **fréquence en heures** détermine la fréquence à laquelle les sauvegardes sont créées. La valeur par défaut est 12. Scheduler prend en charge un maximum de 12 heures et un minimum de 4 heures. 
8. La **période de rétention en jours** détermine le nombre de jours pendant lesquels les sauvegardes sont conservées sur l’emplacement externe. La valeur par défaut est 7. Scheduler prend en charge un maximum de 14 jours et un minimum de 2 jours. Les sauvegardes antérieures à la période de rétention sont automatiquement supprimées de l’emplacement externe.

    > [!Note]  
    > Si vous souhaitez archiver les sauvegardes antérieures à la période de rétention, veillez à sauvegarder les fichiers avant que le planificateur supprime les sauvegardes. Si vous réduisez la période de rétention de sauvegarde (par exemple, de 7 à 5 jours), le planificateur supprime toutes les sauvegardes antérieures à la nouvelle période de rétention. Confirmez que vous acceptez la suppression des sauvegardes avant de mettre à jour cette valeur. 

9. Indiquez une clé prépartagée dans la zone **Clé de chiffrement**. Les fichiers de sauvegarde sont chiffrés avec cette clé. Pensez à stocker cette clé à un emplacement sécurisé. Une fois que vous avez défini cette clé pour la première fois ou que vous procédez ultérieurement à une rotation de la clé, vous ne pouvez pas voir la clé à partir de cette interface. Pour créer la clé, exécutez les commandes Azure Stack PowerShell suivantes :
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Sélectionnez **OK** pour enregistrer vos paramètres de contrôleur de sauvegarde.

    ![Azure Stack - Paramètres du contrôleur de sauvegarde](media/azure-stack-backup/backup-controller-settings.png)

## <a name="start-backup"></a>Démarrer la sauvegarde
Pour démarrer une sauvegarde, cliquez sur **Sauvegarder maintenant** afin de démarrer une sauvegarde à la demande. Une sauvegarde à la demande ne modifiera pas l’heure de la prochaine sauvegarde planifiée. Une fois la tâche terminée, vous pouvez confirmer les paramètres dans **Éléments principaux** :

![Azure Stack - sauvegarde à la demande](media/azure-stack-backup/scheduled-backup.png)

Vous pouvez également exécuter l’applet de commande PowerShell **Start-AzsBackup** sur votre ordinateur d’administration Azure Stack. Pour plus d’informations, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Activer ou désactiver les sauvegardes automatiques
Les sauvegardes sont automatiquement planifiées lorsque vous activez la sauvegarde. Vous pouvez vérifier la prochaine sauvegarde planifiée dans **Éléments principaux**. 

![Azure Stack - sauvegarde à la demande](media/azure-stack-backup/on-demand-backup.png)

Si vous devez désactiver les futures sauvegardes planifiées, cliquez sur **Désactiver les sauvegardes automatiques**. La désactivation des sauvegardes automatiques conservera les paramètres de sauvegarde configurés et la planification de sauvegarde. Cette action indique simplement au planificateur d’ignorer les futures sauvegardes. 

![Azure Stack - Désactiver les sauvegardes planifiées](media/azure-stack-backup/disable-auto-backup.png)

Vérifiez que les futures sauvegardes planifiées ont été désactivées dans **Éléments principaux**:

![Azure Stack - confirmer que les sauvegardes ont été désactivées](media/azure-stack-backup/confirm-disable.png)

Cliquez sur **Activer les sauvegardes automatiques** pour informer le planificateur de démarrer les sauvegardes ultérieures à l’heure planifiée. 

![Azure Stack - Activer les sauvegardes planifiées](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Si vous avez configuré la sauvegarde de l’infrastructure avant la mise à jour vers 1807, les sauvegardes automatiques seront désactivées. Ainsi, les sauvegardes démarrées par Azure Stack n’entrent pas en conflit avec les sauvegardes démarrées par un moteur de planification de tâche externe. Après avoir désactivé un planificateur de tâche externe, cliquez sur **Activer les sauvegardes automatiques**.


## <a name="next-steps"></a>Étapes suivantes

- Apprenez à exécuter une sauvegarde. Voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Apprenez à vérifier que votre sauvegarde a été exécutée. Voir [Confirmer la sauvegarde dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md).
