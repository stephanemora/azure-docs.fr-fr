---
title: Résolution des problèmes de protection de mot de passe Azure AD - Azure Active Directory
description: Comprendre la protection de mot de passe du Azure AD courants dépannage
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
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863172"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Résolution de problèmes de protection par mot de passe Azure AD

Après déploiement de la protection par mot de passe Azure AD, il peut s’avérer nécessaire de résoudre des problèmes. Cet article entre dans les détails pour vous aider à comprendre certaines étapes de la résolution des problèmes courants.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>L’agent du contrôleur de domaine ne peut pas localiser un proxy dans le répertoire

Le symptôme principal de ce problème est 30017 événements dans le journal des événements des administrateur de l’agent de contrôleur de domaine.

La cause habituelle de ce problème est qu’un proxy n’a pas encore été enregistré. Si un proxy a été inscrit, il peut y avoir certains délai en raison de la latence de réplication AD jusqu'à ce qu’un agent contrôleur de domaine particulier est en mesure de voir ce proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>L’agent du contrôleur de domaine n’est pas en mesure de communiquer avec un proxy

Le symptôme principal de ce problème est 30018 événements dans le journal des événements des administrateur de l’agent de contrôleur de domaine. Cela peut avoir plusieurs causes :

1. L’agent du contrôleur de domaine se trouve dans une partie isolée du réseau qui n’autorise pas de connectivité réseau vers le proxy(s) inscrits. Ce problème peut être par conséquent expected\benign tant que les autres agents du contrôleur de domaine peuvent communiquer avec le proxy(s) afin de télécharger des stratégies de mot de passe à partir d’Azure, qui sera ensuite être obtenue par le contrôleur de domaine isolé par le biais de la réplication des fichiers de stratégie dans le partage sysvol.

1. L’ordinateur hôte proxy bloque l’accès au point de terminaison RPC endpoint mapper (port 135)

   Le programme d’installation du Proxy de Protection de mot de passe Azure AD crée automatiquement une règle de trafic entrant de pare-feu de Windows qui autorise l’accès au port 135. Si cette règle est ultérieurement supprimée ou désactivée, agents de contrôleur de domaine ne pourront pas communiquer avec le service de Proxy. Si le pare-feu de Windows intégré a été désactivé à la place d’un autre produit de pare-feu, vous devez configurer ce pare-feu pour autoriser l’accès au port 135.

1. L’ordinateur hôte proxy bloque l’accès au point de terminaison RPC (dynamique ou statique) écoutés par le service de Proxy

   Le programme d’installation du Proxy de Protection de mot de passe Azure AD crée automatiquement un pare-feu Windows règle de trafic entrant qui autorise l’accès à des ports entrants écoutés par le service de Proxy de Protection de mot de passe Azure AD. Si cette règle est ultérieurement supprimée ou désactivée, agents de contrôleur de domaine ne pourront pas communiquer avec le service de Proxy. Si le pare-feu de Windows intégré a été désactivé à la place d’un autre produit de pare-feu, vous devez configurer que le pare-feu pour autoriser l’accès à des ports entrants écoutés par le service de Proxy de Protection de mot de passe Azure AD. Cette configuration peut être rendue plus spécifique si le service de Proxy a été configuré pour écouter sur un port RPC statique (à l’aide de la `Set-AzureADPasswordProtectionProxyConfiguration` applet de commande).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Le service de Proxy peut recevoir des appels provenant des agents de contrôleur de domaine dans le domaine, mais ne peut pas communiquer avec Azure

1. Vérifiez l’ordinateur proxy dispose d’une connectivité aux points de terminaison répertoriés dans le [exigences relatives au déploiement](howto-password-ban-bad-on-premises-deploy.md).

1. Vérifiez que la forêt et le proxy tous les serveurs sont enregistrés dans le même locataire Azure.

   Vous pouvez le vérifier en exécutant la `Get-AzureADPasswordProtectionProxy` et `Get-AzureADPasswordProtectionDCAgent` applets de commande PowerShell, puis comparer les `AzureTenant` propriété de chaque élément retourné. Pour fonctionner correctement ces doivent être identique au sein d’une forêt, dans tous les agents du contrôleur de domaine et serveurs proxy.

   Si une condition d’incompatibilité d’inscription client Azure n’existe pas, cela peut être réparée en exécutant la `Register-AzureADPasswordProtectionProxy` et/ou `Register-AzureADPasswordProtectionForest` applets de commande PowerShell selon les besoins, en veillant à utiliser les informations d’identification à partir du même client Azure pour toutes les inscriptions.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>L’agent du contrôleur de domaine ne peut pas chiffrer ou déchiffrer des fichiers de stratégie de mot de passe et l’autre état

Ce problème peut se manifester avec un large éventail de problèmes, mais a généralement une cause courante de la racine.

Protection de mot de passe AD Azure a une dépendance critique sur les fonctionnalités de chiffrement et déchiffrement fournie par le Service de Distribution de clés de Microsoft, qui est disponible sur les contrôleurs de domaine exécutant Windows Server 2012 et versions ultérieures. Le service KDS doit être activé et opérationnel sur tous les Windows Server 2012 et versions ultérieures contrôleurs de domaine dans un domaine.

Par défaut le KDS mode de démarrage du service service est configuré en tant que manuel (début du déclencheur). Cette configuration signifie que la première fois qu’un client essaie d’utiliser le service, il est démarré à la demande. Ce mode de démarrage du service par défaut est acceptable pour la Protection de mot de passe Azure AD fonctionne.

Si le mode de démarrage du service KDS a été configuré sur désactivé, cette configuration doit être corrigée avant de la Protection de mot de passe Azure AD fonctionnent correctement.

Un test simple pour ce problème consiste à démarrer manuellement le service KDS, soit via la console MMC de gestion de Service, ou à l’aide d’autres outils de gestion de service (par exemple, exécuter « net start opérations » à partir d’une console de l’invite de commandes). Le service KDS est censé démarrer correctement et à rester en cours d’exécution.

La cause racine la plus courante pour le service KDS l’impossibilité de démarrer est que l’objet de contrôleur de domaine Active Directory se trouve en dehors de l’unité d’organisation des contrôleurs de domaine par défaut. Cette configuration n’est pas pris en charge par le service KDS et n’est pas une limitation imposée par la Protection de mot de passe Azure AD. La solution pour cette condition consiste à déplacer l’objet de contrôleur de domaine vers un emplacement sous l’unité d’organisation des contrôleurs de domaine par défaut.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Mots de passe faibles sont acceptées mais ne doivent pas être

Ce problème peut avoir plusieurs causes.

1. Agents de votre contrôleur de domaine ne peut pas télécharger une stratégie ou ne peut pas déchiffrer les stratégies existantes. Recherchez les causes possibles dans les rubriques ci-dessus.

1. Le mode d’application de la stratégie de mot de passe est toujours défini sur Audit. Si cette configuration est en vigueur, le reconfigurer à appliquer à l’aide du portail de la Protection de mot de passe Azure AD. Consultez [activer le mot de passe](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. La stratégie de mot de passe a été désactivée. Si cette configuration est en vigueur, le reconfigurer activée à l’aide du portail de la Protection de mot de passe Azure AD. Consultez [activer le mot de passe](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Vous n’avez pas installé le logiciel de l’agent du contrôleur de domaine sur tous les contrôleurs de domaine dans le domaine. Dans ce cas, il est difficile de s’assurer que les clients Windows distants ciblent un contrôleur de domaine particulier pendant une opération de modification de mot de passe. Si vous avez pensez que vous avez ciblé avec succès un contrôleur de domaine particulier où est installé le logiciel de l’agent du contrôleur de domaine, vous pouvez vérifier en se contente de vérifier le journal des événements DC agent admin : quel que soit le résultat, il y aura au moins un événement pour documenter le résultat du mot de passe validation. S’il n’existe aucun événement présente pour l’utilisateur dont le mot de passe, puis la modification de mot de passe a été probablement traitée par un autre contrôleur de domaine.

   Comme un autre test, essayez les mots de passe setting\changing connecté directement à un contrôleur de domaine dans lequel est installé le logiciel de l’agent du contrôleur de domaine. Cette technique n’est pas recommandée pour les domaines Active Directory de production.

   Déploiement incrémentiel du logiciel agent contrôleur de domaine est pris en charge soumis à ces limitations, Microsoft recommande que le logiciel de l’agent du contrôleur de domaine est installé sur tous les contrôleurs de domaine dans un domaine dès que possible.

1. L’algorithme de validation de mot de passe peut réellement fonctionner comme prévu. Consultez [comment les mots de passe sont évaluées](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Mode de réparation des services d'annuaire

Si le contrôleur de domaine est démarré en Mode de réparation des Services de répertoire, le service de l’agent du contrôleur de domaine détecte cette condition et entraîne tous les validation de mot de passe ou les activités de l’application doit être désactivée, quelle que soit la configuration de stratégie actuellement active.

## <a name="emergency-remediation"></a>Correction d’urgence

Si le service d’agent DC cause des problèmes, vous devez l’arrêter immédiatement. La DLL de filtre de mot de passe d’agent DC tente encore d’appeler le service non exécuté et enregistrera des événements d’avertissement (10012, 10013), mais tous les mots de passe entrants sont acceptés pendant ce temps. Le service d’agent DC peut également être configuré via le Gestionnaire de contrôle des services Windows avec un type de démarrage « Désactivé » en fonction des besoins.

Une autre mesure de correction consisterait à définir le mode d’activation sur Non dans le portail de la protection par mot de passe Azure AD. Une fois la stratégie mise à jour téléchargée, chaque service d’agent du contrôleur de domaine passe en mode inactif où tous les mots de passe sont acceptés tels quels. Pour plus d’informations, voir [Mode d’application](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Rétrogradation du contrôleur de domaine

Il est possible de rétrograder un contrôleur de domaine exécutant le logiciel de l’agent DC. Les administrateurs doivent toutefois savoir que le logiciel de l’agent du contrôleur de domaine continue d’appliquer la stratégie de mot de passe en vigueur lors de la procédure de rétrogradation. Le nouveau mot de passe du compte administrateur local (spécifié dans le cadre de l’opération de rétrogradation) est validé comme n’importe quel autre mot de passe. Microsoft recommande de choisir des mots de passe sécurisés pour les comptes administrateur locaux dans le cadre d’une procédure de rétrogradation DC. Toutefois, la validation du nouveau mot de passe de compte administrateur local par le logiciel de l’agent DC peut perturber les procédures opérationnelles de rétrogradation pré-existantes.

Une fois que la rétrogradation a réussi et que le contrôleur de domaine a été redémarré et est à nouveau en cours d’exécution en tant que serveur de membre normal, le logiciel de l’agent DC repasse à une exécution en mode passif. Il peut alors être désinstallé à tout moment.

## <a name="removal"></a>Suppression

S’il est décidé de désinstaller le logiciel de protection de mot de passe Azure AD et un nettoyage tout état le (s) et la forêt, cette tâche peut être accomplie en procédant comme suit :

> [!IMPORTANT]
> Il est important d’effectuer ces étapes dans l’ordre. Si une instance du service Proxy reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint. Si une instance du service d’agent DC reste en cours d’exécution, elle recrée périodiquement son objet serviceConnectionPoint et l’état sysvol.

1. Désinstallez le logiciel de proxy sur tous les ordinateurs. Cette étape ne requiert **pas** de redémarrage.
2. Désinstallez le logiciel de l’agent DC sur tous les contrôleurs de domaine. Cette étape **requiert** un redémarrage.
3. Supprimez manuellement tous les points de connexion du service Proxy dans chaque contexte de nommage de domaine. Vous pouvez détecter l’emplacement de ces objets avec la commande PowerShell Active Directory suivante :

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   N’omettez pas l’astérisque (« * ») à la fin de la valeur de la variable $keywords.

   Les objets trouvés via la commande `Get-ADObject` peuvent ensuite être dirigés vers `Remove-ADObject` ou supprimés manuellement.

4. Supprimez manuellement tous les points de connexion d’agent DC dans chaque contexte de nommage de domaine. Il existe peut-être un ces objets par le contrôleur de domaine dans la forêt, selon la portée d’application le logiciel a été déployé. Vous pouvez détecter l’emplacement de cet objet avec la commande PowerShell Active Directory suivante :

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Les objets trouvés via la commande `Get-ADObject` peuvent ensuite être dirigés vers `Remove-ADObject` ou supprimés manuellement.

   N’omettez pas l’astérisque (« * ») à la fin de la valeur de la variable $keywords.

5. Supprimez manuellement l’état de configuration au niveau de la forêt. L’état de configuration de la forêt est conservé dans un conteneur du contexte de nommage de configuration Active Directory. Il peut être détecté et supprimé comme suit :

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Supprimez tous les états sysvol manuellement en supprimant manuellement le dossier suivant et tout son contenu :

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Si nécessaire, ce chemin est aussi accessible localement sur un contrôleur de domaine donné ; l’emplacement par défaut ressemble au chemin d’accès suivant :

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Ce chemin d’accès est différent si le partage sysvol a été configuré à un emplacement non défini par défaut.

## <a name="next-steps"></a>Étapes suivantes

[Forum aux questions pour la Protection de mot de passe Azure AD](howto-password-ban-bad-on-premises-faq.md)

Pour plus d’informations sur les listes de mots de passe interdits globales et personnalisées, consultez l’article [Éliminer les mots de passe interdits](concept-password-ban-bad.md).
