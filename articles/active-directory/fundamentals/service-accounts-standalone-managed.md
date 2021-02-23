---
title: Sécurisation des comptes de service géré autonomes | Azure Active Directory
description: Guide de sécurisation des comptes de service géré autonomes.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a919338789a02d50cbb6976ee50b214cb0d612
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416494"
---
# <a name="securing-standalone-managed-service-accounts"></a>Sécurisation de comptes de service géré autonomes

Les comptes de service géré autonomes (sMSA) sont des comptes de domaine managé utilisés pour sécuriser un ou plusieurs services exécutés sur un serveur. Ils ne peuvent pas être réutilisés sur plusieurs serveurs. Les sMSA offrent une gestion automatique des mots de passe, une gestion simplifiée du nom de principal du service (SPN) et la possibilité de déléguer la gestion à d’autres administrateurs. 

Dans Active Directory, les sMSA sont liés à un serveur spécifique qui exécute un service. Vous pouvez trouver ces comptes dans le composant logiciel enfichable Utilisateurs et ordinateurs Active Directory de la console de gestion Microsoft.

![Capture d’écran du composant logiciel enfichable Utilisateurs et ordinateurs Active Directory montrant l’UO de comptes de service géré.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Les comptes de service géré ont été introduits le schéma Active Directory de Windows Server 2008R2 et le niveau de système d’exploitation doit être au minimum Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>Avantages de l’utilisation de sMSA

Les sMSA offrent une plus grande sécurité que les comptes d’utilisateur utilisés comme comptes de service, tout en réduisant la charge administrative :

* Définition de mots de passe forts. Les sMSA utilisent des mots de passe complexes générés de manière aléatoire de 240 octets. La complexité et la longueur des mots de passe de sMSA réduisent la probabilité qu’un service soit compromis par des attaques par force brute ou par dictionnaire.

* Recyclage régulier des mots de passe. Windows modifie automatiquement le mot de passe du sMSA tous les 30 jours. Les administrateurs de service et de domaine n’ont pas besoin de planifier des modifications de mot de passe ni de gérer les temps d’arrêt associés.

* Simplification de la gestion des noms de principal du service. Les noms de principal du service sont automatiquement mis à jour si le niveau fonctionnel du domaine (DFL) est Windows Server 2008 R2. Par exemple, le nom de principal du service est automatiquement mis à jour dans les scénarios suivants :

   * Le compte de l’ordinateur hôte a été renommé. 

   * Le nom DNS de l’ordinateur hôte a été modifié.

   * Lors de l’ajout ou de la suppression d’un paramètre supplémentaire sam-accountname ou dns-hostname à l’aide de [PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-adserviceaccount?view=win10-ps)

## <a name="when-to-use-smsas"></a>Quand l’utiliser des sMSA

Les sMSA peuvent simplifier les tâches de gestion et de sécurité. Utilisez des sMSA lorsque vous avez un ou plusieurs services déployés sur un seul serveur et que vous ne pouvez pas utiliser un gMSA. 

> [!NOTE] 
> Bien que vous puissiez utiliser des sMSA pour plusieurs services, nous recommandons que chaque service ait sa propre identité à des fins d’audit. 

Si le créateur du logiciel ne peut pas vous indiquer s’il peut utiliser un MSA, vous devez tester votre application. Pour ce faire, créez un environnement de test et assurez-vous qu’il peut accéder à toutes les ressources requises. Pour obtenir des instructions pas à pas, consultez [Créer et installer un sMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting).

### <a name="assess-security-posture-of-smsas"></a>Évaluer la posture de sécurité de sMSA

Les sMSA sont par nature plus sécurisés que les comptes d’utilisateur standard, qui nécessitent une gestion continue des mots de passe. Toutefois, il est important de tenir compte de l’étendue de l’accès des sMSA dans le cadre de leur posture de sécurité globale.

Le tableau suivant montre comment atténuer les potentiels problèmes de sécurité posés par des sMSA.

| Problèmes de sécurité| Corrections |
| - | - |
| Le sMSA est membre de groupes privilégiés.|Supprimez le sMSA des groupes avec privilèges élevés (par exemple, Administrateurs de domaine). <br> Utilisez le modèle avec le moins de privilèges et accordez au sMSA uniquement les droits et les autorisations dont il a besoin pour exécuter ses services. <br> Si vous n’êtes pas sûr des autorisations requises, consultez le créateur du service. |
| Le sMSA dispose d’un accès en lecture/écriture aux ressources sensibles.|Auditez l’accès aux données sensibles. Archivez les journaux d’audit dans un système SIEM (Azure Log Analytics ou Azure Sentinel) à des fins d’analyse. <br> Corrigez les autorisations de ressource si un niveau d’accès indésirable est détecté. |
| Par défaut, la fréquence de substitution de mot de passe des sMSA est de 30 jours| La stratégie de groupe peut être utilisée pour régler la durée en fonction des exigences de l’entreprise en matière de sécurité. <br> * Vous pouvez définir la durée d’expiration du mot de passe à l’aide du chemin suivant : <br>Configuration de l’ordinateur\Stratégies\ Paramètres Windows\Paramètres de sécurité\Options de sécurité\Membre de domaine : Durée maximale du mot de passe du compte de machine |



### <a name="challenges-with-smsas"></a>Défis liés aux sMSA

Les défis associés aux sMSA sont les suivants :

| Défis| Corrections |
| - | - |
| Ils peuvent être utilisés sur un serveur unique.| Utilisez des gMSA si vous devez utiliser le compte sur plusieurs serveurs. |
| Ils ne peuvent pas être utilisés dans plusieurs domaines.| Utilisez des gMSA si vous devez utiliser le compte sur plusieurs domaines. |
| Toutes les applications ne prennent pas en charge les sMSA.| Utilisez des gMSA dans la mesure du possible. Si vous ne le faites pas, utilisez un compte d’utilisateur standard ou un compte d’ordinateur, comme recommandé par le créateur de l’application. |


## <a name="find-smsas"></a>Rechercher des sMSA

Sur n’importe quel contrôleur de domaine, exécutez DSA.msc et développez le conteneur Comptes de service géré pour afficher tous les sMSA. 

La commande PowerShell suivante retourne tous les sMSA et gMSA du domaine Active Directory. 

`Get-ADServiceAccount -Filter *`

La commande suivante retourne uniquement les sMSA du domaine Active Directory.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>Gérer des sMSA

Vous pouvez utiliser les cmdlets PowerShell Active Directory suivantes pour gérer des sMSA :

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Passer à des sMSA

Si un service d’application prend en charge les sMSA, mais pas les gMSA, et qu’il utilise actuellement un compte d’utilisateur ou un compte d’ordinateur pour le contexte de sécurité, [créez et installez un sMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) sur le serveur. 

Dans l’idéal, déplacez les ressources vers Azure et utilisez des identités managées Azure ou des principaux de service.

 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants sur la sécurisation des comptes de service :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)

* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)

* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)

* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)

* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)

 
