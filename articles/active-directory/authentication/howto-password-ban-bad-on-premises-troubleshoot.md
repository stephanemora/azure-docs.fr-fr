---
title: Résolution des problèmes de protection par mot de passe dans Azure AD – Azure Active Directory
description: Comprendre la résolution de problèmes courants de protection par mot de passe Azure AD
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414764"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Résolution de problèmes de protection par mot de passe Azure AD

Après déploiement de la protection par mot de passe Azure AD, il peut s’avérer nécessaire de résoudre des problèmes. Cet article entre dans les détails pour vous aider à comprendre certaines étapes de la résolution des problèmes courants.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>L’agent du contrôleur de domaine ne peut pas localiser un proxy dans l’annuaire

Ce problème se traduit principalement par la présence d’événements 30017 dans le journal des événements d’administration de l’agent du contrôleur de domaine.

La cause habituelle de ce problème est qu’un proxy n’a pas encore été inscrit. Si un proxy a été inscrit, en raison de la latence de réplication d’AD, un certain délai peut s’écouler avant qu’un agent du contrôleur de domaine particulier soit en mesure de voir ce proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>L’agent du contrôleur de domaine ne peut pas communiquer avec un proxy

Ce problème se traduit principalement par la présence d’événements 30018 dans le journal des événements d’administration de l’agent du contrôleur de domaine. Cela peut avoir plusieurs causes :

1. L’agent du contrôleur de domaine se trouve dans une partie isolée du réseau qui n’autorise pas de connectivité réseau au(x) proxy(s) inscrit(s). Ce problème peut donc être attendu\bénin tant que d’autres agents du contrôleur de domaine peuvent communiquer avec le(s) proxy(s) afin de télécharger à partir d’Azure les stratégies de mot de passe qui seront ensuite obtenues par le contrôleur de domaine isolé via une réplication des fichiers de stratégie dans le partage sysvol.

1. L’ordinateur hôte proxy bloque l’accès au point de terminaison du mappeur de point de terminaison RPC (port 135).

   Le programme d’installation du proxy de protection par mot de passe Azure AD crée automatiquement une règle de trafic entrant du Pare-feu Windows, qui autorise l’accès au port 135. En cas de suppression ou de désactivation de cette règle par la suite, les agents du contrôleur de domaine ne peuvent plus communiquer avec le service proxy. Si le Pare-feu Windows intégré a été désactivé à la place d’un autre produit de pare-feu, vous devez configurer ce pare-feu pour autoriser l’accès au port 135.

1. L’ordinateur hôte proxy bloque l’accès au point de terminaison RPC (dynamique ou statique) qu’écoute le service proxy.

   Le programme d’installation du proxy de protection par mot de passe Azure AD crée automatiquement une règle de trafic entrant du Pare-feu Windows, qui autorise l’accès à tout port entrant qu’écoute le service proxy de protection par mot de passe Azure AD. En cas de suppression ou de désactivation de cette règle par la suite, les agents du contrôleur de domaine ne peuvent plus communiquer avec le service proxy. Si le Pare-feu Windows intégré a été désactivé à la place d’un autre produit de pare-feu, vous devez configurer ce pare-feu pour autoriser l’accès à tout port entrant qu’écoute le service proxy de protection par mot de passe Azure AD. Cette configuration peut être rendue plus spécifique si le service proxy a été configuré pour écouter un port RPC statique spécifique (à l’aide de l’applet de commande `Set-AzureADPasswordProtectionProxyConfiguration`).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Le service proxy peut recevoir des appels provenant d’agents du contrôleur de domaine dans le domaine, mais ne peut pas communiquer avec Azure

1. Vérifiez que l’ordinateur proxy dispose d’une connectivité aux points de terminaison répertoriés dans les [conditions requises pour le déploiement](howto-password-ban-bad-on-premises-deploy.md).

1. Assurez-vous que la forêt et tous les serveurs proxy sont inscrits auprès du même locataire Azure.

   Vous pouvez le vérifier en exécutant les applets de commande PowerShell `Get-AzureADPasswordProtectionProxy` et `Get-AzureADPasswordProtectionDCAgent`, puis en comparant la propriété `AzureTenant` de chaque élément retourné. Pour que tout fonctionne correctement, les propriétés doivent être identiques au sein d’une forêt, dans l’ensemble des agents du contrôleur de domaine et des serveurs proxy.

   S’il n’existe pas de condition d’incompatibilité d’inscription du locataire Azure, cela peut être réparé en exécutant les applets de commande PowerShell `Register-AzureADPasswordProtectionProxy` ou `Register-AzureADPasswordProtectionForest` selon le besoin, en veillant à utiliser les informations d’identification du même locataire Azure pour toutes les inscriptions.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>L’agent du contrôleur de domaine ne peut pas chiffrer ou déchiffrer des fichiers de stratégie de mot de passe

Ce problème peut se manifester au travers de divers symptômes, mais a généralement toujours la même cause racine.

La protection par mot de passe Azure AD dépend de manière critique des fonctionnalités de chiffrement et de déchiffrement fournies par le service de distribution de clés Microsoft, disponible sur les contrôleurs de domaine exécutant Windows Server 2012 et versions ultérieures. Le service de distribution de clés doit être activé et opérationnel sur tous les contrôleurs de domaine Windows Server 2012 et versions ultérieures au sein d’un domaine.

Par défaut, le mode de démarrage du service de distribution de clés est Manuel (Déclencher le démarrage). Cette configuration a pour effet que, la première fois qu’un client essaie d’utiliser le service, celui-ci est démarré à la demande. Ce mode de démarrage par défaut du service permet que la protection par mot de passe Azure AD fonctionne.

Si le mode de démarrage du service de distribution de clés est désactivé, cette configuration doit être corrigée pour que la protection par mot de passe Azure AD fonctionne correctement.

Un test simple en lien avec ce problème consiste à démarrer manuellement le service de distribution de clés, soit via la console MMC de management des services, ou à l’aide d’autres outils de gestion des services (par exemple, en tapant la commande « net start kdssvc » dans une console d’invite de commande). Le service de distribution de clés est censé démarrer correctement et continuer à s’exécuter.

La cause racine la plus courante de l’incapacité du service de distribution de clés à démarrer est que l’objet contrôleur de domaine Active Directory se trouve en dehors de l’unité d’organisation Contrôleurs de domaine par défaut. Cette configuration n’est pas pris en charge par le service de distribution de clés et ne constitue pas une limitation imposée par la protection par mot de passe Azure AD. La solution pour corriger cette situation consiste à déplacer l’objet contrôleur de domaine vers un emplacement situé sous l’unité d’organisation Contrôleurs de domaine par défaut.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Les mots de passe faibles sont acceptés alors qu’ils ne devraient pas l’être

Ce problème peut avoir plusieurs causes.

1. Les agents de votre contrôleur de domaine ne peuvent pas télécharger une stratégie ou déchiffrer des stratégies existantes. Recherchez les causes possibles dans les rubriques ci-dessus.

1. Le mode d’application de la stratégie de mot de passe est toujours défini sur Audit. Si c’est le cas, reconfigurez-le pour l’appliquer via le portail de protection par mot de passe Azure AD. Voir [Activer la protection par mot de passe](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. La stratégie de mot de passe a été désactivée. Si c’est le cas, reconfigurez-le pour l’activer via le portail de protection par mot de passe Azure AD. Voir [Activer la protection par mot de passe](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Vous n’avez pas installé le logiciel de l’agent du contrôleur de domaine sur tous les contrôleurs de domaine dans le domaine. Dans ce cas, il est difficile de s’assurer que les clients Windows distants ciblent un contrôleur de domaine particulier pendant une opération de modification de mot de passe. Si vous pensez avoir ciblé avec succès un contrôleur de domaine particulier sur lequel le logiciel de l’agent du contrôleur de domaine est installé, vous pouvez examiner attentivement le journal des événements d’administration de l’agent du contrôleur de domaine. Quel que soit le résultat, il y aura au moins un événement pour expliquer le résultat du processus de validation du mot de passe. En l’absence d’événement en relation avec l’utilisateur dont le mot de passe a changé, la modification du mot de passe a probablement été traitée par un autre contrôleur de domaine.

   Un autre test consiste à essayer de définir ou modifier les mots de passe pendant que vous êtes connecté directement à un contrôleur de domaine sur lequel le logiciel de l’agent du contrôleur de domaine est installé. Cette technique n’est pas recommandée pour les domaines Active Directory de production.

   Bien qu’un déploiement incrémentiel du logiciel de l’agent du contrôleur de domaine soit pris en charge dans le respect de ces limitations, Microsoft recommande vivement d’installer au plus tôt le logiciel de l’agent du contrôleur de domaine sur tous les contrôleurs de domaine au sein d’un domaine.

1. En définitive, l’algorithme de validation de mot de passe fonctionne peut-être comme prévu. Voir [Comment les mots de passe sont évalués](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Mode de réparation des services d'annuaire

Si le contrôleur de domaine est démarré en mode de réparation des services d’annuaire, le service d’agent du contrôleur de domaine le détecte, ce qui a pour effet de désactiver toutes les activités de validation ou d’application du mot de passe, quelle que soit la configuration de la stratégie active.

## <a name="emergency-remediation"></a>Correction d’urgence

Si le service d’agent DC cause des problèmes, vous devez l’arrêter immédiatement. La DLL de filtre de mot de passe d’agent DC tente encore d’appeler le service non exécuté et enregistrera des événements d’avertissement (10012, 10013), mais tous les mots de passe entrants sont acceptés pendant ce temps. Le service d’agent DC peut également être configuré via le Gestionnaire de contrôle des services Windows avec un type de démarrage « Désactivé » en fonction des besoins.

Une autre mesure de correction consisterait à définir le mode d’activation sur Non dans le portail de la protection par mot de passe Azure AD. Une fois la stratégie mise à jour téléchargée, chaque service d’agent du contrôleur de domaine passe en mode inactif où tous les mots de passe sont acceptés tels quels. Pour plus d’informations, voir [Mode d’application](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Rétrogradation du contrôleur de domaine

Il est possible de rétrograder un contrôleur de domaine exécutant le logiciel de l’agent DC. Les administrateurs doivent toutefois savoir que le logiciel de l’agent du contrôleur de domaine continue d’appliquer la stratégie de mot de passe en vigueur lors de la procédure de rétrogradation. Le nouveau mot de passe du compte administrateur local (spécifié dans le cadre de l’opération de rétrogradation) est validé comme n’importe quel autre mot de passe. Microsoft recommande de choisir des mots de passe sécurisés pour les comptes administrateur locaux dans le cadre d’une procédure de rétrogradation DC. Toutefois, la validation du nouveau mot de passe de compte administrateur local par le logiciel de l’agent DC peut perturber les procédures opérationnelles de rétrogradation pré-existantes.

Une fois que la rétrogradation a réussi et que le contrôleur de domaine a été redémarré et est à nouveau en cours d’exécution en tant que serveur de membre normal, le logiciel de l’agent DC repasse à une exécution en mode passif. Il peut alors être désinstallé à tout moment.

## <a name="removal"></a>Suppression

Si vous décidez de désinstaller le logiciel de protection par mot de passe Azure AD et de nettoyer tout état associé des domaines et de la forêt, vous pouvez accomplir cette tâche en procédant comme suit :

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

4. Supprimez manuellement tous les points de connexion d’agent DC dans chaque contexte de nommage de domaine. Il peut exister un de ces objets par contrôleur de domaine dans la forêt, selon la portée du déploiement du logiciel. Vous pouvez détecter l’emplacement de cet objet avec la commande PowerShell Active Directory suivante :

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

[Forum au questions sur la protection par mot de passe Azure AD](howto-password-ban-bad-on-premises-faq.md)

Pour plus d’informations sur les listes de mots de passe interdits globales et personnalisées, consultez l’article [Éliminer les mots de passe interdits](concept-password-ban-bad.md).
