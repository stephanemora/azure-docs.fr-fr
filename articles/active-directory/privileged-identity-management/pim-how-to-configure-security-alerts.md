---
title: Configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans PIM | Microsoft Docs
description: Découvrez comment configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/01/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e7204c223681b9a33c439b0d9fc653167422384a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011695"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans PIM

Azure AD Privileged Identity Management (PIM) génère des alertes en cas d’activité suspecte ou non fiable dans votre environnement. Lorsqu’une alerte est déclenchée, elle s’affiche sur le tableau de bord PIM. Sélectionnez l’alerte pour obtenir un rapport qui répertorie les utilisateurs ou les rôles à l’origine de son déclenchement.

![Alertes de sécurité PIM - capture d’écran](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertes de sécurité

Cette section répertorie toutes les alertes de sécurité pour les rôles d’annuaire, et explique comment les corriger et les éviter. Le terme Gravité revêt les significations suivantes :

* **Élevé** : nécessite une action immédiate, car il s’agit d’une violation de stratégie.
* **Moyen** : ne nécessite pas d’action immédiate, mais signale une violation potentielle de la stratégie.
* **Faible** : ne nécessite pas d’action immédiate, mais suggère une modification de la stratégie.

### <a name="roles-are-being-assigned-outside-of-pim"></a>Les rôles sont affectés en dehors de PIM

| | |
| --- | --- |
| **Niveau de gravité** | Élevé |
| **Pourquoi reçois-je cette alerte ?** | Les affectations de rôle privilégié effectuées en dehors de PIM ne sont pas analysées correctement et peuvent indiquer une attaque active. |
| **Procédure de résolution** | Passez en revue les utilisateurs de la liste et retirez-les des rôles privilégiés qui leur ont été attribués en dehors de PIM. |
| **Prévention** | Recherchez les endroits où des rôles privilégiés ont été assignés aux utilisateurs en dehors de PIM et interdisez toute future affectation à partir de là. |
| **Action d´atténuation dans le portail** | Supprime le compte de leur rôle privilégié. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Comptes périmés potentiels dans un rôle privilégié

| | |
| --- | --- |
| **Niveau de gravité** | Moyenne |
| **Pourquoi reçois-je cette alerte ?** | Les comptes qui n’ont pas changé leur mot de passe récemment peuvent être des comptes de service ou des comptes partagés qui ne sont pas en cours de maintenance. Ces comptes dans des rôles privilégiés sont vulnérables aux attaques. |
| **Procédure de résolution** | Passez en revue les comptes de la liste. S’ils n’ont plus besoin d’un accès, supprimez-les de leurs rôles privilégiés. |
| **Prévention** | Assurez-vous que les comptes partagés modifient régulièrement leur mot de passe fort en cas de changement des utilisateurs qui connaissent le mot de passe. </br>Vérifiez régulièrement les comptes avec des rôles privilégiés à l’aide de révisions d’accès, et supprimez les attributions de rôles qui ne sont plus nécessaires. |
| **Action d´atténuation dans le portail** | Supprime le compte de leur rôle privilégié. |

### <a name="users-arent-using-their-privileged-roles"></a>Les utilisateurs n’utilisent pas leurs rôles privilégiés

| | |
| --- | --- |
| **Niveau de gravité** | Faible |
| **Pourquoi reçois-je cette alerte ?** | L’affectation de rôles privilégiés à des utilisateurs qui n’en ont pas besoin augmente la probabilité d’une attaque. Il est également plus facile pour les attaquants de passer inaperçus dans des comptes qui ne sont pas activement utilisés. |
| **Procédure de résolution** | Passez en revue les utilisateurs de la liste et retirez-les des rôles privilégiés dont ils n’ont pas besoin. |
| **Prévention** | Affectez uniquement des rôles privilégiés aux utilisateurs qui en ont besoin sur le plan professionnel. </br>Prévoyez des révisions d’accès régulières pour vérifier que les utilisateurs ont toujours besoin de leur accès. |
| **Action d´atténuation dans le portail** | Supprime le compte de leur rôle privilégié. |
| **Déclencheur** | Cette alerte se déclenche si un utilisateur reste un certain temps sans activer un rôle. |
| **Nombre de jours** | Ce paramètre spécifie le nombre de jours, de 0 à 100, pendant lesquels un utilisateur peut rester sans activer un rôle.|

### <a name="there-are-too-many-global-administrators"></a>Trop d'administrateurs généraux

| | |
| --- | --- |
| **Niveau de gravité** | Faible |
| **Pourquoi reçois-je cette alerte ?** | Administrateur général est le rôle privilégié le plus élevé. Si un administrateur général est victime d’une attaque, la personne malveillante a accès à toutes ses autorisations, ce qui met en danger l’intégralité de votre système. |
| **Procédure de résolution** | Passez en revue les utilisateurs de la liste et supprimez ceux qui n’ont absolument pas besoin du rôle Administrateur général. </br>Affectez à ces utilisateurs des rôles privilégiés inférieurs. |
| **Prévention** | Affectez aux utilisateurs le rôle le moins privilégié dont ils ont besoin. |
| **Action d´atténuation dans le portail** | Supprime le compte de leur rôle privilégié. |
| **Déclencheur** | Cette alerte se déclenche si deux critères correspondent, et vous pouvez configurer ces deux valeurs. Vous devez tout d’abord atteindre un certain seuil d’administrateurs généraux. Puis vous devez réserver un certain pourcentage du total de vos affectations à des rôles d’administrateur général. L’alerte ne s’affiche pas si vous ne remplissez qu’un seul de ces critères. |
| **Nombre minimal d’administrateurs généraux** | Ce paramètre spécifie, de 2 à 100, le nombre d’administrateurs généraux qui, selon vous, présenterait un risque. |
| **Pourcentage d'administrateurs généraux** | Ce paramètre spécifie, de 0 % à 100 %, le pourcentage d'administrateurs généraux par rapport au nombre total d’administrateurs qui, selon vous, présenterait un risque pour votre environnement. |

### <a name="roles-are-being-activated-too-frequently"></a>Les rôles sont activés trop fréquemment

| | |
| --- | --- |
| **Niveau de gravité** | Faible |
| **Pourquoi reçois-je cette alerte ?** | Plusieurs activations au même rôle privilégié par le même utilisateur sont le signe d’une attaque. |
| **Procédure de résolution** | Passez en revue les utilisateurs de la liste et vérifiez que la [durée d’activation](pim-how-to-change-default-settings.md) de leur rôle privilégié est suffisante pour qu’ils puissent effectuer leurs tâches. |
| **Prévention** | Vérifiez que la [durée d’activation](pim-how-to-change-default-settings.md) des rôles privilégiés est suffisante pour que les utilisateurs puissent effectuer leurs tâches.</br>[Exigez l’authentification multifacteur (MFA)](pim-how-to-change-default-settings.md) pour les rôles privilégiés qui ont des comptes partagés par plusieurs administrateurs. |
| **Action d´atténuation dans le portail** | N/A |
| **Déclencheur** | Se déclenche si un utilisateur active le même rôle privilégié plusieurs fois pendant une période spécifiée. Vous pouvez configurer la période et le nombre d’activations. |
| **Période de renouvellement d'activation** | Ce paramètre spécifie la période, en jours, heures, minutes et secondes, que vous souhaitez utiliser pour effectuer le suivi des renouvellements suspects. |
| **Nombre de renouvellements de l’activation** | Ce paramètre spécifie le nombre d’activations, de 2 à 100, qui vous semblent devoir déclencher une alerte, dans l’intervalle de temps que vous avez choisi. Vous pouvez modifier ce paramètre en déplaçant le curseur ou en tapant un nombre dans la zone de texte. |

### <a name="roles-dont-require-mfa-for-activation"></a>Rôles qui ne nécessitent pas l’authentification multifacteur pour l’activation

| | |
| --- | --- |
| **Niveau de gravité** | Faible |
| **Pourquoi reçois-je cette alerte ?** | Sans authentification multifacteur, les utilisateurs compromis peuvent activer des rôles privilégiés. |
| **Procédure de résolution** | Passez en revue la liste des rôles et [exigez l’authentification multifacteur](pim-how-to-change-default-settings.md) pour chaque rôle. |
| **Prévention** | [Demander l’authentification multifacteur](pim-how-to-change-default-settings.md) pour chaque rôle.  |
| **Action d´atténuation dans le portail** | Rend l’authentification multifacteur obligatoire pour l’activation du rôle privilégié. |

## <a name="configure-security-alert-settings"></a>Configurez les paramètres d'alerte de sécurité

Vous pouvez personnaliser certaines alertes de sécurité dans PIM selon votre environnement et vos objectifs de sécurité. Suivez ces étapes pour ouvrir les paramètres d’alerte de sécurité :

1. Ouvrez **Azure AD Privileged Identity Management**.

1. Cliquez sur **Rôles Azure AD**.

1. Cliquez sur **Paramètres**, puis sur **Alertes**.

    ![Accès aux paramètres des alertes de sécurité](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Cliquez sur le nom d’une alerte pour configurer le paramètre de cette alerte.

    ![Paramètres d'alerte de sécurité](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles d’annuaire Azure AD dans PIM](pim-how-to-change-default-settings.md)
