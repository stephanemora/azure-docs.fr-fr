---
title: Résoudre les problèmes de sauvegarde de l’état du système avec sauvegarde Azure
description: Résoudre les problèmes de sauvegarde de l’état système.
services: backup
author: srinathvasireddy
manager: sivan
keywords: Comment sauvegarder ; état du système de sauvegarde
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathvasireddy
ms.openlocfilehash: 53b9f8fb58a6e70a4bd2cd02adb9ce824466d7de
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481593"
---
# <a name="troubleshoot-system-state-backup"></a>Résoudre les problèmes de sauvegarde de l’état du système

Cet article décrit les solutions aux problèmes que vous pouvez rencontrer lors de l’utilisation de sauvegarde de l’état système.

## <a name="pre-requisite"></a>Conditions préalables

Avant de nous résoudre les problèmes de sauvegarde de l’état système avec sauvegarde Azure, assurez-vous de vous effectuer la ci-dessous les conditions préalables vérifier.  

### <a name="verify-windows-server-backup-is-installed"></a>Vérifier la que sauvegarde Windows Server est installée

Vérifiez que sauvegarde Windows Server est installée et activée sur le serveur. Pour vérifier l’état d’installation, exécutez la commande PowerShell ci-dessous :

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Si la sortie affiche le **état d’installation** comme **disponible**, cela signifie que la fonctionnalité de sauvegarde de Windows Server est disponible pour l’installation, mais n’est pas installée sur le serveur. Toutefois si la sauvegarde de Windows Server n’est pas installée, puis utilisez une des méthodes ci-dessous pour l’installer.

**Méthode 1 : Installer la sauvegarde de Windows Server à l’aide de PowerShell**

Pour installer la sauvegarde de Windows Server à l’aide de PowerShell, exécutez la commande ci-dessous :

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Méthode 2 : Installer à l’aide du Gestionnaire de serveur de sauvegarde de Windows Server**

Pour installer la sauvegarde de Windows Server à l’aide du Gestionnaire de serveur, effectuez la ci-dessous :

1. Dans le **Manager Server** et cliquez sur **ajouter des rôles et fonctionnalités**. Le **Assistant Ajout de rôles et fonctionnalités** s’affiche.

    ![tableau de bord](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Sélectionnez **Type d’Installation** et cliquez sur **suivant**.

    ![Type d’installation](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Sélectionnez un serveur du pool de serveurs, cliquez sur **suivant**. Dans le rôle de serveur, conservez la sélection par défaut et cliquez sur **suivant**.
4. Sélectionnez **sauvegarde Windows Server** dans **fonctionnalités** onglet et cliquez sur **suivant**.

    ![features](./media/backup-azure-system-state-troubleshoot/features.png)

5. Dans le **Confirmation** , cliquez sur **installer** pour démarrer le processus d’installation.
6. Dans le **résultats** onglet, il affichera la sauvegarde Windows Server composant est correctement installé sur votre serveur Windows.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Autorisation des informations de Volume système

Assurez-vous que le système Local dispose d’un contrôle total sur **System Volume Information** dossier se trouve dans le volume où windows est installé. Il s’agit généralement **C:\System Volume Information**. Sauvegarde de Windows Server peut échouer si les autorisations ci-dessus ne sont pas définies correctement

### <a name="dependent-services"></a>Services dépendants

Vérifiez les services ci-dessous sont en cours d’exécution :

**Nom du service** | **Type de démarrage**
--- | ---
Appel de procédure distante | Automatique
Événements de COM + System(EventSystem) | Automatique
Service(SENS) de Notification d’événement système | Automatique
Copy(VSS) de clichés instantanés de volume | Manuel
Provider(SWPRV) de copie de clichés instantanés logiciels Microsoft | Manuel

### <a name="validate-windows-server-backup-status"></a>Valider l’état de la sauvegarde de Windows Server

Pour valider l’état de la sauvegarde de Windows Server, effectuer le ci-dessous :

  * Vérifiez que WSB PowerShell s’exécute.

    -   Exécutez `Get-WBJob` PowerShell avec élévation de privilèges et vérifiez que, elle ne retourne pas l’erreur suivante :

    > [!WARNING]
    > Get-WBJob : Le terme 'Get-WBJob' n’est pas reconnu comme le nom de l’applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez l’orthographe du nom ou, si un chemin d’accès a été inclus, vérifiez que le chemin d’accès est correct et réessayez.

    -   Si elle échoue avec cette erreur puis réinstallez la fonctionnalité sauvegarde Windows Server sur l’ordinateur serveur, comme indiqué dans les conditions préalables de l’étape 1.

  * Garantir la sauvegarde de la sauvegarde Windows Server fonctionne correctement, en exécutant la commande à partir de l’invite de commandes avec élévation de privilèges ci-dessous :

      ` wbadmin start systemstatebackup -backuptarget:X: -quiet `

      > [!NOTE]
      >Remplacez les X par la lettre de lecteur du volume où vous voulez stocker l’état du système sauvegarder d’image.

    - Vérifier régulièrement l’état du travail en exécutant `Get-WBJob` commande à partir de PowerShell avec élévation de privilèges        
    - Une fois le travail de sauvegarde se termine vérifier l’état final du travail en exécutant `Get-WBJob -Previous 1` commande

Si le travail échoue, il indique un problème de sauvegarde Windows Server, ce qui entraînerait l’agent MARS échec des sauvegardes d’état du système.

## <a name="common-errors"></a>Erreurs courantes

### <a name="vss-writer-timeout-error"></a>Erreur de délai d’expiration de l’enregistreur VSS

| Symptôme | Cause : | Résolution :
| -- | -- | --
| -L’agent MARS échoue avec le message d’erreur : « Échec de la tâche de sauvegarde Windows Server avec des erreurs VSS. Vérifiez les journaux des événements VSS pour résoudre l’échec »<br/><br/> -Erreur suivante journal est présent dans les journaux des événements Application VSS : « Un enregistreur VSS a rejeté un événement avec l’erreur 0x800423f2, délai d’expiration de l’enregistreur a expiré entre les événements de blocage et déblocage ».| Enregistreur VSS est impossible d’effectuer dans le temps en raison d’un manque de ressources processeur et mémoire sur l’ordinateur <br/><br/> Un autre logiciel de sauvegarde est déjà à l’aide de l’enregistreur VSS, par conséquent l’opération d’instantané n'a pas pu terminer pour cette sauvegarde | Attente de processeur/mémoire libérés sur le système ou abandonner le processus prend trop de ressources mémoire/processeur et recommencez l’opération <br/><br/>  Attendez que la sauvegarde en cours se termine, recommencez l’opération ultérieurement lorsqu’aucune sauvegarde n’est en cours d’exécution sur l’ordinateur


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espace disque insuffisant pour augmenter les clichés instantanés

| Symptôme | Résolution :
| -- | --
| -L’agent MARS échoue avec le message d’erreur : Sauvegarde a échoué car le volume de clichés instantanés ne peut pas augmenter en raison d’un espace disque insuffisant sur les volumes contenant les fichiers système <br/><br/> -Journal d’erreur / d’avertissement à la suite est présente dans les journaux des événements système volsnap : « Il était un espace disque insuffisant sur le volume C: pour augmenter le stockage des clichés instantanés pour les clichés instantanés du lecteur C: en raison de cet échec tous les clichés instantanés de volume C: sont exposés en cours de suppression » | -Libérer de l’espace dans le volume en surbrillance dans le journal des événements afin que l’espace est suffisant pour les clichés instantanés de croître pendant la sauvegarde en cours <br/><br/> -Lorsque vous configurez l’espace de clichés instantanés nous pouvons limiter la quantité d’espace utilisé pour le cliché instantané, pour plus d’informations voir [article](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax)


### <a name="efi-partition-locked"></a>Partition EFI verrouillée

| Symptôme | Résolution :
| -- | --
| Échec de l’agent MARS avec le message d’erreur : « État du système précédent a échoué car la partition système EFI est verrouillée. Cela peut être en raison d’accès de la partition par un tiers de sécurité ou un logiciel de sauvegarde » | -Si le problème est dû à un logiciel de sécurité tiers, vous devez contacter le fournisseur d’antivirus afin qu’il autorise l’agent MARS <br/><br/> -Si un logiciel de sauvegarde tiers est en cours d’exécution, puis attendez qu’elle se termine, puis réessayez arrière des


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’état du système Windows dans un déploiement Resource Manager, consultez [sauvegarde d’état du système Windows Server](backup-azure-system-state.md)
