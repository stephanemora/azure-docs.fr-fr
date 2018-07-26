---
title: Résolution des problèmes et journalisation dans la protection de mot de passe Azure AD (préversion)
description: Comprendre la journalisation et la résolution des problèmes courants dans la protection de mot de passe Azure AD (préversion)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1eea6380d4276644db0c7681f23a4b0c5e79ff09
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187347"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Préversion : surveillance, rapports et résolution des problèmes dans la protection de mot de passe Azure AD

|     |
| --- |
| La protection de mot de passe Azure AD et la liste de mots de passe interdits personnalisée sont des fonctionnalités de la préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez la rubrique [Conditions d'utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Après le déploiement de la protection de mot de passe Azure AD, la surveillance et la génération de rapports sont des tâches essentielles. Cet article détaillé vous aide à comprendre où chaque service journalise les informations et comment créer un rapport sur l’utilisation de la protection de mot de passe Azure AD.

## <a name="on-premises-logs-and-events"></a>Journaux et événements locaux

### <a name="dc-agent-service"></a>Service d’agent DC

Sur chaque contrôleur de domaine, le logiciel de service d’agent DC écrit les résultats des validations de mot de passe (et un autre état) dans un journal d’événements local : \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

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

Le mot de passe modifié pour l’utilisateur spécifié a été validé comme étant conforme à la stratégie de mot de passe Azure en vigueur.

 UserName: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exemple de message de journal des événements pour les ID d’événement 10017 et 30003 (mot de passe non défini)

10017 :

La réinitialisation du mot de passe pour l’utilisateur spécifié a été rejetée, car il n’était pas conforme à la stratégie de mot de passe Azure en vigueur. Consultez le message associé dans le journal des événements pour plus d’informations.

 UserName: BPL_03283841185 FullName:

30003 :

La réinitialisation du mot de passe pour l’utilisateur spécifié a été rejetée, car il correspondait au moins à un des jetons présents dans la liste de mots de passe interdits par le locataire de la stratégie de mot de passe Azure en vigueur.

 UserName: BPL_03283841185 FullName:

Voici d’autres messages clés de journal des événements à connaître :

#### <a name="sample-event-log-message-for-event-id-30001"></a>Exemple de message de journal des événements pour l’ID d’événement 30001

Le mot de passe pour l’utilisateur spécifié a été accepté, car aucune stratégie de mot de passe Azure n’est pas encore disponible.

UserName: <user> FullName: <user>

Cela peut être dû à une ou plusieurs des raisons suivantes : %n

1. La forêt n’a pas encore été inscrite avec Azure.

   Étapes de résolution : un administrateur doit inscrire la forêt à l’aide de la cmdlet Register-AzureADPasswordProtectionForest.

2. Aucun proxy de protection de mot de passe Azure AD n’est encore disponible sur au moins un ordinateur de la forêt actuelle.

   Étapes de résolution : un administrateur doit installer et inscrire un proxy à l’aide de la cmdlet Register-AzureADPasswordProtectionProxy.

3. Ce contrôleur de domaine n’a pas de connectivité réseau pour les instances de proxy de protection de mot de passe Azure AD.

   Étapes de résolution : vérifiez la connectivité réseau à au moins une instance de proxy de protection mot de passe Azure AD.

4. Ce contrôleur de domaine n’a pas de connectivité à d’autres contrôleurs de domaine dans le domaine.

   Étapes de résolution : vérifiez la connectivité de réseau pour le domaine.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Exemple de message de journal des événements pour l’ID d’événement 30006

Le service applique maintenant la stratégie de mot de passe Azure suivante.

 AuditOnly : 1

 Date de la stratégie globale : 2018-05-15T00:00:00.000000000Z

 Date de la stratégie de locataire : 2018-06-10T20:15:24.432457600Z

 Appliquer la stratégie de locataire : 1

#### <a name="dc-agent-log-locations"></a>Emplacements des journaux d’agent DC

Le service d’agent DC enregistre également les événements opérationnels dans le journal suivant : \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

Le service d’agent DC peut enregistrer les événements de trace de débogage en mode verbose dans le journal suivant : \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> Le journal de trace est désactivé par défaut. Lorsqu’il est activé, ce journal reçoit un volume élevé d’événements et peut influer sur les performances du contrôleur de domaine. Par conséquent, ce journal amélioré doit uniquement être activé lorsqu’un problème nécessite un examen plus approfondi, puis seulement pendant une courte période.

### <a name="azure-ad-password-protection-proxy-service"></a>Service de proxy de protection de mot de passe Azure AD

Le service de proxy de protection de mot de passe émet un ensemble minimal d’événements dans le journal suivant : \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

Le service de proxy de protection de mot de passe peut également enregistrer des événements de trace de débogage en mode verbose dans le journal suivant : \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> Le journal de trace est désactivé par défaut. Lorsqu’il est activé, ce journal reçoit un volume élevé d’événements et peut influer sur les performances de l’hôte proxy. Par conséquent, ce journal doit uniquement être activé lorsqu’un problème nécessite un examen plus approfondi, puis seulement pendant une courte période.

### <a name="dc-agent-discovery"></a>Détection d’agent DC

La cmdlet `Get-AzureADPasswordProtectionDCAgent` peut être utilisée pour afficher des informations de base sur les divers agents DC en cours d’exécution dans un domaine ou une forêt. Ces informations sont extraites des objets serviceConnectionPoint inscrits par les services d’agent DC en cours d’exécution. Voici un exemple de sortie de cette cmdlet :

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Les différentes propriétés sont mises à jour par chaque service de l’agent DC environ toutes les heures. Les données sont toujours soumises à la latence de la réplication Active Directory.

La portée de la requête de la cmdlet peut être influencée à l’aide des paramètres –Forest ou –Domain.

### <a name="emergency-remediation"></a>Correction d’urgence

Si le service d’agent DC cause des problèmes, vous devez l’arrêter immédiatement. La DLL de filtre de mot de passe d’agent DC tente d’appeler le service non exécuté et enregistrera des événements d’avertissement (10012, 10013), mais tous les mots de passe entrants sont acceptés pendant ce temps. Le service d’agent DC peut également être configuré via le Gestionnaire de contrôle des services Windows avec un type de démarrage « Désactivé » en fonction des besoins.

### <a name="performance-monitoring"></a>Analyse des performances

Le logiciel de service d’agent DC installe un objet de compteur de performances nommé **protection de mot de passe Azure AD**. Les compteurs de performances suivants sont actuellement disponibles :

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
> Il est important d’effectuer ces étapes dans l’ordre. Si une instance du service proxy de protection de mot de passe reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint et l’état sysvol.

1. Désinstallez le logiciel de proxy de protection de mot de passe sur tous les ordinateurs. Cette étape ne requiert **pas** de redémarrage.
2. Désinstallez le logiciel de l’agent DC sur tous les contrôleurs de domaine. Cette étape **requiert** un redémarrage.
3. Supprimez manuellement tous les points de connexion de service de proxy dans chaque contexte de nommage de domaine. L’emplacement de ces objets peut être détecté avec la commande Powershell Active Directory suivante :
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   N’omettez pas l’astérisque (« * ») à la fin de la valeur de la variable $keywords.

   L’objet trouvé via la commande `Get-ADObject` peut ensuite être dirigé vers `Remove-ADObject` ou supprimé manuellement. 

4. Supprimez manuellement tous les points de connexion d’agent DC dans chaque contexte de nommage de domaine. Il peut exister un de ces objets par contrôleur de domaine dans la forêt, selon la portée du déploiement du logiciel de la version préliminaire publique. L’emplacement de cet objet peut être détecté avec la commande Powershell Active Directory suivante :

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   L’objet trouvé via la commande `Get-ADObject` peut ensuite être dirigé vers `Remove-ADObject` ou supprimé manuellement.

5. Supprimez manuellement l’état de configuration au niveau de la forêt. L’état de configuration de la forêt est conservé dans un conteneur du contexte de nommage de configuration Active Directory. Il peut être détecté et supprimé comme suit :

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Supprimez tous les états sysvol manuellement en supprimant manuellement le dossier suivant et tout son contenu :

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Si nécessaire, ce chemin est aussi accessible localement sur un contrôleur de domaine donné ; l’emplacement par défaut ressemble au chemin d’accès suivant :

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ce chemin d’accès est différent si le partage sysvol a été configuré à un emplacement non défini par défaut.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les listes de mots de passe interdits globales et personnalisées, consultez l’article [Éliminer les mots de passe interdits](concept-password-ban-bad.md).
