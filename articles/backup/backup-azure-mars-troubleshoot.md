---
title: Résoudre les problèmes de l’agent de sauvegarde Azure
description: Résoudre les problèmes liés à l’installation et l’inscription de l’agent Sauvegarde Azure
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: e7a63167285c06fdfe632e7d45d9fddd3cca7842
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248520"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>Résoudre les problèmes liés à Microsoft Azure Recovery Services Agent (MARS)
## <a name="recommended-steps"></a>Étapes recommandées
Consultez cet article pour résoudre les erreurs de configuration, d’inscription, de sauvegarde et de restauration à l’aide de l’agent MARS.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Informations d’identification du coffre fournies non valides. Cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération.
| Détails de l’erreur | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |
| **Error** </br> *Informations d’identification du coffre fournies non valides. Cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération. (ID : 34513)* | <ul><li> Les informations d’identification du coffre sont invalides (c’est-à-dire qu’elles ont été téléchargées plus de 48 heures avant l’heure de l’inscription).<li>L’agent MARS ne peut pas télécharger de fichiers dans le répertoire Temp de Windows. <li>Les informations d’identification du coffre se trouvent sur un emplacement réseau. <li>TLS 1.0 est désactivé<li> Un serveur proxy configuré bloque la connexion. <br> |  <ul><li>Téléchargez les nouvelles informations d’identification du coffre.<li>Accédez à **Options Internet** > **Sécurité** > **Internet**. Ensuite, sélectionnez **Personnaliser le niveau** et faites défiler jusqu’à la section téléchargement de fichiers. Ensuite, sélectionnez **Activer**.<li>Vous devrez peut-être aussi ajouter le site à vos [sites de confiance](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modifiez les paramètres pour utiliser un serveur proxy. Fournissez ensuite les détails du serveur proxy. <li> Faites correspondre la date et l’heure avec celles de votre ordinateur.<li>Accédez à C:/Windows/Temp et vérifiez s’il y a plus de 60 000 ou 65 000 fichiers comportant l’extension .tmp. Si c’est le cas, supprimez ces fichiers.<li>Vous pouvez le vérifier en exécutant le package SDP sur le serveur. Si vous obtenez une erreur indiquant que les téléchargements de fichiers ne sont pas autorisés, il est probable qu’il y ait un nombre élevé de fichiers dans le répertoire C:/Windows/Temp.<li>Assurez-vous que .NET Framework 4.6.2 est installé. <li>Si vous avez désactivé TLS 1.0 en raison de la conformité avec PCI, consultez cette [Page de dépannage](https://support.microsoft.com/help/4022913). <li>Si vous avez des logiciels ou programmes antivirus installés sur le serveur, excluez les fichiers suivants de l’analyse antivirus : <ul><li>CBengine.exe<li>CSC.exe, qui est lié à .NET Framework. Il existe un CSC.exe pour chaque version .NET installée sur le serveur. Excluez les fichiers CSC.exe qui sont liés à toutes les versions de .NET Framework sur le serveur concerné. <li>Emplacement du dossier temporaire ou du cache. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L’agent Microsoft Azure Recovery Service n’a pas pu se connecter à la Sauvegarde Microsoft Azure

| Détails de l’erreur | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |
| **Error** </br><ol><li>*L’agent Microsoft Azure Recovery Service n’a pas pu se connecter à la Sauvegarde Microsoft Azure. (ID : 100050) Vérifiez vos paramètres réseau et assurez-vous que vous pouvez vous connecter à Internet*<li>*(407) Authentification proxy requise* |Le proxy bloque la connexion. |  <ul><li>Accédez à **Options Internet** > **Sécurité** > **Internet**. Sélectionnez ensuite **Personnaliser le niveau** et faites défiler jusqu’à la section téléchargement de fichiers. Sélectionnez **Activer**.<li>Vous devrez peut-être aussi ajouter le site à vos [sites de confiance](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Modifiez les paramètres pour utiliser un serveur proxy. Fournissez ensuite les détails du serveur proxy. <li>Si vous avez des logiciels ou programmes antivirus installés sur le serveur, excluez les fichiers suivants de l’analyse antivirus. <ul><li>CBEngine.exe (au lieu de dpmra.exe).<li>CSC.exe (lié à .NET Framework). Il existe un CSC.exe pour chaque version .NET installée sur le serveur. Excluez les fichiers CSC.exe qui sont liés à toutes les versions de .NET Framework sur le serveur concerné. <li>Emplacement du dossier temporaire ou du cache. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Échec de définition de la clé de chiffrement pour les sauvegardes sécurisées

| Détails de l’erreur | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |      
| **Error** </br>*Impossible de définir la clé de chiffrement pour les sauvegardes sécurisées. L’activation n’a pas entièrement réussi, mais la phrase secrète de chiffrement a été enregistrée dans le fichier suivant*. |<li>Le serveur est déjà inscrit auprès d’un autre coffre.<li>Lors de la configuration, la phrase secrète a été endommagée.| Désinscrivez le serveur du coffre et réinscrivez-le avec une nouvelle phrase secrète.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>L’activation n’a pas réussi. Échec de l’opération en cours en raison d’une erreur de service interne [0x1FC07]

| Détails de l’erreur | Causes possibles | Actions recommandées |
|---------|---------|---------|
|**Error** </br><ol>*L’activation n’a pas réussi. Échec de l’opération en cours en raison d’une erreur de service interne [0x1FC07]. Réessayez l’opération après un certain temps. Si le problème persiste, contactez le support technique Microsoft*     | <li> Le dossier temporaire se situe sur un volume dont l’espace est insuffisant. <li> Le dossier temporaire a été incorrectement déplacé vers un autre emplacement. <li> Le fichier OnlineBackup.KEK est manquant.         | <li>Effectuez une mise à niveau vers la [dernière version](http://aka.ms/azurebackup_agent) de l’Agent MARS.<li>Déplacez le dossier temporaire ou l’emplacement du cache vers un volume avec un espace disponible équivalent à 5-10 % de la taille totale des données de sauvegarde. Pour déplacer correctement l’emplacement du cache, reportez-vous aux étapes contenues dans [Questions sur l’agent de Sauvegarde Microsoft Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Assurez-vous que le fichier OnlineBackup.KEK est présent. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Erreur 34506. La phrase secrète de chiffrement stockée sur cet ordinateur n’est pas configurée correctement.

| Détails de l’erreur | Causes possibles | Actions recommandées |
|---------|---------|---------|
|**Error** </br><ol>*Erreur 34506. La phrase secrète de chiffrement stockée sur cet ordinateur n’est pas correctement configurée*.    | <li> Le dossier temporaire se situe sur un volume dont l’espace est insuffisant. <li> Le dossier temporaire a été incorrectement déplacé vers un autre emplacement. <li> Le fichier OnlineBackup.KEK est manquant.        | <li>Effectuez une mise à niveau vers la [dernière version](http://aka.ms/azurebackup_agent) de l’Agent MARS.<li>Déplacez le dossier temporaire ou l’emplacement du cache vers un volume avec un espace disponible équivalent à 5-10 % de la taille totale des données de sauvegarde. Pour déplacer correctement l’emplacement du cache, reportez-vous aux étapes contenues dans [Questions sur l’agent de Sauvegarde Microsoft Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Assurez-vous que le fichier OnlineBackup.KEK est présent. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>Les sauvegardes ne s’exécutent pas selon ce qui a été planifié
Procédez comme suit si vos sauvegardes planifiées ne se déclenchent pas automatiquement, alors que vous n’avez aucun problème à effectuer des sauvegardes manuelles. 
 
<li>Vérifiez que PowerShell 3.0 ou version ultérieure est installé sur le serveur. Pour vérifier la version de PowerShell, exécutez la commande suivante et vérifiez que le numéro de version *majeure* est supérieur ou égal à 3.

`$PSVersionTable.PSVersion`
<li>Vérifiez que le chemin d’accès suivant fait partie de la variable d'environnement *PSMODULEPATH*.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>Si la stratégie d’exécution de Powershell pour *LocalMachine* est « restreinte », l’applet de commande Powershell qui déclenche la tâche de sauvegarde peut échouer. Exécutez les commandes suivantes en mode élévation de privilèges pour vérifier et définir la stratégie d’exécution sur *Unrestricted* ou *RemoteSigned*

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>Accédez à Panneau de configuration -> Outils d’administration -> Planificateur de tâches -> développez Microsoft -> sélectionnez Sauvegarde en ligne
<li>Double-cliquez sur la tâche « Microsoft-OnlineBackup » et accédez à l’onglet « Déclencheurs ».
<li>Assurez-vous que l’état de la tâche est définie sur « Activé ». Si ce n’est pas le cas, cliquez sur « Modifier » et cochez la case « Activé »
<li>Accédez à la section *Options de sécurité* de l’onglet *Général*
<li>Assurez-vous que le compte d’utilisateur sélectionné pour l’exécution de la tâche est soit *SYSTEM* soit le groupe des administrateurs locaux sur le serveur > [!TIP]. Il est recommandé de redémarrer le serveur après avoir effectué les étapes ci-dessus pour vous assurer que les modifications apportées sont appliquées de manière cohérente


## <a name="troubleshooting-restore-issues"></a>Résolution des problèmes de restauration

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>Échec du montage du volume de récupération pendant la récupération de fichiers et dossiers
Si Azure Backup ne monte pas efficacement le volume de montage, même lorsque vous cliquez plusieurs fois sur **Monter**, ou ne parvient pas à le monter et génère une ou plusieurs erreurs, suivez la procédure ci-dessous pour entamer une récupération normale.

1.  Si le processus de montage s’exécute depuis plusieurs minutes, annulez-le.

2.  Vérifiez que vous disposez de la dernière version de l’agent de sauvegarde Azure. Pour en savoir plus sur la version de l’agent de sauvegarde Azure, cliquez sur la zone **À propos de l’agent Microsoft Azure Recovery Services** du volet **Actions** de la console de sauvegarde Microsoft Azure, et assurez-vous que le numéro de **version** est égal ou supérieur à celui qu’indique [cet article](https://go.microsoft.com/fwlink/?linkid=229525). Vous pouvez télécharger la dernière version [ici](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Cliquez sur **Gestionnaire de périphériques** -> **Contrôleurs de stockage** et vérifiez que vous pouvez localiser **l’initiateur Microsoft iSCSI**. Si la réponse est oui, accédez directement à l’étape 7 ci-dessous. 

4.  Si vous ne pouvez pas localiser le service Initiateur iSCSI de Microsoft indiqué à l’étape 3, vérifiez si, sous **Gestionnaire de périphériques** -> **Contrôleurs de stockage**, l’entrée **Appareil inconnu** s’affiche, associée à l’ID matériel **ROOT\ISCSIPRT**.

5.  Cliquez avec le bouton droit sur l’entrée **Appareil inconnu** et sélectionnez **Mettre à jour le pilote**.

6.  Mettez à jour le pilote, en sélectionnant l’option **Rechercher automatiquement un pilote logiciel mis à jour**. Suite à la mise à jour, l’entrée **Appareil inconnu** est remplacée par **Initiateur Microsoft iSCSI**, comme indiqué ci-dessous. 

    ![Chiffrement](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Cliquez sur **Gestionnaire des tâches** -> **Services (local)** -> **Service Initiateur iSCSI de Microsoft**. 

    ![Chiffrement](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Redémarrez le service Initiateur iSCSI de Microsoft en cliquant avec le bouton droit sur ce dernier, en cliquant sur Arrêter, puis en cliquant à nouveau sur le bouton droit et en sélectionnant **Démarrer**.

9.  Recommencez la récupération à l’aide de la restauration instantanée. 

Si elle échoue encore, redémarrez votre serveur/client. Si un redémarrage n’est pas souhaitable, ou si la récupération échoue même après le redémarrage du serveur, essayez de l’exécuter au moyen d’un autre ordinateur en suivant les étapes décrites dans [cet article](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes
* Obtenir plus de détails sur [comment sauvegarder votre serveur Windows avec l’agent Sauvegarde Azure](tutorial-backup-windows-server-to-azure.md).
* Si vous avez besoin de restaurer une sauvegarde, utilisez cet article pour [restaurer des fichiers sur un ordinateur Windows](backup-azure-restore-windows-server.md).
