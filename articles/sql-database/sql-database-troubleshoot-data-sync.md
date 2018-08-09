---
title: Résoudre les problèmes liés à Azure SQL Data Sync | Microsoft Docs
description: Découvrez comment résoudre les problèmes courants liés à Azure SQL Data Sync.
services: sql-database
ms.date: 07/16/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 8ba4b32f45dd978439b08650e498c3030c618aab
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618707"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Résoudre les problèmes liés à SQL Data Sync

Cet article explique comment résoudre les problèmes connus liés à Azure SQL Data Sync. S’il existe un moyen de résoudre un problème, celui-ci est décrit ici.

Pour obtenir une vue d’ensemble de SQL Data Sync, consultez [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](sql-database-sync-data.md).

## <a name="sync-issues"></a>Problèmes de synchronisation

- [La synchronisation de bases de données locales associées à l’agent client échoue dans l’interface utilisateur du portail](#sync-fails)

- [Mon groupe de synchronisation est bloqué à l’état de traitement](#sync-stuck)

- [Mes tables contiennent des données erronées](#sync-baddata)

- [Des données de clé primaire incohérentes s’affichent après une synchronisation réussie](#sync-pkdata)

- [Je constate une dégradation significative des performances](#sync-perf)

- [Le message suivant s’affiche : « Impossible d’insérer la valeur NULL dans la colonne<column>. Cette colonne n’accepte pas les valeurs NULL. » Que signifie cette erreur et comment puis-je la corriger ?](#sync-nulls)

- [Comment Data Sync traite-t-il les références circulaires ? Autrement dit, lorsque les mêmes données sont synchronisées dans plusieurs groupes de synchronisation et changent constamment en conséquence.](#sync-circ)

### <a name="sync-fails"></a> La synchronisation des bases de données locales associées à l’agent client échoue dans l’interface utilisateur du portail.

La synchronisation des bases de données locales associées à l’agent client échoue dans l’interface utilisateur du portail SQL Data Sync. Sur l’ordinateur local qui exécute l’agent, le journal des événements affiche des erreurs System.IO.IOException. Ces erreurs indiquent que l’espace disponible sur le disque est insuffisant.

- **Cause**. L’espace dont dispose le lecteur est insuffisant.

- **Résolution**. Libérez de l’espace sur le lecteur où se trouve le répertoire %TEMP%.

### <a name="sync-stuck"></a> Mon groupe de synchronisation est bloqué à l’état de traitement

Un groupe de synchronisation de SQL Data Sync est en cours de traitement depuis un certain temps. Il ne répond pas à la commande d’**arrêt** et aucune nouvelle entrée n’apparaît dans les journaux.

L’une des conditions suivantes peut provoquer le blocage d’un groupe de synchronisation sur l’état de traitement :

- **Cause**. L’agent client est hors connexion.

- **Résolution**. Vérifiez que l’agent client est en ligne, puis réessayez.

- **Cause**. L’agent client est désinstallé ou manquant

- **Résolution**. Si l’agent client est désinstallé ou manquant :

    1. Accédez au dossier d’installation de SQL Data Sync et supprimez le fichier XML de l’agent, si ce fichier existe.
    1. Installez l’agent sur un ordinateur local (il peut s’agir du même ordinateur ou d’un autre ordinateur). Envoyez ensuite la clé générée dans le portail pour l’agent qui apparaît comme étant hors connexion.

- **Cause**. Le service SQL Data Sync est arrêté.

- **Résolution**. Redémarrez le service SQL Data Sync.

    1. Dans le menu **Démarrer**, recherchez **Services**.
    1. Dans les résultats de la recherche, sélectionnez **Services**.
    1. Trouvez le service **SQL Data Sync**.
    1. Si l’état du service est **Arrêté**, cliquez avec le bouton droit sur le nom du service, puis sélectionnez **Démarrer**.

> [!NOTE]
> Si les informations précédentes ne permettent pas à votre groupe de synchronisation de quitter l’état de traitement, le Support Microsoft peut réinitialiser son état. Pour demander la réinitialisation de l’état de votre groupe de synchronisation, créez un billet dans le [Forum Microsoft Azure SQL Database](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted). Dans le billet, indiquez votre ID d’abonnement et l’ID du groupe de synchronisation à réinitialiser. Un ingénieur du Support Microsoft répondra à votre billet et vous préviendra dès que l’état aura été réinitialisé.

### <a name="sync-baddata"></a> Mes tables contiennent des données erronées.

Si des tables portant le même nom mais provenant de schémas de base de données différents sont incluses dans une synchronisation, des données erronées apparaissent dans les tables à l’issue de la synchronisation.

- **Cause**. Le processus de déploiement de SQL Data Sync utilise les mêmes tables de suivi pour les tables qui portent le même nom mais proviennent de schémas différents. Par conséquent, les modifications des deux tables sont répercutées dans la même table de suivi. Et cela génère des modifications de données erronées pendant la synchronisation.

- **Résolution**. Assurez-vous que les noms des tables impliquées dans une synchronisation sont différents, même si elles appartiennent à des schémas de base de données différents.

### <a name="sync-pkdata"></a> Des données de clé primaire incohérentes s’affichent après une synchronisation réussie

Une synchronisation est signalée comme réussie, et le journal n’affiche aucune ligne Échec ou Ignoré, mais vous constatez la présence de données de clé primaire incohérentes dans les bases de données du groupe de synchronisation.

- **Cause**. Ce résultat est lié à la conception. Toute modification d’une colonne de clé primaire génère des données incohérentes sur les lignes où la clé primaire a été modifiée.

- **Résolution**. Pour éviter ce problème, vérifiez qu’aucune donnée dans une colonne de clé primaire n’est modifiée. Pour résoudre ce problème, supprimez la ligne contenant les données incohérentes sur tous les points de terminaison du groupe de synchronisation. Puis réinsérez la ligne.

### <a name="sync-perf"></a> Je constate une dégradation significative des performances.

Les performances se dégradent considérablement, jusqu’à ce que vous ne soyez même plus en mesure d’ouvrir l’interface utilisateur de Data Sync.

- **Cause**. Le problème est probablement lié à une boucle de synchronisation. Une boucle de synchronisation se produit lorsqu’une synchronisation initiée par un groupe de synchronisation A déclenche une synchronisation par un groupe de synchronisation B, qui déclenche à son tour une synchronisation par le groupe de synchronisation A. La réalité est parfois plus complexe, puisque plus de deux groupes de synchronisation peuvent être impliqués dans la boucle. Le problème est lié au déclenchement circulaire de la synchronisation, lui-même provoqué par le chevauchement des groupes de synchronisation.

- **Résolution**. Le meilleur correctif est la prévention. Vérifiez l’absence de références circulaires dans vos groupes de synchronisation. Lorsqu’une ligne est synchronisée par un groupe de synchronisation, elle ne peut pas l’être par un autre groupe de synchronisation.

### <a name="sync-nulls"></a> Le message suivant s’affiche : « Impossible d’insérer la valeur NULL dans la colonne<column>. Cette colonne n’accepte pas les valeurs NULL. » Que signifie cette erreur et comment puis-je la corriger ? 
Ce message d’erreur indique que l’un des deux problèmes suivants est survenu :
-  Une table ne possède pas de clé primaire. Pour résoudre ce problème, ajoutez une clé primaire à toutes les tables que vous synchronisez.
-  Votre instruction CREATE INDEX contient une clause WHERE. Data Sync ne traite pas cette condition. Pour résoudre ce problème, supprimez la clause WHERE ou apportez manuellement les modifications à toutes les bases de données. 
 
### <a name="sync-circ"></a> Comment Data Sync traite-t-il les références circulaires ? Autrement dit, lorsque les mêmes données sont synchronisées dans plusieurs groupes de synchronisation et changent constamment en conséquence ?
Data Sync ne traite pas les références circulaires. Veillez à les éviter. 

## <a name="client-agent-issues"></a>Problèmes liés à l’agent client

- [L’installation, la désinstallation ou la réparation de l’agent client échoue.](#agent-install)

- [L’agent client ne fonctionne pas après l’annulation de la désinstallation.](#agent-uninstall)

- [Ma base de données n’est pas répertoriée dans la liste des agents.](#agent-list)

- [L’agent client ne démarre pas (Erreur 1069).](#agent-start)

- [Je ne parviens pas à envoyer la clé d’un agent.](#agent-key)

- [L’agent client ne peut pas être supprimé du portail si la base de données locale associée est inaccessible.](#agent-delete)

- [L’application locale Agent de synchronisation ne peut pas se connecter au service de synchronisation local.](#agent-connect)

### <a name="agent-install">L’installation, la désinstallation ou la réparation de l’agent client échoue.</a>

- **Cause**. Cet échec peut avoir de nombreuses causes. Pour déterminer la cause spécifique de cet échec, consultez les journaux.

- **Résolution**. Pour rechercher la cause spécifique de l’échec rencontré, générez et consultez les journaux Windows Installer. Vous pouvez activer la journalisation à partir d’une invite de commandes. Par exemple, si le fichier AgentServiceSetup.msi téléchargé est LocalAgentHost.msi, générez et examinez les fichiers journaux à l’aide des lignes de commande suivantes :

    -   Pour les installations : `msiexec.exe /i SQLDataSyncAgent-Preview-ENU.msi /l\*v LocalAgentSetup.InstallLog`
    -   Pour les désinstallations : `msiexec.exe /x SQLDataSyncAgent-se-ENU.msi /l\*v LocalAgentSetup.InstallLog`

    Vous pouvez également activer la journalisation pour toutes les installations effectuées par Windows Installer. L’article de la Base de connaissances Microsoft [Guide pratique pour activer la journalisation de Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fournit une solution en un clic pour activer la journalisation pour Windows Installer. Il indique également l’emplacement des journaux.

### <a name="agent-uninstall"></a> Mon agent client ne fonctionne pas après l’annulation de la désinstallation.

L’agent client ne fonctionne pas, même après l’annulation de sa désinstallation.

- **Cause**. Ce problème survient car l’agent client SQL Data Sync ne stocke pas les informations d’identification.

- **Résolution**. Vous pouvez essayer les deux solutions suivantes :

    -   Utilisez services.msc afin de réentrer les informations d’identification pour l’agent client.
    -   Désinstallez cet agent client, puis installez-en un nouveau. Téléchargez et installez l’agent client le plus récent à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Ma base de données n’est pas répertoriée dans la liste des agents.

Lorsque vous essayez d’ajouter une base de données SQL Server existante à un groupe de synchronisation, la base de données n’apparaît pas dans la liste des agents.

Ce problème peut avoir les causes suivantes :

- **Cause**. L’agent client et le groupe de synchronisation se trouvent dans des centres de données différents.

- **Résolution**. L’agent client et le groupe de synchronisation doivent se trouver dans le même centre de données. Pour cela, vous disposez de deux options :

    -   Créez un agent dans le centre de données où se trouve le groupe de synchronisation. Puis inscrivez la base de données auprès de cet agent.
    -   Supprimez le groupe de synchronisation actuel. Recréez ensuite le groupe de synchronisation dans le centre de données où se trouve l’agent.

- **Cause**. La liste des bases de données de l’agent client n’est pas à jour.

- **Résolution**. Arrêtez, puis redémarrez le service agent client.

    L’agent local télécharge la liste des bases de données associées uniquement lors du premier envoi de la clé de l’agent. Il ne la télécharge pas lors des envois suivants. Les bases de données inscrites pendant le déplacement d’un agent ne sont pas visibles sur l’instance d’origine de l’agent.

### <a name="agent-start"></a> L’agent client ne démarre pas (Erreur 1069).

Vous découvrez que l’agent n’est pas exécuté sur un ordinateur qui héberge SQL Server. Lorsque vous essayez de démarrer manuellement l’agent, une boîte de dialogue affiche le message « Erreur 1069 : L’échec d’une ouverture de session a empêché le démarrage du service ».

![Boîte de dialogue de l’erreur 1069 Data Sync](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Cause**. Cette erreur peut être due au fait que le mot de passe du serveur local a changé depuis que vous avez créé l’agent et son mot de passe.

- **Résolution**. Remplacez le mot de passe de l’agent par le mot de passe actuel du serveur :

  1. Recherchez le service de l’agent du client SQL Data Sync.  
    a. Sélectionnez **Démarrer**.  
    b. Dans la zone de recherche, entrez **services.msc**.  
    c. Dans les résultats de la recherche, sélectionnez **Services**.  
    d. Dans la fenêtre **Services**, faites défiler jusqu’à l’entrée **Agent SQL Data Sync**.  
  1. Cliquez avec le bouton droit sur **Agent SQL Data Sync**, puis sélectionnez **Arrêter**.
  1. Cliquez avec le bouton droit sur **Agent SQL Data Sync**, puis sélectionnez **Propriétés**.
  1. Dans **Propriétés de l’agent SQL Data Sync**, sélectionnez l’onglet **Connexion**.
  1. Dans la zone **Mot de passe**, entrez votre mot de passe.
  1. Dans la zone **Confirmer le mot de passe**, entrez de nouveau votre mot de passe.
  1. Sélectionnez **Apply** (Appliquer), puis **OK**.
  1. Dans la fenêtre **Services**, cliquez avec le bouton droit sur le service **Agent SQL Data Sync**, puis cliquez sur **Démarrer**.
  1. Fermez la fenêtre **Services**.

### <a name="agent-key"></a> Je ne parviens pas à envoyer la clé d’un agent.

Une fois que vous avez créé ou recréé la clé d’un agent, vous essayez d’envoyer cette clé par le biais de l’application SqlAzureDataSyncAgent. Mais l’envoi échoue.

![Boîte de dialogue d’erreur de synchronisation - Impossible d’envoyer la clé d’agent](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Conditions préalables**. Avant de continuer, vérifiez les conditions préalables suivantes :

  - Le service Windows SQL Data Sync est en cours d’exécution.

  - Le compte de service pour le service Windows SQL Data Sync a accès au réseau.

  - Le port 1433 de trafic sortant est ouvert dans votre règle de pare-feu local.

  - L’adresse IP locale est ajoutée au serveur ou à la règle de pare-feu de base de données pour la base de données de métadonnées de synchronisation.

- **Cause**. La clé d’agent identifie de façon unique chaque agent local. La clé doit remplir deux conditions :

  -   La clé de l’agent client sur le serveur SQL Data Sync et l’ordinateur local doivent être identiques.
  -   La clé de l’agent client ne peut être utilisée qu’une seule fois.

- **Résolution**. Si votre agent ne fonctionne pas, cela signifie que l’une de ces conditions (ou les deux) n’est pas remplie. Pour que votre agent fonctionne de nouveau :

  1. Générez une nouvelle clé.
  1. Appliquez la nouvelle clé à l’agent.

  Pour appliquer la nouvelle clé à l’agent :

  1. Dans l’Explorateur de fichiers, accédez au répertoire d’installation de votre agent. Le répertoire d’installation par défaut est C:\\Program Files (x86)\\Microsoft SQL Data Sync.
  1. Double-cliquez sur le sous-répertoire bin.
  1. Ouvrez l’application SqlAzureDataSyncAgent.
  1. Sélectionnez **Envoyer la clé d’agent**.
  1. Collez la clé placée dans votre Presse-papiers à l’espace prévu à cet effet.
  1. Sélectionnez **OK**.
  1. Fermez le programme.

### <a name="agent-delete"></a> L’agent client ne peut pas être supprimé du portail si la base de données locale associée est inaccessible.

Si un point de terminaison local (autrement dit, une base de données) inscrite auprès d’un agent client SQL Data Sync devient inaccessible, l’agent client ne peut pas être supprimé.

- **Cause**. L’agent local ne peut pas être supprimé, car la base de données inaccessible est encore inscrite auprès de l’agent. Quand vous tentez de supprimer l’agent, le processus de suppression essaie d’atteindre la base de données, et il échoue.

- **Résolution**. Utilisez « Forcer la suppression » pour supprimer la base de données inaccessible.

> [!NOTE]
> S’il reste des tables de métadonnées de synchronisation après une opération « Forcer la suppression », utilisez `deprovisioningutil.exe` pour les nettoyer.

### <a name="agent-connect"></a> L’application locale Agent de synchronisation ne peut pas se connecter au service de synchronisation local.

- **Résolution**. Essayez les étapes suivantes :

  1. Quittez l’application.  
  1. Ouvrez le panneau Services de composants.  
    a. Dans la zone de recherche de la barre des tâches, entrez **services.msc**.  
    b. Dans les résultats de la recherche, double-cliquez sur **Services**.  
  1. Arrêtez le service **SQL Data Sync**.
  1. Redémarrez le service **SQL Data Sync**.  
  1. Rouvrez l’application.

## <a name="setup-and-maintenance-issues"></a>Problèmes d’installation et de maintenance

- [Je reçois un message « Espace disque insuffisant ».](#setup-space)

- [Je ne parviens pas à supprimer mon groupe de synchronisation.](#setup-delete)

- [Je ne peux pas annuler l’inscription d’une base de données SQL Server locale.](#setup-unreg)

- [Je ne dispose pas des privilèges suffisants pour démarrer les services système.](#setup-perms)

- [Une base de données a un état « Obsolète ».](#setup-date)

- [Un groupe de synchronisation a un état « Obsolète ».](#setup-date2)

- [Impossible de supprimer un groupe de synchronisation dans les trois minutes qui suivent la désinstallation ou l’arrêt de l’agent.](#setup-delete2)

- [Que se passe-t-il quand je restaure une base de données perdue ou endommagée ?](#setup-restore)

### <a name="setup-space">Je reçois un message « Espace disque insuffisant ».</a>

- **Cause**. Le message « Espace disque insuffisant » peut s’afficher s’il reste des fichiers à supprimer. Ce problème peut être dû à un logiciel antivirus. Il peut également survenir lorsque des fichiers sont ouverts pendant une tentative de suppression.

- **Résolution**. Supprimez manuellement les fichiers de synchronisation situés dans le dossier %Temp% (`del \*sync\* /s`). Puis supprimez les sous-répertoires du dossier %Temp%.

> [!IMPORTANT]
> Ne supprimez aucun fichier tant que la synchronisation est en cours.

### <a name="setup-delete"></a> Je ne parviens pas à supprimer mon groupe de synchronisation.

Votre tentative de suppression d’un groupe de synchronisation échoue. La suppression d’un groupe de synchronisation peut échouer dans les situations suivantes :

- **Cause**. L’agent client est hors connexion

- **Résolution**. Vérifiez que l’agent client est en ligne, puis réessayez.

- **Cause**. L’agent client est désinstallé ou manquant

- **Résolution**. Si l’agent client est désinstallé ou manquant :  
    a. Accédez au dossier d’installation de SQL Data Sync et supprimez le fichier XML de l’agent, si ce fichier existe.  
    b. Installez l’agent sur un ordinateur local (il peut s’agir du même ordinateur ou d’un autre ordinateur). Envoyez ensuite la clé générée dans le portail pour l’agent qui apparaît comme étant hors connexion.

- **Cause**. Une base de données est hors connexion

- **Résolution**. Vérifiez que vos bases de données SQL et SQL Server sont toutes en ligne.

- **Cause**. Le groupe de synchronisation est en cours de déploiement ou de synchronisation.

- **Résolution**. Attendez que le processus de déploiement ou de synchronisation se termine, puis essayez à nouveau de supprimer le groupe de synchronisation.

### <a name="setup-unreg"></a> Je ne peux pas annuler l’inscription d’une base de données SQL Server locale.

- **Cause**. Vous essayez probablement d’annuler l’inscription d’une base de données qui a déjà été supprimée.

- **Résolution**. Pour annuler l’inscription d’une base de données SQL Server locale, sélectionnez la base de données, puis sélectionnez **Forcer la suppression**.

  Si cette opération ne permet pas de supprimer la base de données du groupe de synchronisation :

  1. Arrêtez le service hôte de l’agent client, puis redémarrez-le :  
    a. Sélectionnez le menu **Démarrer**.  
    b. Dans la zone de recherche, entrez **services.msc**.  
    c. Dans la section **Programmes** du volet des résultats de la recherche, double-cliquez sur **Services**.  
    d. Faites un clic droit sur le service **SQL Data Sync**.  
    e. Si le service est en cours d’exécution, arrêtez-le.  
    f. Cliquez avec le bouton droit sur le service, puis sélectionnez **Démarrer**.  
    g. Vérifiez si la base de données est toujours inscrite. Si elle n’est plus inscrite, vous avez terminé. Sinon, passez à l’étape suivante.
  1. Ouvrez l’application de l’agent client (SqlAzureDataSyncAgent).
  1. Sélectionnez **Modifier les informations d’identification**, puis entrez les informations d’identification de la base de données.
  1. Effectuez l’annulation de l’inscription.

### <a name="setup-perms"></a> Je ne dispose pas des privilèges suffisants pour démarrer les services système.

- **Cause**. Cette erreur se produit dans deux situations :
  -   Le nom d’utilisateur et/ou le mot de passe sont incorrects.
  -   Le compte d’utilisateur spécifié ne dispose pas des privilèges suffisants pour se connecter en tant que service.

- **Résolution**. Accordez des informations d’identification « Ouvrir une session en tant que service » au compte d’utilisateur :

  1. Accédez à **Démarrer** > **Panneau de configuration** > **Outils d’administration** > **Stratégie de sécurité locale** > **Stratégie locale** > **Gestion des droits de l’utilisateur**.
  1. Sélectionnez **Ouvrir une session en tant que service**.
  1. Dans la boîte de dialogue **Propriétés**, ajoutez le compte d’utilisateur.
  1. Sélectionnez **Apply** (Appliquer), puis **OK**.
  1. Fermez toutes les fenêtres.

### <a name="setup-date"></a> Une base de données a un état « Obsolète ».

- **Cause**. SQL Data Sync supprime du service les bases de données qui sont hors connexion depuis 45 jours ou plus (délai calculé à partir du moment où la base de données a basculé hors connexion). Si une base de données est hors connexion pendant au moins 45 jours, puis qu’elle rebascule en ligne, son état est défini sur **Obsolète**.

- **Résolution**. Pour éviter l’état **Obsolète**, veillez à ce qu’aucune de vos bases de données ne reste hors ligne pendant 45 jours ou plus.

  Si l’état d’une base de données est **Obsolète** :

  1. Supprimez la base de données dont l’état est **Obsolète** du groupe de synchronisation.
  1. Réajouter la base de données dans le groupe de synchronisation.

  > [!WARNING]
  > Vous perdez toutes les modifications apportées à cette base de données pendant qu’elle était hors connexion.

### <a name="setup-date2"></a> Un groupe de synchronisation a un état « Obsolète ».

- **Cause**. Si une ou plusieurs modifications ne sont pas appliquées pendant la période de rétention de 45 jours, un groupe de synchronisation peut devenir obsolète.

- **Résolution**. Pour éviter que l’état d’un groupe de synchronisation ne devienne **Obsolète**, examinez régulièrement les résultats de vos travaux de synchronisation dans la visionneuse de l’historique. Recherchez et résolvez les problèmes liés aux modifications qui ne sont pas appliquées.

  Si l’état d’un groupe de synchronisation est **Obsolète**, supprimez le groupe et recréez-le.

### <a name="setup-delete2"></a> Impossible de supprimer un groupe de synchronisation dans les trois minutes qui suivent la désinstallation ou l’arrêt de l’agent.

Vous ne pouvez pas supprimer un groupe de synchronisation dans les trois minutes qui suivent la désinstallation ou l’arrêt de l’agent client SQL Data Sync associé.

- **Résolution**.

  1. Supprimez un groupe de synchronisation pendant que les agents de synchronisation associés sont en ligne (recommandé).
  1. Si l’agent est hors connexion mais installé, mettez-le en ligne sur l’ordinateur local. Attendez que l’état de l’agent apparaisse comme **En ligne** sur le portail SQL Data Sync. Puis supprimez le groupe de synchronisation.
  1. Si l’agent est hors connexion parce qu’il a été désinstallé :  
    a.  Accédez au dossier d’installation de SQL Data Sync et supprimez le fichier XML de l’agent, si ce fichier existe.  
    b.  Installez l’agent sur un ordinateur local (il peut s’agir du même ordinateur ou d’un autre ordinateur). Envoyez ensuite la clé générée dans le portail pour l’agent qui apparaît comme étant hors connexion.  
    c. Essayez de supprimer le groupe de synchronisation.

### <a name="setup-restore"></a> Que se passe-t-il quand je restaure une base de données perdue ou endommagée ?

Si vous restaurez une base de données perdue ou endommagée à partir d’une sauvegarde, un problème de non-convergence des données peut survenir dans les groupes de synchronisation auxquels la base de données appartient.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL Data Sync, consultez :

-   [Synchroniser des données entre plusieurs bases de données locales et cloud avec Azure SQL Data Sync](sql-database-sync-data.md)  
-   [Configurer Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)  
-   [Bonnes pratiques pour Azure SQL Data Sync](sql-database-best-practices-data-sync.md)  
-   [Surveiller Azure SQL Data Sync avec Log Analytics](sql-database-sync-monitor-oms.md)  
-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :  
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)  

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
