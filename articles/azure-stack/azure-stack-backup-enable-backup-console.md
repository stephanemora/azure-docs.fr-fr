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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: ce5fd2feaa30948042cc0570a4b0ea7f0ab7ad77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302254"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Activer la sauvegarde d’Azure Stack à partir du portail d’administration
Activez le service de sauvegarde d’infrastructure via le portail d’administration afin qu’Azure Stack puisse générer des sauvegardes. Vous pouvez utiliser ces sauvegardes pour restaurer votre environnement avec la récupération cloud en cas [d’erreur irrécupérable](.\azure-stack-backup-recover-data.md). L’objectif d’une récupération cloud est de s’assurer que les opérateurs et utilisateurs peuvent se reconnecter au portail une fois la récupération terminée. Les utilisateurs ont leurs abonnements restaurés, avec notamment les autorisations d’accès en fonction du rôle et les rôles, les plans d’origine, les offres, le calcul défini précédemment, le stockage et les quotas réseau.

Toutefois, le service Infrastructure Backup ne sauvegarde pas les machines virtuelles IaaS, les configurations réseau et les ressources de stockage comme que les comptes de stockage, les objets blob, les tables et ainsi de suite. Ainsi, lorsque les utilisateurs se connectent après la récupération du cloud, ils ne verront aucune de leurs ressources existantes. Les ressources et données Platform as a service (PaaS) ne sont également pas sauvegardées par le service. 

Les administrateurs et utilisateurs sont responsables de la sauvegarde et de la restauration des ressources IaaS et PaaS séparément du processus de sauvegarde d’infrastructure. Pour plus d’informations sur la sauvegarde des ressources IaaS et PaaS, consultez les liens suivants :

- [Machines virtuelles](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Avant d’activer la sauvegarde via la console, vous devez configurer le service de sauvegarde. Vous pouvez configurer le service de sauvegarde à l’aide de PowerShell. Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Activer la sauvegarde

1. Ouvrez le portail d’administration Azure Stack sur [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Sélectionnez **Plus de services** > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.

    ![Azure Stack - Paramètres du contrôleur de sauvegarde](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Utilisez une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour le service, vous pouvez utiliser une adresse IP. Pour garantir la disponibilité des données de sauvegarde après un sinistre, l’appareil doit se trouver dans un emplacement distinct.
    > [!Note]  
    > Si votre environnement prend en charge la résolution de noms à partir du réseau d’infrastructure Azure Stack vers votre environnement d’entreprise, vous pouvez utiliser un nom de domaine qualifié complet plutôt que l’adresse IP.
4. Saisissez le **Nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur avec un accès suffisant pour lire et écrire des fichiers. Par exemple : `Contoso\backupshareuser`.
5. Saisissez le **mot de passe** de l’utilisateur.
5. Saisissez une nouvelle fois le mot de passe pour le **confirmer**.
6. Indiquez une clé prépartagée dans la zone **Clé de chiffrement**. Les fichiers de sauvegarde sont chiffrés avec cette clé. Pensez à stocker cette clé à un emplacement sécurisé. Une fois que vous avez défini cette clé pour la première fois ou que vous procédez ultérieurement à une rotation de la clé, vous ne pouvez pas voir cette clé à partir de cette interface. Pour obtenir plus d’instructions en vue de générer une clé prépartagée, suivez les scripts de la rubrique [Activer la sauvegarde d’Azure Stack avec PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Sélectionnez **OK** pour enregistrer vos paramètres de contrôleur de sauvegarde.

Pour exécuter une sauvegarde, vous devez télécharger les outils Azure Stack, puis exécuter la cmdlet PowerShell **Start-AzSBackup** sur votre nœud administration Azure Stack. Pour plus d’informations, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à exécuter une sauvegarde. Voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Apprenez à vérifier que votre sauvegarde a été exécutée. Voir [Confirmer la sauvegarde dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md).
