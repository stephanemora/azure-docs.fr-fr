---
title: Mise à l’échelle dynamique des hôtes de session Windows Virtual Desktop - Azure
description: Explique comment configurer le script de mise à l’échelle automatique pour les hôtes de session Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: a991a41466d216b9f245c20dbd8054f3ae5ef3d0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451336"
---
# <a name="scale-session-hosts-dynamically"></a>Mettre à l’échelle dynamiquement des hôtes de session

Pour de nombreux déploiements de Windows Virtual Desktop dans Azure, les coûts liés aux machines virtuelles représentent une part significative du coût total de déploiement de Windows Virtual Desktop. Pour réduire les coûts, il est préférable d’arrêter et de libérer les machines virtuelles hôtes de la session pendant les heures d’utilisation creuses, puis de les redémarrer pendant les heures de pointe.

Cet article utilise un script de mise à l’échelle simple pour mettre automatiquement à l’échelle les machines virtuelles de l’hôte de la session dans votre environnement Windows Virtual Desktop. Pour en savoir plus sur le fonctionnement du script de mise à l’échelle, consultez la section [How the scaling script works](#how-the-scaling-script-works) (Fonctionnement du script de mise à l’échelle).

## <a name="prerequisites"></a>Conditions préalables requises

L’environnement dans lequel vous exécutez le script doit disposer des éléments suivants :

- Un locataire, un compte ou un principal de service Windows Virtual Desktop avec des autorisations pour interroger ce locataire (par exemple, Contributeur des services Bureau à distance).
- Machines virtuelles du pool d’hôtes de la session configurées et inscrites avec le service Windows Virtual Desktop.
- Une machine virtuelle supplémentaire qui exécute la tâche planifiée par le biais du Planificateur de tâches et qui dispose d’un accès réseau aux hôtes de session. Elle sera appelée machine virtuelle scaler ultérieurement dans ce document.
- Le [module Microsoft Azure Resource Manager PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) installé sur la machine virtuelle exécutant la tâche planifiée.
- Le [module Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) installé sur la machine virtuelle exécutant la tâche planifiée.

## <a name="recommendations-and-limitations"></a>Recommandations et restrictions

Lorsque vous exécutez le script de mise à l’échelle, gardez les points suivants à l’esprit :

- Ce script de mise à l’échelle peut uniquement gérer un pool d’hôtes par instance de la tâche planifiée qui exécute le script de mise à l’échelle.
- Les tâches planifiées qui exécutent les scripts de mise à l’échelle doivent se faire sur une machine virtuelle toujours activée.
- Créez un dossier distinct pour chaque instance du script de mise à l’échelle et sa configuration.
- Ce script ne prend pas en charge la connexion à Windows Virtual Desktop en tant qu’administrateur avec des comptes d’utilisateur Azure AD qui exigent une authentification multifacteur. Nous vous recommandons d’utiliser des principaux de service pour accéder au service Windows Virtual Desktop et à Azure. Suivez [ce tutoriel](create-service-principal-role-powershell.md) pour créer un principal de service et une attribution de rôle avec PowerShell.
- La garantie du contrat de niveau de service d’Azure s’applique uniquement aux machines virtuelles dans un groupe à haute disponibilité. La version actuelle du document décrit un environnement avec une seule machine virtuelle effectuant la mise à l’échelle, qui peut ne pas répondre aux exigences de disponibilité.

## <a name="deploy-the-scaling-script"></a>Déployer le script de mise à l’échelle

Les procédures suivantes vous expliqueront comment déployer le script de mise à l’échelle.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Préparation de votre environnement pour le script de mise à l’échelle

Tout d’abord, préparez votre environnement pour le script de mise à l’échelle :

1. Connectez-vous à la machine virtuelle (machine virtuelle scaler) qui exécutera la tâche planifiée avec un compte d’administration de domaine.
2. Créez un dossier sur la machine virtuelle scaler pour stocker le script de mise à l’échelle et sa configuration (par exemple, **C:\\scaling-HostPool1**).
3. Téléchargez les fichiers **basicScale.ps1**, **Config.json** et **Functions-PSStoredCredentials.ps1**, ainsi que le dossier **PowershellModules** dans le [référentiel du script de mise à l’échelle](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script), puis copiez-les dans le dossier que vous avez créé à l’étape 2. Il existe deux méthodes principales pour obtenir les fichiers avant de les copier dans la machine virtuelle scaler :
    - Clonez le référentiel Git sur votre ordinateur local.
    - Affichez la version **Raw** de chaque fichier, et copiez et collez le contenu de chaque fichier dans un éditeur de texte. Enregistrez les fichiers avec le nom de fichier et le type de fichier correspondants. 

### <a name="create-securely-stored-credentials"></a>Créer des informations d’identification stockées en toute sécurité

Ensuite, vous devez créer des informations d’identification stockées en toute sécurité :

1. Démarrez PowerShell ISE en tant qu’administrateur.
2. Importez le module PowerShell des services Bureau à distance en exécutant la cmdlet suivante :

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Ouvrez le volet d’édition et chargez le fichier **Function-PSStoredCredentials.ps1**, puis exécutez l’intégralité du script (F5).
4. Exécutez l’applet de commande suivante :
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Par exemple, **Set-Variable -Name KeyPath -Scope Global -Value "c:\\scaling-HostPool1"**
5. Exécutez la cmdlet **New-StoredCredential -KeyPath \$KeyPath**. Lorsque vous y êtes invité, entrez vos informations d’identification Windows Virtual Desktop avec des autorisations pour interroger le pool d’hôtes (le pool d’hôtes est spécifié dans le fichier **config.json**).
    - Si vous utilisez différents principaux de service ou un compte standard, exécutez la cmdlet **New-StoredCredential -KeyPath \$KeyPath** une fois pour chaque compte pour créer des informations d’identification stockées localement.
6. Exécutez **Get-StoredCredential -List** pour confirmer que les informations d’identification ont bien été créées.

### <a name="configure-the-configjson-file"></a>Configurer le fichier config.json

Entrez les valeurs appropriées dans les champs suivants pour mettre à jour les paramètres du script de mise à l’échelle dans le fichier config.json :

| Champ                     | Description                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID du locataire AD Azure qui associe l’abonnement dans lequel s’exécutent les machines virtuelles hôtes de la session     |
| AADApplicationId              | ID d’application du principal de service                                                       |
| AADServicePrincipalSecret     | Il peut être entré durant la phase de test, mais il doit rester vide une fois que vous avez créé des informations d’identification avec le fichier **Functions-PSStoredCredentials.ps1**    |
| currentAzureSubscriptionId    | ID de l’abonnement Azure dans lequel s’exécutent les machines virtuelles hôtes de la session                        |
| tenantName                    | Nom du locataire Windows Virtual Desktop                                                    |
| hostPoolName                  | Nom du pool d’hôtes Windows Virtual Desktop                                                 |
| RDBroker                      | URL du service WVD, valeur par défaut https:\//rdbroker.wvd.microsoft.com             |
| Nom d’utilisateur                      | ID d’application du principal du service (il est possible d’avoir le même principal de service que dans AADApplicationId) ou de l’utilisateur standard sans authentification multifacteur |
| isServicePrincipal            | Les valeurs acceptées sont **true** ou **false**. Indique si le deuxième ensemble d’informations d’identification utilisé est un principal de service ou un compte standard. |
| BeginPeakTime                 | Début des heures de pointe                                                            |
| EndPeakTime                   | Fin des heures de pointe                                                              |
| TimeDifferenceInHours         | Décalage horaire entre l’heure locale et l’heure UTC, en heures                                   |
| SessionThresholdPerCPU        | Nombre maximal de sessions utilisé par seuil de processeur pour déterminer lorsqu’une nouvelle machine virtuelle d’hôte de session doit être démarrée durant les heures de pointe.  |
| MinimumNumberOfRDSH           | Nombre minimal de machines virtuelles du pool d’hôtes à maintenir en cours d’exécution durant les heures creuses             |
| LimitSecondsToForceLogOffUser | Nombre de secondes à attendre avant de forcer les utilisateurs à se déconnecter. Si la valeur est définie sur 0, les utilisateurs ne sont pas obligés de se déconnecter.  |
| LogOffMessageTitle            | Titre du message envoyé à un utilisateur avant qu’il ne soit obligé de se déconnecter                  |
| LogOffMessageBody             | Corps du message d’avertissement envoyé aux utilisateurs avant qu’ils ne soient déconnectés. Par exemple, « Cet ordinateur s’arrêtera dans X minutes. Veuillez enregistrer votre travail et vous déconnecter. » |

### <a name="configure-the-task-scheduler"></a>Configurer le Planificateur de tâches

Après avoir configuré le fichier JSON de configuration, vous devez configurer le Planificateur de tâches pour exécuter le fichier basicScaler.ps1 à intervalles réguliers.

1. Démarrez le **Planificateur de tâche**.
2. Dans la fenêtre **Planificateur de tâches**, sélectionnez **Créer une tâche...**
3. Dans la boîte de dialogue **Créer une tâche**, sélectionnez l’onglet **Général**, entrez un **Nom** (par exemple, « RDSH dynamique »), sélectionnez **Exécuter même si l’utilisateur n’est pas connecté** et **Exécuter avec les privilèges les plus élevés**.
4. Accédez à l’onglet **Déclencheurs**, puis sélectionnez **Nouveau...**
5. Dans la boîte de dialogue **Nouveau déclencheur**, sous **Paramètres avancés**, vérifiez **Répéter chaque tâche** et sélectionnez la période et la durée appropriées (par exemple, **15 minutes** ou **Indéfiniment**).
6. Sélectionnez l’onglet **Actions** et **Nouveau...**
7. Dans la boîte de dialogue **Nouvelle Action**, entrez **powershell.exe** dans le champ **Programme/script**. Ensuite, entrez **C:\\scaling\\basicScale.ps1** dans le champ **Ajouter des arguments (facultatif)** .
8. Accédez aux onglets **Conditions** et **Paramètres**, puis sélectionnez **OK** pour accepter les paramètres par défaut pour chacun.
9. Entrez le mot de passe du compte d’administration dans lequel vous souhaitez exécuter le script de mise à l’échelle.

## <a name="how-the-scaling-script-works"></a>Fonctionnement du script de mise à l’échelle

Ce script de mise à l’échelle lit les paramètres à partir d’un fichier config.json, y compris le début et la fin des heures de pointe pendant la journée.

Durant les heures de pointe, le script vérifie le nombre actuel de sessions et la capacité RDSH actuelle en cours d’exécution pour chaque pool d’hôtes. Elle calcule si les machines virtuelles hôtes de la session en cours d’exécution dispose d’une capacité suffisante pour prendre en charge les sessions existantes en fonction du paramètre SessionThresholdPerCPU défini dans le fichier config.json. Si ce n’est pas le cas, le script démarre des machines virtuelles hôtes de session supplémentaires dans le pool d’hôtes.

Durant les heures creuses, le script détermine quelles machines virtuelles hôtes de la session doivent être arrêtées en fonction du paramètre MinimumNumberOfRDSH dans le fichier config.json. Le script configurera les machines virtuelles hôtes de la session sur le mode de drainage pour empêcher les nouvelles sessions de se connecter aux hôtes. Si vous définissez le paramètre **LimitSecondsToForceLogOffUser** dans le fichier config.json sur une valeur positive différente de zéro, le script enverra une notification aux utilisateurs actuellement connectés leur demandant d’enregistrer leur travail, attendra le laps de temps configuré et puis forcera les utilisateurs à se déconnecter. Une fois que toutes les sessions utilisateur déconnectées sur une machine virtuelle hôte de session, le script arrête le serveur.

Si vous définissez le paramètre **LimitSecondsToForceLogOffUser** dans le fichier config.json sur zéro, le script autorisera le paramètre de configuration de session dans les propriétés du pool d’hôtes à gérer la déconnexion des sessions utilisateur. S’il reste des sessions en cours sur une machine virtuelle hôte de session, il laissera la machine virtuelle hôte de session s’exécuter. S’il ne reste aucune session, le script arrêtera la machine virtuelle hôte de session.

Le script est conçu pour s’exécuter périodiquement sur le serveur de machine virtuelle scaler à l’aide du Planificateur de tâches. Sélectionnez l’intervalle de temps approprié en fonction de la taille de votre environnement Services Bureau à distance et n’oubliez pas que le démarrage et l’arrêt des machines virtuelles peuvent prendre un certain temps. Nous vous recommandons d’exécuter le script de mise à l’échelle toutes les 15 minutes.

## <a name="log-files"></a>Fichiers journaux

Le script de mise à l’échelle crée deux fichiers journaux, **WVDTenantScale.log** et **WVDTenantUsage.log**. Le fichier **WVDTenantScale.log** enregistre les événements et les erreurs (le cas échéant) à chaque exécution du script de mise à l’échelle.

Le fichier **WVDTenantUsage.log** enregistre le nombre réel de cœurs et de machines virtuelles chaque fois que vous exécutez le script de mise à l’échelle. Vous pouvez utiliser ces informations pour estimer l’utilisation réelle des machines virtuelles Microsoft Azure et leur coût. Le fichier est présenté sous la forme de valeurs séparées par des virgules, avec chaque élément contenant les informations suivantes :

>temps, pool d’hôtes, cœurs, machines virtuelles

Le nom de fichier peut également être modifié pour avoir une extension .csv, chargé dans Microsoft Excel, et analysé.
