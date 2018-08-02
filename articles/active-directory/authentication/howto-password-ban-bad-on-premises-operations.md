---
title: Opérations en préversion de protection et rapport de mot de passe Azure AD
description: Opérations en préversion de post-déploiement et rapport de protection du mot de passe Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 14aa52b6d424423f4863efa63f3e2e66b84dac70
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163563"
---
# <a name="preview-azure-ad-password-protection-post-deployment"></a>Préversion : post-déploiement de la protection du mot de passe Azure AD

|     |
| --- |
| La protection de mot de passe Azure AD et la liste de mots de passe interdits personnalisée sont des fonctionnalités de la préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Une fois que vous avez terminé l’[installation de la protection de mot de passe Azure AD](howto-password-ban-bad-on-premises.md) locale, quelques éléments doivent être configurés dans le portail Azure.

## <a name="configure-the-custom-banned-password-list"></a>Configurer la liste des mots de passe interdits personnalisée

Suivez les instructions dans l’article [Configurer la liste des mots de passe interdits personnalisée](howto-password-ban-bad.md) pour savoir comment personnaliser la liste des mots de passe interdits pour votre organisation.

## <a name="enable-password-protection"></a>Activer la protection de mot de passe

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à **Azure Active Directory**, **Méthodes d’authentification**, puis **Protection par mot de passe (préversion)**.
1. Réglez **Activer la protection de mot de passe pour Windows Server Active Directory** sur**Oui**
1. Comme mentionné dans le [Guide de déploiement](howto-password-ban-bad-on-premises.md#deployment-strategy), il est recommandé de régler de base le **Mode** sur **Audit**
   * Une fois que vous êtes familiarisé avec la fonctionnalité, vous pouvez basculer le **Mode** sur **Appliqué**
1. Cliquez sur **Enregistrer**.

![Activation des composants de protection de mot de passe Azure AD dans le portail Azure](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Mode Audit

Le mode Audit est conçu comme un moyen d’exécuter le logiciel dans un mode de « simulation ». Chaque service d’agent DC évalue un mot de passe entrant en fonction de la stratégie active. Si la stratégie actuelle est configurée pour être en mode Audit, les « mauvais » mots de passe entraînent l’apparition de messages dans le journal des événements, mais sont acceptés. C’est la seule différence entre le mode d’Audit et le mode Appliquer ; toutes les autres opérations s’exécuteront de la même manière.

> [!NOTE]
> Microsoft recommande que les déploiement et test initiaux soient toujours démarrés en mode Audit. Les événements dans le journal des événements doivent ensuite être surveillés pour tenter d’anticiper si un processus opérationnel existant serait dérangé une fois que le mode Appliquer est activé.

## <a name="enforce-mode"></a>Mode Appliquer

Le mode Appliquer est conçu comme la configuration finale. Comme dans le mode Audit, chaque service d’agent DC évalue les mots de passe entrant en fonction de la stratégie active. Cependant, si le mode Appliquer est activé, un mot de passe qui est considéré comme non sécurisé, en accord avec la stratégie sera rejeté.

Lorsqu’un mot de passe est refusé en mode Appliquer par l’agent DC de la protection de mot de passe Azure AD, l’impact visible vu par un utilisateur final est le même que si leur mot de passe avait été rejeté par l’application sur site traditionnelle de la complexité de mot de passe. Par exemple, un utilisateur peut voir le message d’erreur traditionnel suivant sur l’écran de mot de passe logon\change :

« Impossible de mettre à jour le mot de passe. La valeur fournie pour le nouveau mot de passe ne respecte pas la longueur, la complexité, ou les exigences de l’historique du domaine. »

Ce message n’est qu’un seul exemple de plusieurs résultats possibles. Le message d’erreur spécifique peut varier selon le logiciel réel ou le scénario qui tente de définir un mot de passe non sécurisé.

Les utilisateurs finaux concernés devront peut-être travailler avec leur service informatique pour comprendre les nouvelles exigences et être plus en mesure de choisir des mots de passe sécurisés.

## <a name="usage-reporting"></a>Rapports d’utilisation

La cmdlet `Get-AzureADPasswordProtectionSummaryReport` peut être utilisée pour produire un résumé de l’activité. Voici un exemple de sortie de cette cmdlet :

```
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
> Cette cmdlet fonctionne en interrogeant à distance le journal des événements administrateur de chaque agent de service DC. Si les journaux des événements contiennent un grand nombre d’événements, la cmdlet peut prendre beaucoup de temps à se faire. En outre, les requêtes de réseau en bloc de grands jeux de données peuvent affecter les performances du contrôleur de domaine. Par conséquent, cette cmdlet doit être utilisée avec précaution dans les environnements de production.

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes et journalisation des informations dans la protection de mot de passe Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)
