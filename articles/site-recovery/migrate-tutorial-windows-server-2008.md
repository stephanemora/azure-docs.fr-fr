---
title: Migrer des serveurs Windows Server 2008 locaux vers Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment migrer des machines Windows Server 2008 locales vers Azure à l’aide d’Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/23/2018
ms.author: bsiva
ms.openlocfilehash: 552a0d131f630db7b3a73293d330377ee350d2a9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214616"
---
# <a name="migrate-servers-running-windows-server-2008-2008-r2-to-azure"></a>Migrer des serveurs exécutant Windows Server 2008 et 2008 R2 vers Azure

Ce didacticiel montre comment migrer des serveurs locaux exécutant Windows Server 2008 ou 2008 R2 vers Azure à l’aide d’Azure Site Recovery. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer votre environnement local pour la migration
> * Configurer l’environnement cible
> * Configurer une stratégie de réplication
> * Activer la réplication
> * Exécutez un test de migration afin de vérifier que tout fonctionne bien.
> * Basculer vers Azure et effectuer la migration

La section relative aux limitations et aux problèmes connus répertorie certaines limitations et solutions de contournement de problèmes connus que vous pouvez rencontrer lors de la migration de machines Windows Server 2008 vers Azure. 


## <a name="supported-operating-systems-and-environments"></a>Systèmes d’exploitation et environnements pris en charge


|Système d’exploitation  | Environnement local  |
|---------|---------|
|Windows Server 2008 SP2 - 32 bits et 64 bits (IA-32 et cx86-64)</br>- Standard</br>- Entreprise</br>- Centre de données   |     Machines virtuelles VMware, machines virtuelles Hyper-V et serveurs physiques    |
|Windows Server 2008 R2 SP1 - 64 bits</br>- Standard</br>- Entreprise</br>- Centre de données     |     Machines virtuelles VMware, machines virtuelles Hyper-V et serveurs physiques|

> [!WARNING]
> - La migration de serveurs exécutant Server Core n’est pas prise en charge.
> - Avant la migration, assurez-vous que le dernier Service Pack et les dernières mises à jour de Windows sont installés.


## <a name="prerequisites"></a>Prérequis

Avant de commencer, il est utile d’examiner l’architecture d’Azure Site Recovery pour la [migration de VMware et de serveur physique](vmware-azure-architecture.md) ou la [migration de machine virtuelle Hyper-V](hyper-v-azure-architecture.md). 

Pour migrer des machines virtuelles Hyper-V exécutant Windows Server 2008 ou 2008 R2, suivez les étapes décrites dans le didacticiel [Migrer des machines locales vers Azure](migrate-tutorial-on-premises-azure.md).

Le reste de ce didacticiel montre comment migrer des machines virtuelles VMware locales et des serveurs physiques exécutant Windows Server 2008 ou 2008 R2.


## <a name="limitations-and-known-issues"></a>Limitations et problèmes connus

- Le serveur de configuration, les serveurs de processus supplémentaires et le service Mobilité utilisé pour migrer les serveurs Windows Server 2008 SP2 doivent exécuter la version 9.18.0.1 du logiciel Azure Site Recovery. Le programme d’installation unifié pour la version 9.18.0.1 du serveur de configuration et du serveur de processus peut être téléchargé à partir de [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup).

- Un serveur de configuration ou de processus existant ne peut pas être utilisé pour migrer des serveurs exécutant Windows Server 2008 SP2. Un nouveau serveur de configuration doit être approvisionné avec la version 9.18.0.1 du logiciel Azure Site Recovery. Ce serveur de configuration doit être utilisé uniquement pour la migration de serveurs Windows vers Azure.

- Les points de récupération cohérents d’application et la fonctionnalité de cohérence multimachine virtuelle ne sont pas pris en charge pour la réplication des serveurs exécutant Windows Server 2008 SP2. Les serveurs Windows Server 2008 SP2 doivent être migrés vers un point de récupération cohérent d’incident. Par défaut, des points de récupération cohérents d’incident sont générés toutes les 5 minutes. L’utilisation d’une stratégie de réplication avec une fréquence de capture instantanée de cohérence des applications configurée a pour effet que l’intégrité de la réplication devient critique à défaut de point de récupération cohérent d’application. Pour éviter les faux positifs, définissez la fréquence de capture instantanée de cohérence des applications dans la stratégie de réplication sur « Désactivé ».

- Pour que le service Mobilité fonctionne, .NET Framework 3.5 Service Pack 1 doit être installé sur les serveurs migrés.


- Si votre serveur dispose de disques dynamiques, vous remarquerez peut-être que, dans certaines configurations, ces disques sur le serveur basculé sont marqués hors connexion ou affichés en tant que disques étrangers. Vous pourriez également remarquer que l’état de mise en miroir défini pour les volumes en miroir des disques dynamiques est marqué « Échec de la redondance ». Vous pouvez résoudre ce problème à partir de diskmgmt.msc en important ces disques et en les réactivant manuellement.

- Les serveurs migrés doivent avoir le pilote vmstorfl.sys. Le basculement peut échouer si le pilote n’est pas présent sur le serveur migré. 
  > [!TIP]
  >Vérifiez si le pilote est présent dans « C:\Windows\system32\drivers\vmstorfl.sys ». Si le pilote est introuvable, vous pouvez contourner ce problème en créant un fichier factice. 
  >
  > Ouvrez l’invite de commandes (run > cmd), puis exécutez la commande suivante : « copy nul c:\Windows\system32\drivers\vmstorfl.sys »

- Il se peut que vous ne puissiez pas utiliser le protocole RDP sur des serveurs Windows Server 2008 SP2 exécutant le système d’exploitation 32 bits immédiatement après leur basculement ou leur test de basculement vers Azure. Redémarrez la machine virtuelle basculée à partir du portail Azure, puis réessayez de vous connecter. Si vous ne parvenez pas à vous connecter, vérifiez si le serveur est configuré pour autoriser les connexions Bureau à distance, et assurez-vous qu’il n’y pas de règle de pare-feu ou de groupe de sécurité réseau bloquant la connexion. 
  > [!TIP]
  > Un test de basculement est fortement recommandé avant de migrer les serveurs. Assurez-vous d’avoir effectué au moins un test de basculement réussi sur chaque serveur que vous migrez. Dans le cadre du test de basculement, connectez-vous à la machine de test et vérifiez que tout fonctionne comme prévu.
  >
  >L’opération de test de basculement est non perturbateur et vous aide à tester les migrations en créant des machines virtuelles dans un réseau isolé de votre choix. Contrairement à l’opération de basculement, pendant l’opération de test de basculement, la réplication des données continue à progresser. Vous pouvez effectuer autant de tests de basculement que vous le souhaitez pour préparer la migration. 
  >
  >


## <a name="getting-started"></a>Prise en main

Pour préparer l’abonnement Azure et l’environnement physique/VMware local, effectuez les tâches suivantes :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. Préparer les machines virtuelles [VMware](vmware-azure-tutorial-prepare-on-premises.md) locales


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Cliquez sur **Créer une ressource** > **Surveillance + Gestion** > **Backup and Site Recovery**.
3. Dans **Nom**, indiquez le nom convivial **W2K8-migration**. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources **w2k8migrate**.
5. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Pour accéder rapidement au coffre à partir du tableau de bord, cliquez sur **Épingler au tableau de bord**, puis sur **Créer**.

   ![Nouveau coffre](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

Le nouveau coffre est ajouté à la zone **Tableau de bord** dans **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Préparer votre environnement local pour la migration

- Télécharger le programme d’installation du serveur de configuration (installation unifiée) à partir de [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup)
- Suivez les étapes décrites ci-dessous pour configurer l’environnement source à l’aide du fichier du programme d’installation téléchargé à l’étape précédente.

> [!IMPORTANT]
> - Veillez à utiliser le fichier d’installation téléchargé à la première étape ci-dessus pour installer et inscrire le serveur de configuration. Ne téléchargez pas le fichier d’installation à partir du portail Azure. Le fichier d’installation disponible à l’adresse [https://aka.ms/asr-w2k8-migration-setup](https://aka.ms/asr-w2k8-migration-setup) est la seule version qui prenne en charge la migration de Windows Server 2008.
>
> - Vous ne pouvez pas utiliser un serveur de configuration existant pour migrer des machines exécutant Windows Server 2008. Vous devez configurer un nouveau serveur de configuration en suivant le lien fourni ci-dessus.
>
> - Suivez les étapes indiquées ci-dessous pour installer le serveur de configuration. N’essayez pas d’utiliser la procédure d’installation basée sur l’interface graphique utilisateur en exécutant l’installation unifiée directement. Cela entraînerait l’échec de la tentative d’installation avec une erreur incorrecte indiquant qu’il n’existe aucune connectivité Internet.

 
1) Téléchargez le fichier d’informations d’identification du coffre à partir du portail : dans le portail Azure, sélectionnez le coffre Recovery Services créé à l’étape précédente. Dans le menu de la page du coffre, sélectionnez **Infrastructure Site Recovery** > **Serveurs de configuration**. Cliquez ensuite sur **+Serveur**. Sélectionnez *Configuration Server for Physical* (Serveur de configuration pour serveur physique) dans le formulaire de type liste déroulante de la page qui s’ouvre. Cliquez sur le bouton Télécharger à l’étape 4 pour télécharger le fichier d’informations d’identification du coffre.

 ![Télécharger la clé d’inscription du coffre](media/migrate-tutorial-windows-server-2008/download-vault-credentials.png) 

2) Copiez le fichier d’informations d’identification du coffre téléchargé à l’étape précédente et le fichier d’installation unifiée téléchargé précédemment sur le Bureau de la machine du serveur de configuration (ordinateur Windows Server 2012 R2 ou Windows Server 2016 sur lequel vous souhaitez installer le logiciel du serveur de configuration).

3) Vérifiez que le serveur de configuration a une connectivité Internet et que l’horloge système ainsi que les paramètres de fuseau horaire sur l’ordinateur sont correctement configurés. Téléchargez le programme d’installation [MySQL 5.7](https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi) et placez-le dans *C:\Temp\ASRSetup* (créez le répertoire s’il n’existe pas). 

4) Créez un fichier d’informations d’identification MySQL avec les lignes suivantes et placez-le sur le Bureau dans **C:\Users\Administrator\MySQLCreds.txt**. Remplacez « Password~1 » ci-dessous par un mot de passe fort et approprié :

```
[MySQLCredentials]
MySQLRootPassword = "Password~1"
MySQLUserPassword = "Password~1"
```

5) Extrayez le contenu du fichier d’installation unifiée téléchargé sur le Bureau en exécutant la commande suivante :

```
cd C:\Users\Administrator\Desktop

MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Users\Administrator\Desktop\9.18
```
  
6) Installez le logiciel du serveur de configuration à l’aide du contenu extrait en exécutant les commandes suivantes :

```
cd C:\Users\Administrator\Desktop\9.18.1

UnifiedSetup.exe /AcceptThirdpartyEULA /ServerMode CS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /MySQLCredsFilePath "C:\Users\Administrator\Desktop\MySQLCreds.txt" /VaultCredsFilePath <vault credentials file path> /EnvType VMWare /SkipSpaceCheck
```

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez le modèle de déploiement Resource Manager.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.


## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

1. Pour créer une nouvelle stratégie de réplication, cliquez sur **Infrastructure de Site Recovery** > **Stratégies de réplication** > **+Stratégie de réplication**.
2. Dans **Créer une stratégie de réplication**, indiquez le nom de la stratégie.
3. Dans **Seuil d’objectif de point de récupération**, spécifiez la limite de l’objectif de point de récupération (RPO). Une alerte est générée si l’objectif de point de récupération dépasse cette limite.
4. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre. Les machines virtuelles répliquées vers Premium Storage peuvent prendre en charge jusqu’à 24 heures de rétention et 72 heures en cas de stockage standard.
5. Dans **Fréquence de capture instantanée de cohérence des applications**, spécifiez **Désactivé**. Cliquez sur **OK** pour créer la stratégie.

La stratégie est automatiquement associée au serveur de configuration.

> [!WARNING]
> Veillez à spécifier **Désactivé** pour le paramètre de fréquence de capture instantanée de cohérence des applications de la stratégie de réplication. Seuls les points de récupération cohérents d’incident sont pris en charge lors de la réplication de serveurs exécutant Windows Server 2008. La spécification de toute autre valeur pour la fréquence de capture instantanée de cohérence des applications entraîne de fausses alertes en rendant le contrôle d’intégrité de la réplication du serveur critique à défaut de point de récupération cohérent d’application.

   ![Créer une stratégie de réplication](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Activer la réplication

[Activez la réplication](physical-azure-disaster-recovery.md#enable-replication) pour le serveur Windows Server 2008 SP2 ou 2008 R2 SP1 à migrer.
   
   ![Ajouter un serveur physique](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Activer la réplication](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Exécuter un test de migration

Vous pouvez effectuer un test de basculement des serveurs de réplication une fois que la réplication initiale est terminée et que le serveur est dans l’état **Protégé**.

Exécutez un [test de basculement](tutorial-dr-drill-azure.md) vers Azure afin de vérifier que tout fonctionne bien.

   ![Test de basculement](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrer vers Azure

Exécutez un basculement pour les machines que vous souhaitez migrer.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine > **Basculement**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le dernier point de récupération.
3. Sélectionnez **Arrêter la machine avant de commencer le basculement**. Site Recovery tente d’arrêter le serveur avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
4. Vérifiez que la machine virtuelle Azure s’affiche dans Azure comme prévu.
5. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Terminer la migration**. Cette opération termine le processus de migration, interrompt la réplication pour la machine virtuelle et arrête la facturation Site Recovery pour la machine virtuelle.

   ![Terminer la migration](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **N’annulez pas un basculement en cours** : la réplication de la machine virtuelle est arrêtée avant que le basculement démarre. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.
