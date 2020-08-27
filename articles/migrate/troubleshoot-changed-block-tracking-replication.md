---
title: Résoudre les problèmes de réplication dans la migration de machines virtuelles VMware sans agent
description: Obtenir de l’aide en cas d’échec du cycle de réplication
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 5748f758d8ac2f1723a20858920a4f261c07f938
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608655"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Résolution des problèmes de réplication dans la migration de machines virtuelles VMware sans agent

Cet article décrit certains problèmes courants et erreurs spécifiques que vous pouvez rencontrer lorsque vous répliquez des machines virtuelles VMware locales à l’aide de la méthode sans agent d’Azure Migrate : Évaluation du serveur.

Lorsque vous répliquez une machine virtuelle VMware à l’aide de la méthode de réplication sans agent, les données des disques de la machine virtuelle (vmdk) sont répliquées sur les disques managés de réplica dans votre abonnement Azure. Lorsque la réplication commence pour une machine virtuelle, un premier cycle de réplication se produit, dans lequel des copies complètes des disques sont répliquées. Une fois la réplication initiale terminée, des cycles de réplication incrémentielle sont planifiés régulièrement pour transférer les modifications qui se sont produites depuis le cycle de réplication précédent.

Il se peut que vous rencontriez parfois des échecs de cycles de réplication pour une machine virtuelle. Ces échecs peuvent se produire pour des raisons allant des problèmes de configuration du réseau local aux problèmes de serveur principal du service cloud d’Azure Migrate. Dans cet article, nous allons :

 - vous montrer comment analyser l’état de la réplication et résoudre les erreurs ;
 - répertorier certaines des erreurs de réplication les plus fréquentes et suggérer des étapes supplémentaires pour y remédier.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Analyser l’état de la réplication à l’aide du portail Azure

Pour analyser l’état de réplication de vos machines virtuelles, procédez comme suit :

  1. Accédez à la page Serveurs dans Azure Migrate sur le portail Azure.
  2. Accédez à la page « Réplication des machines » en cliquant sur « Serveurs de réplication » dans la vignette Migration du serveur.
  3. Vous verrez une liste de serveurs de réplication ainsi que des informations supplémentaires telles que l’état, l’intégrité, la dernière synchronisation, etc. La colonne Intégrité indique l’intégrité de réplication actuelle de la machine virtuelle. Une valeur « Critique » ou « Avertissement » dans la colonne Intégrité indique généralement que le cycle de réplication précédent de la machine virtuelle a échoué. Pour obtenir plus de détails, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez « Détails de l’erreur ». La page Détails de l’erreur contient des informations sur l’erreur et des détails supplémentaires sur la façon de résoudre le problème. Vous verrez également un lien « Événements récents » qui peut être utilisé pour accéder à la page des événements de la machine virtuelle.
  4. Cliquez sur « Événements récents » pour voir les échecs du cycle de réplication précédent pour la machine virtuelle. Dans la page Événements, recherchez l’événement le plus récent du type « Échec du cycle de réplication » ou « Échec du cycle de réplication pour le disque » de la machine virtuelle.
  5. Cliquez sur l’événement pour comprendre les causes possibles de l’erreur et les mesures correctives recommandées. Utilisez les informations fournies pour dépanner et corriger l’erreur.
    
## <a name="common-replication-errors"></a>Erreurs de réplication courantes

Cette section décrit quelques-unes des erreurs courantes et la façon dont vous pouvez les résoudre.

## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**ID d’erreur :** 181008

**Message d’erreur :** MV : VMName. Erreur : Événement d’expiration du délai « DisposeArtefactsTimeout » rencontré dans l’état &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')]'.

**Causes possibles :**

Le composant qui tente de répliquer des données dans Azure est indisponible ou ne répond pas. Les causes possibles sont les suivantes :

- Le service de passerelle exécuté dans l’appliance Azure Migrate est indisponible.
- Le service de passerelle rencontre des problèmes de connectivité au compte de stockage Service Bus/Event Hubs/de l’appliance.

**Identification de la cause exacte de DisposeArtefactsTimedOut et de la résolution correspondante :**

1. Vérifiez que l’appliance Azure Migrate est opérationnelle
2. Vérifiez si le service de passerelle est en cours d’exécution sur l’appliance :
   1.  Connectez-vous à l’appliance Azure Migrate à l’aide du Bureau à distance et procédez comme suit.

   2.  Ouvrez le composant logiciel enfichable MMC des services Microsoft (Exécuter > services.msc) et vérifiez si « Service de passerelle Microsoft Azure » est en cours d’exécution. Si le service est arrêté ou n’est pas en cours d’exécution, démarrez-le. Vous pouvez également ouvrir l’invite de commandes ou PowerShell pour effectuer l’opération : « Net Start asrgwy ».

3. Vérifiez les problèmes de connectivité entre l’appliance Azure Migrate et le cache Compte de stockage : 

    Après avoir téléchargé azcopy dans l’appliance Azure Migrate, exécutez la commande suivante :
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **Procédure d’exécution du test d’évaluation des performances :**
    
      1. [Téléchargez](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy.
        
      2. Recherchez l’appliance Compte de stockage dans le groupe de ressources. Le nom du compte de stockage ressemble à migrategwsa\*\*\*\*\*\*\*\*\*\*. Il s’agit de la valeur du paramètre [account] dans la commande ci-dessus.
        
      3. Recherchez votre compte de stockage dans le portail Azure. Assurez-vous que l’abonnement que vous utilisez pour effectuer la recherche est le même abonnement (abonnement cible) que celui dans lequel le compte de stockage est créé. Accédez à Conteneurs dans la section Service BLOB. Cliquez sur +Conteneur et créez un conteneur. Laissez le niveau d’accès public à la valeur sélectionnée par défaut.
        
      4. Sous Paramètres, accédez à Signature d’accès partagé. Sélectionnez Conteneur dans « Type de ressource autorisé ». Cliquez sur Générer une signature d’accès partagé et une chaîne de connexion. Copiez la valeur SAP.
        
      5. Exécutez la commande ci-dessus dans l’invite de commandes en remplaçant « account », « container » et « SAS » par les valeurs obtenues aux étapes 2, 3 et 4, respectivement.
        
      Vous pouvez également [télécharger](https://go.microsoft.com/fwlink/?linkid=2138967) Explorateur Stockage Azure sur l’appliance et essayer de charger 10 blobs d’environ 64 Mo dans les comptes de stockage. S’il n’y a pas de problème, le chargement devrait réussir.
        
    **Résolution :** Si ce test échoue, vous avez un problème de réseau. Demandez à votre équipe réseau locale de vérifier les problèmes de connectivité. En règle générale, certains paramètres de pare-feu peuvent être à l’origine des échecs.
    
4.  Vérifiez les problèmes de connectivité entre l’appliance Azure Migrate et Service Bus :

    Ce test vérifie si l’appliance Azure Migrate peut communiquer avec le serveur principal du service cloud Azure Migrate. L’appliance communique avec le serveur principal du service via Service Bus et des files d’attente de messages Event Hubs. Pour valider la connectivité de l’appliance à Service Bus, [téléchargez](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer, essayez de vous connecter à l’appliance Service Bus et d’envoyer ou de recevoir un message. S’il n’y a pas de problème, l’opération devrait aboutir.

    **Procédure d’exécution du test :**

    1. Copiez la chaîne de connexion à partir de l’instance Service Bus qui a été créée dans le projet Migrate.
    2. Ouvrez Service Bus Explorer.
    3. Accédez à Fichier, puis à Connexion.
    4. Collez la chaîne de connexion, puis cliquez sur Connexion.
    5. Cette opération ouvre l’espace de noms Service Bus.
    6. Dans la rubrique, sélectionnez Snapshot Manager. Cliquez avec le bouton droit sur Snapshot Manager, sélectionnez « Recevoir des messages » > sélectionnez « Aperçu », puis cliquez sur OK.
    7. Si la connexion est établie, le message « [x] messages reçus » s’affiche dans la sortie de la console. Si la connexion échoue, vous verrez un message indiquant que la connexion a échoué.
    
    **Résolution :** Si ce test échoue, vous avez un problème de réseau. Demandez à votre équipe réseau locale de vérifier les problèmes de connectivité. En règle générale, certains paramètres de pare-feu peuvent être à l’origine des échecs.

5. Vérifiez les problèmes de connectivité entre l’appliance Azure Migrate et Azure Key Vault :

    Ce test vérifie les problèmes de connectivité entre l’appliance Azure Migrate et Azure Key Vault : Le coffre de clés permet de gérer l’accès au compte de stockage utilisé pour la réplication.
    
    **Procédure de vérification de la connectivité :**
    
    1. Récupérez l’URI du coffre de clés dans la liste des ressources du groupe de ressources correspondant au projet Azure Migrate.
    
    1. Ouvrez PowerShell dans l’appliance Azure Migrate et exécutez la commande suivante :
    
    _test-netconnection Key Vault URI -P 443_
    
    Cette commande essaiera d’établir une connexion TCP et renverra une sortie.
    
     - Dans la sortie, vérifiez le champ « _TcpTestSucceeded_ ». Si la valeur est « _True_ », il n’y a pas de problème de connectivité entre l’appliance Azure Migrate et Azure Key Vault. Si la valeur est « False », il y a un problème de connectivité.
    
    **Résolution :** Si ce test échoue, il y a un problème de connectivité entre l’appliance Azure Migrate et Azure Key Vault. Demandez à votre équipe réseau locale de vérifier les problèmes de connectivité. En règle générale, certains paramètres de pare-feu peuvent être à l’origine des échecs.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**ID d’erreur :** 1010

**Message d’erreur :** Le chargement des données du disque DiskPath, DiskId de la machine virtuelle VMName, VMId ne s’est pas terminé dans le délai imparti.

Cette erreur indique généralement que l’appliance Azure Migrate effectuant la réplication n’est pas en mesure de se connecter à Azure Cloud Services, ou que la réplication progresse lentement, provoquant une expiration du délai d’attente du cycle de réplication.

Les causes possibles sont les suivantes :

- L’appliance Azure Migrate est indisponible.
- Le service de passerelle de réplication n’est pas en cours d’exécution sur l’appliance.
- Le service de passerelle de réplication rencontre des problèmes de connectivité avec l’un des composants suivants du service Azure qui sont utilisés pour la réplication : Service Bus/Event Hubs/Cache Azure Compte de stockage/Azure Key Vault.
- Le service de passerelle est limité au niveau vCenter lors de la tentative de lecture du disque.

**Identification de la cause racine et résolution du problème :**

1. Vérifiez que l’appliance Azure Migrate est opérationnelle
2. Vérifiez si le service de passerelle est en cours d’exécution sur l’appliance :
   1.  Connectez-vous à l’appliance Azure Migrate à l’aide du Bureau à distance et procédez comme suit.

   2.  Ouvrez le composant logiciel enfichable MMC des services Microsoft (Exécuter > services.msc) et vérifiez si « Service de passerelle Microsoft Azure » est en cours d’exécution. Si le service est arrêté ou n’est pas en cours d’exécution, démarrez-le. Vous pouvez également ouvrir l’invite de commandes ou PowerShell pour effectuer l’opération : « Net Start asrgwy ».


3. **Vérifiez les problèmes de connectivité entre l’appliance Azure Migrate et le cache Compte de stockage :** 

    Après avoir téléchargé azcopy dans l’appliance Azure Migrate, exécutez la commande suivante :
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **Procédure d’exécution du test d’évaluation des performances :**
    
      1. [Téléchargez](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy.
        
      2. Recherchez l’appliance Compte de stockage dans le groupe de ressources. Le nom du compte de stockage ressemble à migrategwsa\*\*\*\*\*\*\*\*\*\*. Il s’agit de la valeur du paramètre [account] dans la commande ci-dessus.
        
      3. Recherchez votre compte de stockage dans le portail Azure. Assurez-vous que l’abonnement que vous utilisez pour effectuer la recherche est le même abonnement (abonnement cible) que celui dans lequel le compte de stockage est créé. Accédez à Conteneurs dans la section Service BLOB. Cliquez sur +Conteneur et créez un conteneur. Laissez le niveau d’accès public à la valeur sélectionnée par défaut.
        
      4. Sous Paramètres, accédez à Signature d’accès partagé. Sélectionnez Conteneur dans « Type de ressource autorisé ». Cliquez sur Générer une signature d’accès partagé et une chaîne de connexion. Copiez la valeur SAP.
        
      5. Exécutez la commande ci-dessus dans l’invite de commandes en remplaçant « account », « container » et « SAS » par les valeurs obtenues aux étapes 2, 3 et 4, respectivement.
        
      Vous pouvez également [télécharger](https://go.microsoft.com/fwlink/?linkid=2138967) Explorateur Stockage Azure sur l’appliance et essayer de charger 10 blobs d’environ 64 Mo dans les comptes de stockage. S’il n’y a pas de problème, le chargement devrait réussir.
        
    **Résolution :** Si ce test échoue, vous avez un problème de réseau. Demandez à votre équipe réseau locale de vérifier les problèmes de connectivité. En règle générale, certains paramètres de pare-feu peuvent être à l’origine des échecs.
                
4.  **Vérifiez les problèmes de connectivité entre l’appliance Azure Migrate et Azure Service Bus :**

    Ce test vérifie si l’appliance Azure Migrate peut communiquer avec le serveur principal du service cloud Azure Migrate. L’appliance communique avec le serveur principal du service via Service Bus et des files d’attente de messages Event Hubs. Pour valider la connectivité de l’appliance à Service Bus, [téléchargez](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer, essayez de vous connecter à l’appliance Service Bus et d’envoyer ou de recevoir un message. S’il n’y a pas de problème, l’opération devrait aboutir.

    **Procédure d’exécution du test :**
    
    1. Copiez la chaîne de connexion à partir de l’instance Service Bus qui a été créée dans le groupe de ressources correspondant au projet Azure Migrate.
    
    1. Ouvrez Service Bus Explorer.
    
    1. Accédez à Fichier > Connexion
    
    1. Collez la chaîne de connexion que vous avez copiée à l’étape 1, puis cliquez sur Connexion.
    
    1. Cette opération ouvre l’espace de noms Service Bus.
    
    1. Dans la rubrique de l’espace de noms, sélectionnez Snapshot Manager. Cliquez avec le bouton droit sur Snapshot Manager, sélectionnez « Recevoir des messages » > sélectionnez « Aperçu », puis cliquez sur OK.
    
    Si la connexion est établie, le message « [x] messages reçus » s’affiche dans la sortie de la console. Si la connexion échoue, vous verrez un message indiquant que la connexion a échoué.
    
    **Résolution :** Si ce test échoue, il y a un problème de connectivité entre l’appliance Azure Migrate et Service Bus. Demandez à votre équipe réseau locale de vérifier ces problèmes de connectivité. En règle générale, certains paramètres de pare-feu peuvent être à l’origine des échecs.
    
 5. **Vérifiez les problèmes de connectivité entre l’appliance Azure Migrate et Azure Key Vault :**

    Ce test vérifie les problèmes de connectivité entre l’appliance Azure Migrate et Azure Key Vault : Le coffre de clés permet de gérer l’accès au compte de stockage utilisé pour la réplication.
    
    **Procédure de vérification de la connectivité :**
    
    1. Récupérez l’URI du coffre de clés dans la liste des ressources du groupe de ressources correspondant au projet Azure Migrate.
    
    1. Ouvrez PowerShell dans l’appliance Azure Migrate et exécutez la commande suivante :
    
    _test-netconnection Key Vault URI -P 443_
    
    Cette commande essaiera d’établir une connexion TCP et renverra une sortie.
    
    1. Dans la sortie, vérifiez le champ « _TcpTestSucceeded_ ». Si la valeur est « _True_ », il n’y a pas de problème de connectivité entre l’appliance Azure Migrate et Azure Key Vault. Si la valeur est « False », il y a un problème de connectivité.
    
    **Résolution :** Si ce test échoue, il y a un problème de connectivité entre l’appliance Azure Migrate et Azure Key Vault. Demandez à votre équipe réseau locale de vérifier les problèmes de connectivité. En règle générale, certains paramètres de pare-feu peuvent être à l’origine des échecs.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Une erreur s’est produite lors de la tentative de récupération des blocs modifiés

Message d’erreur : « Une erreur s’est produite lors de la tentative de récupération des blocs modifiés »

La méthode de réplication sans agent utilise la technologie Changed Block Tracking (CBT) de VMware pour répliquer les données sur Azure. CBT permet à l’outil Migration de serveur d’assurer le suivi des blocs qui ont été modifiés depuis le dernier cycle de réplication et de répliquer uniquement ces derniers. Cette erreur se produit si la fonctionnalité CBT pour une machine virtuelle de réplication est réinitialisée ou si le fichier CBT est endommagé.

Cette erreur peut être résolue des deux manières suivantes :

- Si vous avez choisi l’option « Réparer automatiquement la réplication » en sélectionnant « Oui » lorsque vous avez déclenché la réplication de la machine virtuelle, l’outil tente de la réparer pour vous. Cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez « Réparer la réplication ».
- Si vous n’avez pas choisi « Réparer automatiquement la réplication » ou si l’étape ci-dessus n’a pas fonctionné pour vous, arrêtez la réplication de la machine virtuelle, [réinitialiser la fonctionnalité CBT](https://go.microsoft.com/fwlink/?linkid=2139203) sur la machine virtuelle, puis reconfigurez la réplication.

L’un des problèmes connus susceptibles de provoquer une réinitialisation CBT de machine virtuelle sur VMware vSphere 5.5 est décrit dans [VMware KB 2048201 : Changed Block Tracking is reset after a storage vMotion operation in vSphere 5.x](https://go.microsoft.com/fwlink/?linkid=2138888). Si vous êtes sur VMware vSphere 5.5, veillez à appliquer les mises à jour décrites dans cet article.

Vous pouvez également réinitialiser la fonctionnalité CBT de VMware sur une machine virtuelle à l’aide de VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Une erreur interne s’est produite

Parfois, vous pouvez rencontrer une erreur due à des problèmes dans l’environnement ou l’API VMware. Nous avons identifié la série d’erreurs suivante comme étant des erreurs liées à l’environnement VMware. Ces erreurs ont un format fixe.

_Message d’erreur : Une erreur interne s’est produite. [Message d’erreur]_

Par exemple : Message d’erreur : Une erreur interne s’est produite. [Une configuration d’instantané non valide a été détectée].

La section suivante répertorie certaines des erreurs VMware les plus fréquentes et la façon dont vous pouvez les atténuer.

## <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Message d’erreur : Une erreur interne s’est produite. [Connexion refusée par le serveur]

Le problème est un problème connu de VMware et se produit dans le VDDK 6.7. Vous devez arrêter le service de passerelle en cours d’exécution dans l’appliance Azure Migrate, [télécharger une mise à jour à partir de la base de connaissances VMware](https://go.microsoft.com/fwlink/?linkid=2138889) et redémarrer le service de passerelle.

Procédure d’arrêt du service de passerelle :

1. Appuyez sur Windows+R, ouvrez services.msc. Cliquez sur « Service de passerelle Microsoft Azure », puis arrêtez-le.
2. Vous pouvez également ouvrir l’invite de commandes ou PowerShell pour effectuer l’opération : Net Stop asrgwy. Veillez à patienter jusqu’à ce que vous obteniez le message indiquant que le service n’est plus en cours d’exécution.

Procédure de démarrage du service de passerelle :

1. Appuyez sur Windows+R, ouvrez services.msc. Cliquez avec le bouton droit sur « Service de passerelle Microsoft Azure », puis démarrez-le.
2. Vous pouvez également ouvrir l’invite de commandes ou PowerShell pour effectuer l’opération : Net Start asrgwy.

## <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Message d’erreur : Une erreur interne s’est produite. [« Une configuration d’instantané non valide a été détectée. »]

Si votre machine virtuelle est dotée de plusieurs disques, cette erreur peut se produire si vous supprimez un disque de la machine virtuelle. Pour remédier à ce problème, reportez-vous aux étapes décrites dans [cet article VMware](https://go.microsoft.com/fwlink/?linkid=2138890).

## <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Message d’erreur : Une erreur interne s’est produite. [Suspension de la génération de l’instantané]

Ce problème se produit lorsque la génération d’instantané est suspendue. Lorsque ce problème se produit, vous pouvez voir que la tâche de création d’un instantané s’arrête à 95 % ou 99 %. Pour résoudre ce problème, consultez cette [base de connaissances VMware](https://go.microsoft.com/fwlink/?linkid=2138969).

## <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Message d’erreur : Une erreur interne s’est produite. [Échec de la consolidation des disques sur les machines virtuelles _[Raisons]_ ]

Lorsque nous consolidons les disques à la fin du cycle de réplication, l’opération échoue. Suivez les instructions de la [base de connaissances VMware](https://go.microsoft.com/fwlink/?linkid=2138970) en sélectionnant la _raison_ appropriée pour résoudre le problème.

Les erreurs suivantes se produisent lorsque les opérations liées aux instantanés VMware de création, suppression ou consolidation des disques échouent. Suivez les instructions de la section suivante pour corriger les erreurs :

## <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Message d’erreur : Une erreur interne s’est produite. [Une autre tâche est déjà en cours]

Ce problème se produit lorsque des tâches conflictuelles de la machine virtuelle sont exécutées en arrière-plan, ou lorsqu’une tâche au sein de vCenter Server expire. Suivez la résolution fournie dans la [base de connaissances VMware](https://go.microsoft.com/fwlink/?linkid=2138891) suivante.

## <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Message d’erreur : Une erreur interne s’est produite. [Opération non autorisée dans l’état actuel]

Ce problème survient lorsque les agents de gestion de vCenter Server cessent de fonctionner. Pour résoudre ce problème, reportez-vous à la résolution dans la [base de connaissances VMware](https://go.microsoft.com/fwlink/?linkid=2138971) suivante.

## <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Message d’erreur : Une erreur interne s’est produite. [Taille du disque d’instantané non valide]

Il s’agit d’un problème VMware connu dans lequel la taille du disque indiquée par l’instantané devient zéro. Suivez la résolution indiquée dans la [base de connaissances VMware](https://go.microsoft.com/fwlink/?linkid=2138972).

## <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Message d’erreur : Une erreur interne s’est produite. [L’allocation de mémoire a échoué. Mémoire insuffisante.]

Cela se produit lorsque la mémoire tampon de l’hôte NFC est insuffisante. Pour résoudre ce problème, vous devez déplacer la machine virtuelle (compute vMotion) vers un autre hôte, dont les ressources sont disponibles.

## <a name="next-steps"></a>Étapes suivantes

Continuez la réplication de machine virtuelle et effectuez un [test de migration](https://go.microsoft.com/fwlink/?linkid=2139333).