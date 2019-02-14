---
title: Résolution de problèmes dans la préversion de la protection par mot de passe Azure AD
description: Comprendre la résolution de problèmes courants dans la préversion de la protection par mot de passe Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5727965373752d40e3ce508c1bc79046c2b3b70b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56177749"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Aperçu : Résolution de problèmes de protection par mot de passe Azure AD

|     |
| --- |
| La protection par mot de passe Azure AD est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Après déploiement de la protection par mot de passe Azure AD, il peut s’avérer nécessaire de résoudre des problèmes. Cet article entre dans les détails pour vous aider à comprendre certaines étapes de la résolution des problèmes courants.

## <a name="weak-passwords-are-not-getting-rejected-as-expected"></a>Les mots de passe faibles ne sont pas rejetés comme prévu

Cela peut avoir plusieurs causes :

1. Vos agents du contrôleur de domaine n’ont pas encore téléchargé de stratégie. Cela se traduit par la présence d’événements 30001 dans le journal des événements d’administration de l’agent du contrôleur de domaine.

    Les causes possibles de ce problème sont les suivantes :

    1. La forêt n’est pas encore inscrite.
    2. Le proxy n’est pas encore inscrit.
    3. Des problèmes de connectivité réseau empêchent le service proxy de communiquer avec Azure (vérifier la configuration requise du Proxy HTTP)

2. Le mode d’application de la stratégie de mot de passe est toujours défini sur Audit. Si c’est le cas, reconfigurez-le pour l’appliquer via le portail de protection par mot de passe Azure AD. Veuillez consulter [Activer la protection par mot de passe](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

3. La stratégie de mot de passe a été désactivée. Si c’est le cas, reconfigurer-la pour l’activer via le portail de protection par mot de passe Azure AD. Veuillez consulter [Activer la protection par mot de passe](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

4. L’algorithme de validation de mot de passe ne fonctionne peut-être pas comme prévu. Veuillez consulter [Comment les mots de passe sont-ils évalués](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Mode de réparation des services d'annuaire

Si le contrôleur de domaine est démarré en mode de réparation des services d’annuaire, le service d’agent du contrôleur de domaine le détecte, ce qui a pour effet que de désactiver toutes les activités de validation ou de mise en œuvre de mot de passe, quelle que soit la configuration de stratégie active.

## <a name="emergency-remediation"></a>Correction d’urgence

Si le service d’agent DC cause des problèmes, vous devez l’arrêter immédiatement. La DLL de filtre de mot de passe d’agent DC tente encore d’appeler le service non exécuté et enregistrera des événements d’avertissement (10012, 10013), mais tous les mots de passe entrants sont acceptés pendant ce temps. Le service d’agent DC peut également être configuré via le Gestionnaire de contrôle des services Windows avec un type de démarrage « Désactivé » en fonction des besoins.

Une autre mesure de correction consisterait à définir le mode d’activation sur Non dans le portail de la protection par mot de passe Azure AD. Une fois la stratégie mise à jour téléchargée, chaque service d’agent du contrôleur de domaine passe en mode inactif où tous les mots de passe sont acceptés tels quels. Pour plus d’informations, voir [Mode d’application](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Rétrogradation du contrôleur de domaine

Il est possible de rétrograder un contrôleur de domaine exécutant le logiciel de l’agent DC. Les administrateurs doivent toutefois savoir que le logiciel de l’agent du contrôleur de domaine continue d’appliquer la stratégie de mot de passe en vigueur lors de la procédure de rétrogradation. Le nouveau mot de passe du compte administrateur local (spécifié dans le cadre de l’opération de rétrogradation) est validé comme n’importe quel autre mot de passe. Microsoft recommande de choisir des mots de passe sécurisés pour les comptes administrateur locaux dans le cadre d’une procédure de rétrogradation DC. Toutefois, la validation du nouveau mot de passe de compte administrateur local par le logiciel de l’agent DC peut perturber les procédures opérationnelles de rétrogradation pré-existantes.

Une fois que la rétrogradation a réussi et que le contrôleur de domaine a été redémarré et est à nouveau en cours d’exécution en tant que serveur de membre normal, le logiciel de l’agent DC repasse à une exécution en mode passif. Il peut alors être désinstallé à tout moment.

## <a name="removal"></a>Suppression

Si vous décidez de désinstaller le logiciel de la version préliminaire publique et de nettoyer tous les états des domaines et de la forêt, cette tâche peut être réalisée en procédant comme suit :

> [!IMPORTANT]
> Il est important d’effectuer ces étapes dans l’ordre. Si une instance du service Proxy reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint. Si une instance du service d’agent DC reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint et l’état sysvol.

1. Désinstallez le logiciel de proxy sur tous les ordinateurs. Cette étape ne requiert **pas** de redémarrage.
2. Désinstallez le logiciel de l’agent DC sur tous les contrôleurs de domaine. Cette étape **requiert** un redémarrage.
3. Supprimez manuellement tous les points de connexion du service Proxy dans chaque contexte de nommage de domaine. Vous pouvez détecter l’emplacement de ces objets avec la commande PowerShell Active Directory suivante :

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   N’omettez pas l’astérisque (« * ») à la fin de la valeur de la variable $keywords.

   Les objets trouvés via la commande `Get-ADObject` peuvent ensuite être dirigés vers `Remove-ADObject` ou supprimés manuellement. 

4. Supprimez manuellement tous les points de connexion d’agent DC dans chaque contexte de nommage de domaine. Il peut exister un de ces objets par contrôleur de domaine dans la forêt, selon la portée du déploiement du logiciel de la version préliminaire publique. Vous pouvez détecter l’emplacement de cet objet avec la commande PowerShell Active Directory suivante :

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Les objets trouvés via la commande `Get-ADObject` peuvent ensuite être dirigés vers `Remove-ADObject` ou supprimés manuellement.

   N’omettez pas l’astérisque (« * ») à la fin de la valeur de la variable $keywords.

5. Supprimez manuellement l’état de configuration au niveau de la forêt. L’état de configuration de la forêt est conservé dans un conteneur du contexte de nommage de configuration Active Directory. Il peut être détecté et supprimé comme suit :

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Supprimez tous les états sysvol manuellement en supprimant manuellement le dossier suivant et tout son contenu :

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Si nécessaire, ce chemin est aussi accessible localement sur un contrôleur de domaine donné ; l’emplacement par défaut ressemble au chemin d’accès suivant :

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ce chemin d’accès est différent si le partage sysvol a été configuré à un emplacement non défini par défaut.

## <a name="next-steps"></a>Étapes suivantes

[Forum au questions sur la protection par mot de passe Azure AD](howto-password-ban-bad-on-premises-faq.md)

Pour plus d’informations sur les listes de mots de passe interdits globales et personnalisées, consultez l’article [Éliminer les mots de passe interdits](concept-password-ban-bad.md).
