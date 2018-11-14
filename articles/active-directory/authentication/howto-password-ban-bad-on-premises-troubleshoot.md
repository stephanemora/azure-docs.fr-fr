---
title: Résolution des problèmes et journalisation dans la protection de mot de passe Azure AD (préversion)
description: Comprendre la journalisation et la résolution des problèmes courants dans la protection de mot de passe Azure AD (préversion)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1e5782ce3421cc5f0d2e0e51484d4bbe6b9eb6ab
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978636"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Préversion : surveillance, rapports et résolution des problèmes dans la protection de mot de passe Azure AD

|     |
| --- |
| La protection par mot de passe Azure AD est une fonctionnalité d’évaluation publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Après le déploiement de la protection de mot de passe Azure AD, la surveillance et la génération de rapports sont des tâches essentielles. Cet article détaillé vous aide à comprendre où chaque service journalise les informations et comment créer un rapport sur l’utilisation de la protection de mot de passe Azure AD.

## <a name="on-premises-logs-and-events"></a>Journaux et événements locaux

### <a name="dc-agent-admin-log"></a>Journal d’administration de l’agent de contrôleur de domaine

Sur chaque contrôleur de domaine, le logiciel de service d’agent DC écrit les résultats des validations de mot de passe (et un autre état) dans un journal d’événements local :

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

Les événements sont enregistrés par les différents composants d’agent DC à l’aide des plages suivantes :

|Composant |Plage d’ID d’événement|
| --- | --- |
|DLL de filtre de mot de passe de l’agent DC| 10000-19999|
|Processus d’hébergement du service d’agent DC| 20000-29999|
|Logique de validation de stratégie du service d’agent DC| 30000-39999|

Pour une opération de validation de mot de passe réussie, un événement est généralement consigné à partir de la DLL de filtre de mot de passe de l’agent DC. Pour une opération de validation de mot de passe qui a échoué, deux événements sont généralement consignés, un à partir du service d’agent DC et un à partir de la DLL de filtre de mot de passe de l’agent DC.

Les événements discrets pour capturer ces situations sont enregistrés en fonction des facteurs suivants :

* Si un mot de passe donné est défini ou modifié.
* Indique si la validation de mot de passe a réussi ou échoué.
* Si la validation a échoué en raison de la stratégie globale de Microsoft, et non de la stratégie de l’organisation.
* Si mode d’audit seul est actuellement activé ou désactivé pour la stratégie de mot de passe en vigueur.

Les principaux événements relatifs à la validation de mot de passe sont les suivants :

|   |Modification de mot de passe |Définition de mot de passe|
| --- | :---: | :---: |
|Réussite |10014 |10015|
|Échec (non validé par la stratégie de mot de passe client)| 10016, 30002| 10017, 30003|
|Échec (non validé par la stratégie de mot de passe Microsoft)| 10016, 30004| 10017, 30005|
|Réussite d’audit seul (aurait échoué avec la stratégie de mot de passe client)| 10024, 30008| 10025, 30007|
|Réussite d’audit seul (aurait échoué avec la stratégie de mot de passe Microsoft)| 10024, 30010| 10025, 30009|

> [!TIP]
> Les mots de passe entrants sont d’abord validés par rapport à la liste de mots de passe globale de Microsoft ; en cas d’échec, aucun traitement supplémentaire n’est effectué. Il s’agit du même comportement que celui survenant lors des modifications de mot de passe dans Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Exemple de message de journal des événements pour l’ID d’événement 10014 (mot de passe défini)

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exemple de message de journal des événements pour les ID d’événement 10017 et 30003 (mot de passe non défini)

10017 :

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003 :

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Exemple de message de journal des événements pour l’ID d’événement 30001 (mot de passe accepté, aucune stratégie disponible)

```
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Exemple de message de journal des événements pour l’ID d’événement 30006 (nouvelle stratégie appliquée)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>Journal des opérations de l’agent DC

Le service d’agent DC enregistre également les événements opérationnels dans le journal suivant :

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>Journal de suivi de l’agent DC

Le service d’agent DC peut également enregistrer les événements de suivi au niveau du débogage détaillé dans le journal suivant :

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Le journal de trace est désactivé par défaut.

> [!WARNING]
>  Lorsqu’il est activé, le journal de suivi reçoit un volume élevé d’événements et peut influer sur les performances du contrôleur de domaine. Par conséquent, ce journal amélioré doit uniquement être activé lorsqu’un problème nécessite un examen plus approfondi, puis seulement pendant une courte période.

#### <a name="dc-agent-text-logging"></a>Journalisation de texte de l’agent DC

Le service d’agent DC peut être configuré pour écrire dans un journal de texte en définissant la valeur de Registre suivante :

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Le journal texte est désactivé par défaut. Un redémarrage du service d’agent DC est nécessaire pour que les modifications de cette valeur entrent en vigueur. Lorsqu’il est activé, le service d’agent DC, il écrit dans un fichier journal situé dans :

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Le journal texte reçoit les mêmes entrées de niveau débogage que celles qui peuvent être consignées dans le journal de trace, mais sont format est généralement plus facile à consulter et à analyser.

> [!WARNING]
> Lorsqu’il est activé, ce journal reçoit un volume élevé d’événements et peut influer sur les performances du contrôleur de domaine. Par conséquent, ce journal amélioré doit uniquement être activé lorsqu’un problème nécessite un examen plus approfondi, puis seulement pendant une courte période.

### <a name="azure-ad-password-protection-proxy-service"></a>Service de proxy de protection de mot de passe Azure AD

#### <a name="proxy-service-event-logs"></a>Journaux des événements du service Proxy

Le service Proxy émet un jeu minimal d’événements pour les journaux d’événements suivants :

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

Le service Proxy peut également enregistrer les événements de suivi au niveau du débogage détaillé dans le journal suivant :

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Le journal de trace est désactivé par défaut.

> [!WARNING]
> Lorsqu’il est activé, ce journal de suivi reçoit un volume élevé d’événements et peut influer sur les performances de l’hôte proxy. Par conséquent, ce journal doit uniquement être activé lorsqu’un problème nécessite un examen plus approfondi, puis seulement pendant une courte période.

#### <a name="proxy-service-text-logging"></a>Journalisation de type texte du service Proxy

Le service Proxy peut être configuré pour écrire dans un journal de texte en définissant la valeur de Registre suivante :

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Le journal texte est désactivé par défaut. Un redémarrage du service Proxy est nécessaire pour que les modifications de cette valeur entrent en vigueur. Lorsqu’il est activé, le service Proxy, il écrit dans un fichier journal situé dans :

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Le journal texte reçoit les mêmes entrées de niveau débogage que celles qui peuvent être consignées dans le journal de trace, mais sont format est généralement plus facile à consulter et à analyser.

> [!WARNING]
> Lorsqu’il est activé, ce journal reçoit un volume élevé d’événements et peut influer sur les performances du contrôleur de domaine. Par conséquent, ce journal amélioré doit uniquement être activé lorsqu’un problème nécessite un examen plus approfondi, puis seulement pendant une courte période.

#### <a name="powershell-cmdlet-logging"></a>Journalisation de cmdlet PowerShell

La plupart des cmdlets PowerShell de protection par mot de passe Azure AD écrivent dans un journal de texte qui se trouve dans :

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

En cas d’erreur du cmdlet et si solution et/ou la cause n’est pas évidente, ces journaux texte peuvent servir de référence.

### <a name="emergency-remediation"></a>Correction d’urgence

Si le service d’agent DC cause des problèmes, vous devez l’arrêter immédiatement. La DLL de filtre de mot de passe d’agent DC tente encore d’appeler le service non exécuté et enregistrera des événements d’avertissement (10012, 10013), mais tous les mots de passe entrants sont acceptés pendant ce temps. Le service d’agent DC peut également être configuré via le Gestionnaire de contrôle des services Windows avec un type de démarrage « Désactivé » en fonction des besoins.

### <a name="performance-monitoring"></a>Analyse des performances

Le logiciel de service d’agent DC installe un objet de compteur de performances appelé **protection de mot de passe Azure AD**. Les compteurs de performances suivants sont actuellement disponibles :

|Nom de compteur de performances | Description|
| --- | --- |
|Mots de passe traités |Ce compteur affiche le nombre total de mots de passe traités (acceptés ou rejetés) depuis le dernier redémarrage.|
|Mots de passe acceptés |Ce compteur affiche le nombre total de mots de passe acceptés depuis le dernier redémarrage.|
|Mots de passe rejetés |Ce compteur affiche le nombre total de mots de passe rejetés depuis le dernier redémarrage.|
|Requêtes de filtre de mot de passe en cours |Ce compteur affiche le nombre de requêtes de filtre de mot de passe en cours d’exécution.|
|Pic de requêtes de filtre de mot de passe |Ce compteur affiche le nombre maximal de requêtes de filtre de mot de passe simultanées depuis le dernier redémarrage.|
|Erreurs de requête de filtre de mot de passe |Ce compteur affiche le nombre total de requêtes de filtre de mot de passe qui ont échoué en raison d’une erreur depuis le dernier redémarrage. Des erreurs peuvent se produire lorsque le service d’agent DC de protection de mot de passe Azure AD n’est pas en cours d’exécution.|
|Requêtes de filtre de mot de passe/seconde |Ce compteur affiche la vitesse à laquelle les mots de passe sont traités.|
|Temps de traitement des requêtes de filtre de mot de passe |Ce compteur affiche le temps moyen nécessaire pour traiter une requête de filtre de mot de passe.|
|Temps maximal de traitement des requêtes de filtre de mot de passe |Ce compteur affiche le temps de traitement maximal de requêtes de filtre de mot de passe depuis le dernier redémarrage.|
|Mots de passe acceptés en raison du mode d’audit |Ce compteur affiche le nombre total de mots de passe qui aurait normalement été rejetés, mais qui ont été acceptés, car la stratégie de mot de passe a été configurée en mode d’audit (depuis le dernier redémarrage).|

## <a name="directory-services-repair-mode"></a>Mode de réparation des services d'annuaire

Si le contrôleur de domaine est démarré en mode de réparation des services d’annuaire, le service d’agent DC détecte cette situation comme étant à l’origine de la désactivation de toutes les activités de validation de mot de passe ou de mise en œuvre, quelle que soit la configuration de stratégie active.

## <a name="domain-controller-demotion"></a>Rétrogradation du contrôleur de domaine

Il est possible de rétrograder un contrôleur de domaine exécutant le logiciel de l’agent DC. Les administrateurs doivent toutefois savoir que le logiciel de l’agent DC continue de s’exécuter et d’appliquer la stratégie de mot de passe en vigueur lors de la procédure de rétrogradation. Le nouveau mot de passe du compte administrateur local (spécifié dans le cadre de l’opération de rétrogradation) est validé comme n’importe quel autre mot de passe. Microsoft recommande de choisir des mots de passe sécurisés pour les comptes administrateur locaux dans le cadre d’une procédure de rétrogradation DC. Toutefois, la validation du nouveau mot de passe de compte administrateur local par le logiciel de l’agent DC peut perturber les procédures opérationnelles de rétrogradation pré-existantes.

Une fois que la rétrogradation a réussi et que le contrôleur de domaine a été redémarré et est à nouveau en cours d’exécution en tant que serveur de membre normal, le logiciel de l’agent DC repasse à une exécution en mode passif. Il peut alors être désinstallé à tout moment.

## <a name="removal"></a>Suppression

Si vous décidez de désinstaller le logiciel de la version préliminaire publique et de nettoyer tous les états des domaines et de la forêt, cette tâche peut être réalisée en procédant comme suit :

> [!IMPORTANT]
> Il est important d’effectuer ces étapes dans l’ordre. Si une instance du service Proxy reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint. Si une instance du service d’agent DC reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint et l’état sysvol.

1. Désinstallez le logiciel de proxy de protection de mot de passe sur tous les ordinateurs. Cette étape ne requiert **pas** de redémarrage.
2. Désinstallez le logiciel de l’agent DC sur tous les contrôleurs de domaine. Cette étape **requiert** un redémarrage.
3. Supprimez manuellement tous les points de connexion du service Proxy dans chaque contexte de nommage de domaine. L’emplacement de ces objets peut être détecté avec la commande Powershell Active Directory suivante :

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   N’omettez pas l’astérisque (« * ») à la fin de la valeur de la variable $keywords.

   Les objets trouvés via la commande `Get-ADObject` peuvent ensuite être dirigés vers `Remove-ADObject` ou supprimés manuellement. 

4. Supprimez manuellement tous les points de connexion d’agent DC dans chaque contexte de nommage de domaine. Il peut exister un de ces objets par contrôleur de domaine dans la forêt, selon la portée du déploiement du logiciel de la version préliminaire publique. L’emplacement de cet objet peut être détecté avec la commande Powershell Active Directory suivante :

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Les objets trouvés via la commande `Get-ADObject` peuvent ensuite être dirigés vers `Remove-ADObject` ou supprimés manuellement.

5. Supprimez manuellement l’état de configuration au niveau de la forêt. L’état de configuration de la forêt est conservé dans un conteneur du contexte de nommage de configuration Active Directory. Il peut être détecté et supprimé comme suit :

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Supprimez tous les états sysvol manuellement en supprimant manuellement le dossier suivant et tout son contenu :

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Si nécessaire, ce chemin est aussi accessible localement sur un contrôleur de domaine donné ; l’emplacement par défaut ressemble au chemin d’accès suivant :

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ce chemin d’accès est différent si le partage sysvol a été configuré à un emplacement non défini par défaut.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les listes de mots de passe interdits globales et personnalisées, consultez l’article [Éliminer les mots de passe interdits](concept-password-ban-bad.md).
