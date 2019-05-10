---
title: Automatiquement à l’échelle des hôtes de session Windows Virtual Desktop Preview - Azure
description: Décrit comment configurer le script de mise à l’échelle automatique pour les ordinateurs hôtes de session Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 7687abf5fc4af0eea9fa6aa210cfd6734cec2b36
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410583"
---
# <a name="automatically-scale-session-hosts"></a>Mettre automatiquement à l’échelle vos hôtes de session

Pour de nombreux déploiements de Windows Virtual Desktop Preview dans Azure, les coûts de la machine virtuelle représentent une partie significative du coût de déploiement de bureau virtuel Windows total. Pour réduire les coûts, il est préférable d’arrêter et désallouer session héberger des ordinateurs virtuels (VM) pendant les heures creuses, puis redémarrez-les pendant les heures de pointe.

Cet article utilise un script de mise à l’échelle simple à l’échelle automatiquement session héberger des ordinateurs virtuels dans votre environnement de bureau virtuel Windows. Pour en savoir plus sur le fonctionnement du script de mise à l’échelle, consultez le [comment fonctionne le script de mise à l’échelle](#how-the-scaling-script-works) section.

## <a name="prerequisites"></a>Conditions préalables

L’environnement dans lequel vous exécutez le script doit disposer des éléments suivants :

- Un client de bureau virtuel Windows et compte ou un principal de service avec des autorisations pour interroger ce client (par exemple, contributeur de services Bureau à distance).
- Machines virtuelles du pool session hôte configuré et inscrit auprès du service Bureau virtuel Windows.
- Une machine virtuelle supplémentaire qui exécute la tâche planifiée par le biais du Planificateur de tâches et dispose d’un accès réseau aux hôtes de session. Ce sera fait référence à plus tard dans le document en tant que machine virtuelle de scaler.
- Le [module Microsoft Azure Resource Manager PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) installé sur la machine virtuelle exécutant la tâche planifiée.
- Le [module PowerShell de bureau virtuel Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) installé sur la machine virtuelle exécutant la tâche planifiée.

## <a name="recommendations-and-limitations"></a>Recommandations et limitations

Lorsque vous exécutez le script de mise à l’échelle, gardez les points suivants à l’esprit :

- Ce script de mise à l’échelle peut uniquement gérer un pool d’hôte par instance de la tâche planifiée qui exécute le script de mise à l’échelle.
- Les tâches planifiées qui exécutent des scripts de mise à l’échelle doivent être sur une machine virtuelle qui est toujours activé.
- Créer un dossier distinct pour chaque instance du script de mise à l’échelle et sa configuration.
- Ce script ne prend pas en charge la connexion en tant qu’administrateur à un bureau virtuel Windows avec des comptes d’utilisateur Azure AD qui nécessitent l’authentification multifacteur. Nous vous recommandons de qu'utiliser des principaux de service pour accéder au service de bureau virtuel Windows et le Azure. Suivez [ce didacticiel](create-service-principal-role-powershell.md) pour créer un principal de service et une attribution de rôle avec PowerShell.
- Garantie de contrat SLA d’Azure s’applique uniquement aux machines virtuelles dans un groupe à haute disponibilité. La version actuelle du document décrit un environnement avec une seule machine virtuelle effectuant la mise à l’échelle, ce qui ne répond pas aux exigences de disponibilité.

## <a name="deploy-the-scaling-script"></a>Déployer le script de mise à l’échelle

Les procédures suivantes vous indiquera comment déployer le script de mise à l’échelle.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Préparer votre environnement pour le script de mise à l’échelle

Tout d’abord, préparez votre environnement pour le script de mise à l’échelle :

1. Connectez-vous à la machine virtuelle (VM scaler) qui exécutera la tâche planifiée avec un compte d’administration de domaine.
2. Créez un dossier sur la machine virtuelle pour stocker le script de mise à l’échelle et sa configuration scaler (par exemple, **C:\\HostPool1 mise à l’échelle**).
3. Téléchargez le **basicScale.ps1**, **Config.xml**, et **PSStoredCredentials.ps1 de fonctions** fichiers et le **PowershellModules** dossier à partir de la [référentiel de scripts de mise à l’échelle](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) et copiez-les dans le dossier que vous avez créé à l’étape 2. Il existe deux méthodes principales pour obtenir les fichiers avant de les copier dans la machine virtuelle scaler :
    - Clonez le référentiel git sur votre ordinateur local.
    - Afficher le **Raw** version de chaque fichier, copiez et collez le contenu de chaque fichier dans un éditeur de texte, puis enregistrez les fichiers avec le type de fichier et le nom de fichier correspondant. 

### <a name="create-securely-stored-credentials"></a>Créer des informations d’identification stockées en toute sécurité

Ensuite, vous devez créer les informations d’identification stockées en toute sécurité :

1. Ouvrez PowerShell ISE en tant qu’administrateur.
2. Importez le module PowerShell de services Bureau à distance en exécutant l’applet de commande suivante :

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Ouvrir le volet d’édition et charger le **PSStoredCredentials.ps1 de fonction** fichier.
4. Exécutez l’applet de commande suivante :
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Par exemple, **Set-Variable - nom KeyPath-étendue globale-valeur « c:\\HostPool1 mise à l’échelle »**
5. Exécutez le **StoredCredential de New - KeyPath \$KeyPath** applet de commande. Lorsque vous y êtes invité, entrez vos informations d’identification de bureau virtuel Windows avec des autorisations pour interroger le pool de l’hôte (le pool de l’hôte est spécifié dans le **config.xml**).
    - Si vous utilisez différents principaux de service ou compte d’utilisateur standard, exécutez le **StoredCredential de New - KeyPath \$KeyPath** applet de commande une fois pour chaque compte créer local les informations d’identification stockées.
6. Exécutez **StoredCredentials de Get-liste** pour confirmer les informations d’identification ont été créées avec succès.

### <a name="configure-the-configxml-file"></a>Configurer le fichier config.xml

Entrez les valeurs appropriées dans les champs suivants pour mettre à jour les paramètres de script de mise à l’échelle dans le fichier config.xml :

| Champ                     | Description                     |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID du locataire AD Azure qui associe l’abonnement dans lequel exécuter des machines virtuelles de l’hôte de session     |
| AADApplicationId              | ID d’application du principal du service                                                       |
| AADServicePrincipalSecret     | Cela peut être entré pendant la phase de test, mais doivent être conservées vide une fois que vous créez des informations d’identification avec **PSStoredCredentials.ps1 de fonctions**    |
| currentAzureSubscriptionId    | L’ID de l’abonnement Azure dans lequel exécuter des machines virtuelles de l’hôte de session                        |
| tenantName                    | Nom de client de bureau virtuel Windows                                                    |
| hostPoolName                  | Nom du pool hôte bureau virtuel Windows                                                 |
| RDBroker                      | URL du service WVD, par défaut la valeur https :\//rdbroker.wvd.microsoft.com             |
| Nom d’utilisateur                      | L’ID d’application du principal du service (il est possible d’avoir le même principal de service comme dans AADApplicationId) ou d’un utilisateur standard sans authentification multifacteur |
| isServicePrincipal            | Valeurs acceptées sont **true** ou **false**. Indique si le deuxième ensemble d’informations d’identification utilisé est un principal de service ou un compte standard. |
| BeginPeakTime                 | Lorsque les heures de pointe commence                                                            |
| EndPeakTime                   | Lorsque les heures de pointe se termine                                                              |
| TimeDifferenceInHours         | Différence de temps entre l’heure locale et l’heure UTC, en heures                                   |
| SessionThresholdPerCPU        | Nombre maximal de sessions par seuil processeur utilisée pour déterminer quand un nouvel hôte de session machine virtuelle doit être démarré pendant les heures de pointe.  |
| MinimumNumberOfRDSH           | Nombre minimal de machines virtuelles à poursuivre son exécution pendant l’heure d’utilisation creuses du pool de hôte             |
| LimitSecondsToForceLogOffUser | Nombre de secondes à attendre avant de forcer les utilisateurs à se déconnecter. Si la valeur 0, les utilisateurs ne sont pas obligée de se déconnecter.  |
| LogOffMessageTitle            | Titre du message envoyé à un utilisateur avant qu’ils êtes obligés de se déconnecter                  |
| LogOffMessageBody             | Corps du message d’avertissement envoyé aux utilisateurs avant qu’ils sont déconnectés. Par exemple, « cet ordinateur s’arrêtera vers le bas dans X minutes. Enregistrez votre travail et vous déconnecter. » |

### <a name="configure-the-task-scheduler"></a>Configurer le Planificateur de tâches

Après avoir configuré le fichier .xml de configuration, vous devez configurer le Planificateur de tâches pour exécuter le fichier basicScaler.ps1 à intervalles réguliers.

1. Démarrer **Planificateur de tâches**.
2. Dans le **Planificateur de tâches** fenêtre, sélectionnez **créer une tâche...**
3. Dans **la tâche créer** boîte de dialogue, sélectionnez le **général** , entrez un **nom** (par exemple, « RDSH dynamique »), sélectionnez **exécuter si l’utilisateur est connecté ou non** et **exécuter avec les autorisations maximales**.
4. Accédez à la **déclencheurs** , puis sélectionnez **New...**
5. Dans le **nouveau déclencheur** boîte de dialogue, sous **paramètres avancés**, vérifiez **répéter la tâche chaque** et sélectionnez la période appropriée et la durée (par exemple, **15 minutes** ou **indéfiniment**).
6. Sélectionnez le **Actions** onglet et **New...**
7. Dans le **nouvelle Action** boîte de dialogue, entrez **powershell.exe** dans le **programme/script** champ, puis entrez **C:\\mise à l’échelle\\ RDSScaler.ps1** dans le **ajouter des arguments (facultatif)** champ.
8. Accédez à la **Conditions** et **paramètres** onglets, puis sélectionnez **OK** pour accepter les paramètres par défaut pour chacun.
9. Entrez le mot de passe du compte d’administration où vous projetez d’exécuter le script de mise à l’échelle.

## <a name="how-the-scaling-script-works"></a>Comment fonctionne le script de mise à l’échelle

Ce script de mise à l’échelle lit les paramètres à partir d’un fichier config.xml, y compris le début et la fin de la période d’utilisation de pointe pendant la journée.

Pendant les heures de pointe, le script vérifie le nombre actuel de sessions et la capacité RDSH en cours d’exécution actuelle pour chaque pool de l’hôte. Elle calcule si l’hôte de session en cours d’exécution des machines virtuelles ait une capacité suffisante pour prendre en charge les sessions existantes en fonction du paramètre SessionThresholdPerCPU défini dans le fichier config.xml. Si ce n’est pas le cas, le script démarre les machines virtuelles hôtes de session supplémentaire dans le pool de l’hôte.

Pendant le délai creuses, le script détermine quel hôte de session des machines virtuelles doit être fermé en fonction du paramètre MinimumNumberOfRDSH dans le fichier config.xml. Le script configurera la session à héberger des machines virtuelles pour vider le mode pour empêcher les nouvelles sessions de connexion aux ordinateurs hôtes. Si vous définissez la **LimitSecondsToForceLogOffUser** paramètre dans le fichier config.xml pour une valeur positive différente de zéro, le script informera les actuellement connecté aux utilisateurs d’enregistrer le travail, attendez le laps de temps configuré et puis forcer la utilisateurs à se déconnecter. Une fois que toutes les sessions utilisateur ont été terminées sur une machine virtuelle d’hôte de session, le script s’arrête le serveur.

Si vous définissez la **LimitSecondsToForceLogOffUser** paramètre dans le fichier config.xml à zéro, le script permettra le paramètre de configuration de session dans l’hôte de propriétés du pool gérer la déconnexion des sessions utilisateur. S’il existe des sessions sur une machine virtuelle d’hôte de session, il laissera la machine virtuelle d’hôte de session en cours d’exécution. S’il ne sont pas toutes les sessions, le script s’arrête à la machine virtuelle d’hôte de session.

Le script est conçu pour s’exécuter périodiquement sur le serveur de machine virtuelle scaler à l’aide du Planificateur de tâches. Sélectionnez l’intervalle de temps appropriée en fonction de la taille de votre environnement de Services Bureau à distance et n’oubliez pas que démarrage et arrêt des machines virtuelles peuvent prendre un certain temps. Nous vous recommandons d’exécuter le script de mise à l’échelle toutes les 15 minutes.

## <a name="log-files"></a>Fichiers journaux

Le script de mise à l’échelle crée deux fichiers journaux, **WVDTenantScale.log** et **WVDTenantUsage.log**. Le **WVDTenantScale.log** fichier enregistrent les événements et les erreurs (le cas échéant lors de chaque exécution du script de mise à l’échelle).

Le **WVDTenantUsage.log** fichier enregistre le nombre réel de cœurs et actif nombre de machines virtuelles chaque fois que vous exécutez le script de mise à l’échelle. Vous pouvez utiliser ces informations pour estimer l’utilisation réelle des machines virtuelles Microsoft Azure et le coût. Le fichier est mis en forme en tant que valeurs séparées par des virgules, avec chaque élément contenant les informations suivantes :

>temps, pool de l’hôte, cœurs, machines virtuelles

Le nom de fichier peut également être modifié pour avoir une extension .csv, chargé dans Microsoft Excel et analysées.
