---
title: Opérations en préversion de protection et rapport de mot de passe Azure AD
description: Opérations en préversion de post-déploiement et rapport de protection du mot de passe Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 8d7002a014fc6cfab1888a6bc97c0f864de1d99d
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080869"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>Aperçu : Procédures opérationnelles de la protection par mot de passe Azure AD

|     |
| --- |
| La protection par mot de passe Azure AD est une fonctionnalité de préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Une fois que vous avez terminé l’[installation de la protection de mot de passe Azure AD](howto-password-ban-bad-on-premises-deploy.md) locale, quelques éléments doivent être configurés dans le portail Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurer la liste des mots de passe interdits personnalisée

Suivez les instructions dans l’article [Configurer la liste des mots de passe interdits personnalisée](howto-password-ban-bad-configure.md) pour savoir comment personnaliser la liste des mots de passe interdits pour votre organisation.

## <a name="enable-password-protection"></a>Activer la protection de mot de passe

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Azure Active Directory**, **Méthodes d’authentification**, puis **Password protection (Preview)** (Protection par mot de passe (préversion)).
1. Réglez **Activer la protection de mot de passe pour Windows Server Active Directory** sur**Oui**
1. Comme mentionné dans le [Guide de déploiement](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), il est recommandé de régler de base le **Mode** sur **Audit**
   * Une fois que vous êtes familiarisé avec la fonctionnalité, vous pouvez basculer le **Mode** sur **Appliqué**
1. Cliquez sur **Enregistrer**.

![Activation des composants de protection de mot de passe Azure AD dans le portail Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Mode Audit

Le mode Audit est conçu comme un moyen d’exécuter le logiciel dans un mode de « simulation ». Chaque service d’agent DC évalue un mot de passe entrant en fonction de la stratégie active. Si la stratégie actuelle est configurée pour être en mode Audit, les « mauvais » mots de passe entraînent l’apparition de messages dans le journal des événements, mais sont acceptés. C’est la seule différence entre le mode d’Audit et le mode Appliquer ; toutes les autres opérations s’exécuteront de la même manière.

> [!NOTE]
> Microsoft recommande que les déploiement et test initiaux soient toujours démarrés en mode Audit. Les événements dans le journal des événements doivent ensuite être surveillés pour tenter d’anticiper si un processus opérationnel existant serait dérangé une fois que le mode Appliquer est activé.

## <a name="enforce-mode"></a>Mode Appliquer

Le mode Appliquer est conçu comme la configuration finale. Comme dans le mode Audit, chaque service d’agent DC évalue les mots de passe entrant en fonction de la stratégie active. Cependant, si le mode Appliquer est activé, un mot de passe qui est considéré comme non sécurisé, en accord avec la stratégie sera rejeté.

Lorsqu’un mot de passe est refusé en mode Appliquer par l’agent DC de la protection de mot de passe Azure AD, l’impact visible vu par un utilisateur final est le même que si leur mot de passe avait été rejeté par l’application sur site traditionnelle de la complexité de mot de passe. Par exemple, un utilisateur peut voir le message d’erreur traditionnel suivant sur l’écran de mot de passe logon\change Windows :

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Ce message n’est qu’un seul exemple de plusieurs résultats possibles. Le message d’erreur spécifique peut varier selon le logiciel réel ou le scénario qui tente de définir un mot de passe non sécurisé.

Les utilisateurs finaux concernés devront peut-être travailler avec leur service informatique pour comprendre les nouvelles exigences et être plus en mesure de choisir des mots de passe sécurisés.

## <a name="enable-mode"></a>Mode d’activation

Ce paramètre doit normalement être laissé à son état par défaut (Oui). La configuration de ce paramètre sur désactivé (Non) provoquera le passage en mode inactif de tous les agents contrôleurs de domaine déployés avec protection par mot de passe Azure AD : tous les mots de passe sont acceptés en l’état et aucune activité de validation ne sera exécutée (par exemple, même aucun événement d’audit ne sera émis).

## <a name="usage-reporting"></a>Rapports d’utilisation

La cmdlet `Get-AzureADPasswordProtectionSummaryReport` peut être utilisée pour produire un résumé de l’activité. Voici un exemple de sortie de cette cmdlet :

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

L’étendue du rapport de la cmdlet peut être influencée à l’aide d’un des paramètres –Forest, -Domain ou –DomainController. Ne pas spécifier un paramètre équivaut à –Forest.

> [!NOTE]
> Cette cmdlet fonctionne en ouvrant une session PowerShell sur chaque contrôleur de domaine. Pour réussir, le support de la session à distance PowerShell doit être activé sur chaque contrôleur de domaine et le client doit disposer de privilèges suffisants. Pour plus d’informations sur les exigences de session à distance PowerShell, exécutez « Get-Help about_Remote_Troubleshooting» dans une fenêtre PowerShell.

> [!NOTE]
> Cette cmdlet fonctionne en interrogeant à distance le journal des événements administrateur de chaque agent de service DC. Si les journaux des événements contiennent un grand nombre d’événements, la cmdlet peut prendre beaucoup de temps à se faire. En outre, les requêtes de réseau en bloc de grands jeux de données peuvent affecter les performances du contrôleur de domaine. Par conséquent, cette cmdlet doit être utilisée avec précaution dans les environnements de production.

## <a name="dc-agent-discovery"></a>Détection d’agent DC

La cmdlet `Get-AzureADPasswordProtectionDCAgent` peut être utilisée pour afficher des informations de base sur les divers agents DC en cours d’exécution dans un domaine ou une forêt. Ces informations sont extraites des objets serviceConnectionPoint inscrits par les services d’agent DC en cours d’exécution. Voici un exemple de sortie de cette cmdlet :

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Les différentes propriétés sont mises à jour par chaque service de l’agent DC environ toutes les heures. Les données sont toujours soumises à la latence de la réplication Active Directory.

La portée de la requête de la cmdlet peut être influencée à l’aide des paramètres –Forest ou –Domain.

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes et surveillance pour la protection par mot de passe Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
