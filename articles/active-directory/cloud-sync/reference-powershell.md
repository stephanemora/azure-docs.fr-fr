---
title: Module PowerShell AADCloudSyncTools pour la synchronisation cloud Azure AD Connect
description: Cet article explique comment installer l’agent d’approvisionnement Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593178"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Module PowerShell AADCloudSyncTools pour la synchronisation cloud Azure AD Connect

Le module AADCloudSyncTools fournit une panoplie d’outils pratiques pour gérer vos déploiements de synchronisation cloud Azure AD Connect.

## <a name="pre-requisites"></a>Conditions préalables
Les conditions préalables suivantes doivent être remplies :

- Tous les composants requis pour ce module peuvent être installés automatiquement à l’aide de `Install-AADCloudSyncToolsPrerequisites`
- Ce module utilisant une authentification MSAL, le module MSAL.PS doit être installé. Pour vérifier cela, dans une fenêtre PowerShell, exécutez la commande `Get-module MSAL.PS -ListAvailable`. Si le module est correctement installé, vous obtenez une réponse. Vous pouvez utiliser `Install-AADCloudSyncToolsPrerequisites` pour installer la version la plus récente de MSAL.PS.
- Bien que le module Azure AD PowerShell ne soit requis pour aucune fonctionnalité de ce module, il est utile d’en disposer de façon à ce qu’il soit également installé automatiquement à l’aide de la commande `Install-AADCloudSyncToolsPrerequisites`.
- L’installation manuelle de modules à partir de PowerShell requiert l’application du protocole TLS 1.2. Pour vérifier que vous pouvez installer les modules, définissez au préalable les éléments suivants dans la session PowerShell
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Installer le module PowerShell AADCloudSyncTools
Pour installer et utiliser le module AADCloudSyncTools, effectuez les étapes suivantes :

1. Ouvrez Windows PowerShell avec des privilèges d’administrateur.
2. Tapez ou copiez-collez le code suivant : `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Appuyez sur Entrée.
4. Pour vérifier que le module a été importé, entrez ou copiez-collez la commande suivante : `Get-module AADCloudSyncTools`
5. Vous devez maintenant voir des informations sur le module.
6. Ensuite, pour installer les composants requis pour le module AADCloudSyncTools, exécutez la commande suivante : `Install-AADCloudSyncToolsPrerequisites`
7. Lors de la première exécution, le module PoweShellGet est installé s’il n’est pas présent. Pour charger le nouveau module PowershellGet, fermez la fenêtre PowerShell et ouvrez une nouvelle session PowerShell avec des privilèges administratifs. 
8. Importez de nouveau le module conformément à l’étape 3.
9. Exécutez `Install-AADCloudSyncToolsPrerequisites` pour installer les modules MSAL et AzureAD.
11. Toutes les éléments prérequis doivent être correctement installés ![Installer le module](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>Applets de commande AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Utilise le module MSAL.PS pour demander un jeton permettant à l’administrateur Azure AD d’accéder à Microsoft Graph 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exporte et empaquette toutes les données de dépannage dans un fichier compressé, comme suit :
 1. Démarre un suivi détaillé avec Start-AADCloudSyncToolsVerboseLogs.  Vous pouvez trouver ces journaux de suivi dans le dossier C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Collecte un journal des traces pendant 3 minutes.
   Vous pouvez spécifier une durée différente avec -TracingDurationMins ou ignorer le suivi détaillé avec -SkipVerboseTrace.
 3. Arrête le suivi détaillé avec Stop-AADCloudSyncToolsVerboseLogs.
 4. Collecte les journaux de l’Observateur d’événements des dernières 24 heures.
 5. Compresse tous les journaux de l’agent, les journaux détaillés et les journaux de l’Observateur d’événements dans un fichier zip compressé dans le dossier Documents de l’utilisateur. 
 </br>Vous pouvez spécifier un dossier de sortie différent avec -OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Affiche les détails des locataires Azure AD et l’état des variables internes.

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne les informations de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne la planification de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne le schéma de la tâche de synchronisation.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Utilise Graph pour obtenir le schéma de la tâche de synchronisation pour l’ID de tâche de synchronisation fourni et fournit en sortie toutes les étendues du groupe de filtres.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne les paramètres de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Utilise Graph pour obtenir les principaux de service AD2AAD et retourne le statut de la tâche de synchronisation.
Peut également être appelé à l’aide de l’ID de tâche de synchronisation spécifique en tant que paramètre.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Utilise Graph pour obtenir le ou les principaux de service pour AD2AAD et/ou SyncFabric.
Sans paramètres, retourne uniquement le ou les principaux de service AD2AAD.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Vérifie la présence de PowerShellGet v2.2.4.1 ou version ultérieure et des modules Azure AD et MSAL.PS, et installe ces derniers s’ils sont manquants.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Appelle une requête web pour l’URI, la méthode et le corps spécifiés en tant que paramètres

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Utilise Azure AD PowerShell pour supprimer le compte actuel (le cas échéant) et réinitialise l’authentification du compte de synchronisation avec un nouveau compte de synchronisation dans Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Redémarre une synchronisation complète.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Poursuit la synchronisation à partir du filigrane précédent.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Modifie le fichier 'AADConnectProvisioningAgent.exe.config' pour activer le suivi détaillé et redémarre le service AADConnectProvisioningAgent. Vous pouvez utiliser -SkipServiceRestart pour empêcher le redémarrage du service, mais aucune modification de configuration ne prendra effet.  Vous pouvez trouver ces journaux de suivi dans le dossier C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Modifie le fichier 'AADConnectProvisioningAgent.exe.config' pour désactiver le suivi détaillé et redémarre le service AADConnectProvisioningAgent. Vous pouvez utiliser -SkipServiceRestart pour empêcher le redémarrage du service, mais aucune modification de configuration ne prendra effet.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Suspend la synchronisation.

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)

