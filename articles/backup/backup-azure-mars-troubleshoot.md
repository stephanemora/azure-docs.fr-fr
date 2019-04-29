---
title: Résoudre les problèmes de l’agent de sauvegarde Azure
description: Résoudre les problèmes liés à l’installation et l’inscription de l’agent Sauvegarde Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: saurse
ms.openlocfilehash: 4bad788156b2068f24484d3b248f2091409752ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216098"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Résoudre les problèmes liés à l'agent MARS (Microsoft Azure Recovery Services)

Voici comment résoudre les erreurs qui peuvent survenir lors de la configuration, de l'inscription, de la sauvegarde et de la restauration.

## <a name="invalid-vault-credentials-provided"></a>Informations d’identification du coffre fournies non valides

| Détails de l’erreur | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |
| **Error** </br> *Informations d’identification du coffre fournies non valides. Cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération. (ID : 34513)* | <ul><li> Les informations d’identification du coffre sont invalides (c’est-à-dire qu’elles ont été téléchargées plus de 48 heures avant l’heure de l’inscription).<li>L’agent MARS ne peut pas télécharger de fichiers dans le répertoire Temp de Windows. <li>Les informations d’identification du coffre se trouvent sur un emplacement réseau. <li>TLS 1.0 est désactivé<li> Un serveur proxy configuré bloque la connexion. <br> |  <ul><li>Téléchargez les nouvelles informations d’identification de coffre. (**Remarque** : si plusieurs fichiers d’informations d’identification de coffre sont téléchargées précédemment, seul le dernier fichier téléchargé est valide dans les 48 heures.) <li>Lancez **IE** > **Paramètres** > **Options Internet** > **Sécurité** > **Internet**. Ensuite, sélectionnez **Personnaliser le niveau** et faites défiler jusqu’à la section téléchargement de fichiers. Ensuite, sélectionnez **Activer**.<li>Vous devrez peut-être aussi ajouter ces sites dans IE à vos [sites de confiance](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Modifiez les paramètres pour utiliser un serveur proxy. Fournissez ensuite les détails du serveur proxy. <li> Faites correspondre la date et l’heure avec celles de votre ordinateur.<li>Si vous obtenez une erreur indiquant que les téléchargements de fichiers ne sont pas autorisés, il est probable qu’il y ait un nombre élevé de fichiers dans le répertoire C:/Windows/Temp.<li>Accédez à C:/Windows/Temp et vérifiez s’il y a plus de 60 000 ou 65 000 fichiers comportant l’extension .tmp. Si c’est le cas, supprimez ces fichiers.<li>Assurez-vous que .NET Framework 4.6.2 est installé. <li>Si vous avez désactivé TLS 1.0 en raison de la conformité avec PCI, consultez cette [Page de dépannage](https://support.microsoft.com/help/4022913). <li>Si vous avez des logiciels ou programmes antivirus installés sur le serveur, excluez les fichiers suivants de l’analyse antivirus : <ul><li>CBengine.exe<li>CSC.exe, qui est lié à .NET Framework. Il existe un CSC.exe pour chaque version .NET installée sur le serveur. Excluez les fichiers CSC.exe liés à toutes les versions de .NET Framework sur le serveur concerné. <li>Emplacement du dossier temporaire ou du cache. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>Le dossier Bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin

## <a name="unable-to-download-vault-credential-file"></a>Impossible de télécharger le fichier d’informations d’identification de coffre

| Détails de l’erreur | Actions recommandées |
| ---     | ---    |
|Échec de téléchargement du fichier d'informations d'identification du coffre. (ID : 403) | <ul><li> Essayez de télécharger les informations d’identification du coffre à l’aide d'un autre navigateur ou suivez la procédure ci-dessous : <ul><li> Lancez Internet Explorer, appuyez sur F12. </li><li> Accédez à l'onglet **Réseau** onglet pour effacer le cache IE et les cookies. </li> <li> Actualisez la page.<br>(OU)</li></ul> <li> Vérifiez si l'abonnement est désactivé ou a expiré.<br>(OU)</li> <li> Vérifiez si une règle de pare-feu bloque le téléchargement du fichier d’informations d’identification du coffre. <br>(OU)</li> <li> Assurez-vous de ne pas avoir atteint la limite relative au coffre (50 machines par coffre).<br>(OU)</li>  <li> Vérifiez l’utilisateur a l’autorisation de sauvegarde Azure pour télécharger les informations d’identification du coffre et inscrire le serveur avec le coffre, consultez requise [article](backup-rbac-rs-vault.md)</li></ul> | 

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L’agent Microsoft Azure Recovery Service n’a pas pu se connecter à la Sauvegarde Microsoft Azure

| Détails de l’erreur | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |
| **Error** <br /><ol><li>*L’agent Microsoft Azure Recovery Service n’a pas pu se connecter à la Sauvegarde Microsoft Azure. (ID : 100050) Vérifiez vos paramètres réseau et assurez-vous que vous pouvez vous connecter à Internet*<li>*(407) Authentification proxy requise* |Le proxy bloque la connexion. |  <ul><li>Lancez **IE** > **Paramètres** > **Options Internet** > **Sécurité** > **Internet**. Sélectionnez ensuite **Personnaliser le niveau** et faites défiler jusqu’à la section téléchargement de fichiers. Sélectionnez **Activer**.<li>Vous devrez peut-être aussi ajouter ces sites dans IE à vos [sites de confiance](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Modifiez les paramètres pour utiliser un serveur proxy. Fournissez ensuite les détails du serveur proxy. <li>Si vous avez des logiciels ou programmes antivirus installés sur le serveur, excluez les fichiers suivants de l’analyse antivirus. <ul><li>CBEngine.exe (au lieu de dpmra.exe).<li>CSC.exe (lié à .NET Framework). Il existe un CSC.exe pour chaque version .NET installée sur le serveur. Excluez les fichiers CSC.exe qui sont liés à toutes les versions de .NET Framework sur le serveur concerné. <li>Emplacement du dossier temporaire ou du cache. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>Le dossier Bin C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Échec de définition de la clé de chiffrement pour les sauvegardes sécurisées

| Détails de l’erreur | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |
| **Error** <br />*Échec de définition de la clé de chiffrement pour les sauvegardes sécurisées. L’activation n’a pas entièrement abouti, mais la phrase secrète de chiffrement a été enregistrée dans le fichier suivant*. |<li>Le serveur est déjà inscrit auprès d’un autre coffre.<li>Lors de la configuration, la phrase secrète a été endommagée.| Désinscrivez le serveur du coffre et réinscrivez-le avec une nouvelle phrase secrète.

## <a name="the-activation-did-not-complete-successfully"></a>L’activation n’a pas abouti

| Détails de l’erreur | Causes possibles | Actions recommandées |
|---------|---------|---------|
|**Error** <br /><ol>*L’activation n’a pas réussi. Échec de l’opération en cours en raison d’une erreur de service interne [0x1FC07]. Réessayez l’opération après un certain temps. Si le problème persiste, contactez le support technique Microsoft*     | <li> Le dossier temporaire se situe sur un volume dont l’espace est insuffisant. <li> Le dossier temporaire a été incorrectement déplacé vers un autre emplacement. <li> Le fichier OnlineBackup.KEK est manquant.         | <li>Effectuez une mise à niveau vers la [dernière version](https://aka.ms/azurebackup_agent) de l’Agent MARS.<li>Déplacez le dossier temporaire ou l’emplacement du cache vers un volume avec un espace disponible équivalent à 5-10 % de la taille totale des données de sauvegarde. Pour déplacer correctement l’emplacement du cache, reportez-vous aux étapes contenues dans [Questions sur l’agent de Sauvegarde Microsoft Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Assurez-vous que le fichier OnlineBackup.KEK est présent. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La phrase secrète de chiffrement n’est pas correctement configurée

| Détails de l’erreur | Causes possibles | Actions recommandées |
|---------|---------|---------|
|**Error** <br /><ol>*Erreur 34506. La phrase secrète de chiffrement stockée sur cet ordinateur n’est pas correctement configurée*.    | <li> Le dossier temporaire se situe sur un volume dont l’espace est insuffisant. <li> Le dossier temporaire a été incorrectement déplacé vers un autre emplacement. <li> Le fichier OnlineBackup.KEK est manquant.        | <li>Effectuez une mise à niveau vers la [dernière version](https://aka.ms/azurebackup_agent) de l’Agent MARS.<li>Déplacez le dossier temporaire ou l’emplacement du cache vers un volume avec un espace disponible équivalent à 5-10 % de la taille totale des données de sauvegarde. Pour déplacer correctement l’emplacement du cache, reportez-vous aux étapes contenues dans [Questions sur l’agent de Sauvegarde Microsoft Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Assurez-vous que le fichier OnlineBackup.KEK est présent. <br>*L’emplacement par défaut du dossier temporaire ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Les sauvegardes ne s'exécutent pas comme prévu
Si les sauvegardes planifiées ne se déclenchent pas automatiquement, alors que vous n’avez aucun problème à effectuer des sauvegardes manuelles, essayez ceci :

- Vérifiez la planification de sauvegarde de Windows Server n’est pas en conflit avec Azure planification de sauvegarde de fichiers et dossiers.
- Accédez à **Panneau de configuration** > **Outils d’administration** > **Planificateur de tâches**. Développez **Microsoft** et sélectionnez **Sauvegarde en ligne**. Double-cliquez sur **Microsoft-OnlineBackup** et accédez à l’onglet **Déclencheurs**. Assurez-vous que l’état est défini sur **Activé**. Si ce n’est pas le cas, sélectionnez **Modifier**, cochez la case **Activé**, puis cliquez sur **OK**. Sous l'onglet **Général**, accédez à **Options de sécurité** et assurez-vous que le compte d’utilisateur sélectionné pour l’exécution de la tâche est **SYSTÈME** ou le **groupe Administrateurs locaux** sur le serveur.

- Vérifiez que PowerShell 3.0 ou version ultérieure est installé sur le serveur. Pour vérifier la version de PowerShell, exécutez la commande suivante et vérifiez que le numéro de la version *principale* est supérieur ou égal à 3.

  `$PSVersionTable.PSVersion`

- Vérifiez que le chemin d’accès suivant fait partie de la variable d'environnement *PSMODULEPATH*.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Pour *LocalMachine*, si la stratégie d’exécution de Powershell est « restreinte », l’applet de commande Powershell qui déclenche la tâche de sauvegarde peut échouer. Exécutez les commandes suivantes en mode élevé pour vérifier la stratégie d’exécution et la définir sur *Unrestricted* ou *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

> [!TIP]
> Pour veiller à ce que les modifications soient appliquées de manière cohérente, redémarrez le serveur après avoir suivi les étapes ci-dessus.


## <a name="troubleshoot-restore-issues"></a>Résoudre les problèmes de restauration

Sauvegarde Azure peut ne pas correctement monter le volume de récupération, même au bout de plusieurs minutes. Vous pouvez également recevoir des messages d’erreur pendant le processus. Pour entamer normalement la récupération, procédez comme suit :

1.  Si le processus de montage s’exécute depuis plusieurs minutes, annulez-le.

2.  Vérifiez que vous disposez de la dernière version de l’agent Sauvegarde Azure. Pour identifier la version, dans le volet **Actions** de la console MARS, sélectionnez **À propos de l'agent Microsoft Azure Recovery Services**. Vérifiez que le numéro de la **version** est supérieur ou égal à la celui mentionné dans [cet article](https://go.microsoft.com/fwlink/?linkid=229525). Vous pouvez télécharger la dernière version [ici](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Cliquez sur **Gestionnaire de périphériques** > **Contrôleurs de stockage** et localisez le service **Initiateur Microsoft iSCSI**. Si vous parvenez à le localiser, accédez directement à l’étape 7.

4.  Si vous ne parvenez pas à localiser le service Initiateur Microsoft iSCSI, essayez de trouver sous **Gestionnaire de périphériques** > **Contrôleurs de stockage** l’entrée **Appareil inconnu** associée à l’ID matériel **ROOT\ISCSIPRT**.

5.  Cliquez avec le bouton droit sur l’entrée **Appareil inconnu** et sélectionnez **Mettre à jour le pilote**.

6.  Mettez à jour le pilote, en sélectionnant l’option **Rechercher automatiquement un pilote logiciel mis à jour**. Suite à la mise à jour, l’entrée **Appareil inconnu** est remplacée par **Initiateur Microsoft iSCSI**, comme indiqué ci-dessous.

    ![Capture d’écran du Gestionnaire de périphériques de Sauvegarde Azure, avec Contrôleurs de stockage en surbrillance](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Cliquez sur **Gestionnaire des tâches** > **Services (local)** > **Service Initiateur iSCSI de Microsoft**.

    ![Capture d’écran du Gestionnaire des tâches de Sauvegarde Azure, avec Services (local) en surbrillance](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Redémarrez le service Initiateur Microsoft iSCSI. Pour ce faire, cliquez sur le service avec le bouton droit, sélectionnez **Arrêter**, cliquez de nouveau sur le bouton droit et sélectionnez **Démarrer**.

9.  Retentez la récupération à l’aide de la [**Restauration instantanée**](backup-instant-restore-capability.md).

Si celle-ci échoue encore, redémarrez votre serveur ou client. Si vous ne souhaitez pas redémarrer, ou si la récupération échoue encore après le redémarrage du serveur, essayez de l'exécuter au moyen d'un autre ordinateur. Suivez les étapes décrites dans [cet article](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

## <a name="next-steps"></a>Étapes suivantes
* Obtenir plus de détails sur [comment sauvegarder votre serveur Windows avec l’agent Sauvegarde Azure](tutorial-backup-windows-server-to-azure.md).
* Si vous avez besoin de restaurer une sauvegarde, utilisez cet article pour [restaurer des fichiers sur un ordinateur Windows](backup-azure-restore-windows-server.md).
