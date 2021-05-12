---
title: Sécurisation des comptes d’ordinateur | Azure Active Directory
description: Guide de sécurisation des comptes d’ordinateur locaux.
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
ms.openlocfilehash: ec2e8cddb93e2e6fdf9ff804bbc28fd283436131
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206616"
---
# <a name="secure-on-premises-computer-accounts"></a>Sécurisation des comptes d’ordinateur locaux

Un compte d’ordinateur, ou compte LocalSystem, est un compte intégré doté de privilèges élevés qui a accès à pratiquement toutes les ressources de l’ordinateur local. Il n’est associé à aucun compte d’utilisateur connecté. Les services qui s’exécutent en tant que LocalSystem accèdent aux ressources réseau en présentant les informations d’identification de l’ordinateur aux serveurs distants au format <nom_domaine>\\<nom_ordinateur>$. Le nom prédéfini d’un compte d’ordinateur est NT AUTHORITY\SYSTEM. Vous pouvez l’utiliser pour démarrer un service et fournir le contexte de sécurité associé.

![Capture d’écran de la liste des services locaux d’un compte d’ordinateur.](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-a-computer-account"></a>Avantages du compte d’ordinateur

Un compte d’ordinateur offre les avantages suivants :

* **Accès local sans restriction** : Le compte d’ordinateur donne un accès complet aux ressources locales de la machine.

* **Gestion automatique des mots de passe** : le compte d’ordinateur vous évite d’avoir à change manuellement de mot de passe. Il est membre d’Active Directory. Son mot de passe est modifié automatiquement. Vous n’avez pas besoin d’inscrire le nom de principal du service.

* **Droits d’accès limités hors machine** : la liste de contrôle d’accès par défaut dans Active Directory Domain Services (AD DS) permet un accès minimal aux comptes d’ordinateur. En cas d’accès par un utilisateur non autorisé, le service ne dispose que d’un accès limité aux ressources du réseau.

## <a name="assess-the-security-posture-of-computer-accounts"></a>Évaluation de l’état de la sécurité des comptes d’ordinateur

Quelques difficultés potentielles liées à l’utilisation d’un compte d’ordinateur et les atténuations associées sont présentées dans le tableau suivant :
 
| Problème | Limitation des risques |
| - | - |
| Les comptes d’ordinateur sont sujets à la suppression et à la recréation lorsque l’ordinateur quitte et rejoint le domaine. | Validez la nécessité d’ajouter un ordinateur à un groupe Active Directory, puis vérifiez quel compte d’ordinateur a été ajouté au groupe en utilisant les exemples de scripts de la section suivante de cet article.| 
| Si vous ajoutez un compte d’ordinateur à un groupe, tous les services qui s’exécutent en tant que LocalSystem sur cet ordinateur reçoivent les droits d’accès du groupe.| Sélectionnez avec soin l’appartenance aux groupes de votre compte d’ordinateur. Évitez de l’ajouter à des groupes d’administrateurs de domaine, car le service associé dispose d’un accès complet à AD DS. |
| La configuration réseau par défaut de LocalSystem est incorrecte. | Ne partez pas du principe que le compte d’ordinateur dispose par défaut d’un accès limité aux ressources réseau. Vérifiez plutôt avec soin l’appartenance aux groupes du compte. |
| Des services inconnus s’exécutent en tant que LocalSystem. | Vérifiez que tous les services qui s’exécutent sous le compte LocalSystem sont des services Microsoft ou des services tiers approuvés. |
| | |

## <a name="find-services-that-run-under-the-computer-account"></a>Recherche des services qui s’exécutent sous le compte d’ordinateur

Pour rechercher les services qui s’exécutent sous le contexte LocalSystem, utilisez la cmdlet PowerShell suivante :

```powershell
Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

Pour rechercher les comptes d’ordinateur membres d’un groupe spécifique, utilisez la cmdlet PowerShell suivante :

```powershell
Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

Pour rechercher les comptes d’ordinateur membres de groupes d’administrateurs d’identité (administrateurs de domaine, administrateurs de l’entreprise et administrateurs), utilisez la cmdlet PowerShell suivante :

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```

## <a name="move-from-computer-accounts"></a>Passer de comptes d’ordinateur à un autre type de compte

> [!IMPORTANT]
> Les comptes d’ordinateur sont des comptes dotés de privilèges élevés. Ils ne doivent être utilisés que si votre service a besoin d’un accès illimité aux ressources locales de l’ordinateur et que vous ne pouvez pas utiliser un compte de service administré (MSA, Managed Service Account).

* Demandez au propriétaire de votre service si ce dernier peut être exécuté à l’aide d’un compte MSA, et utilisez un compte de service administré de groupe (gMSA, group Managed Service Account) ou un compte de service administré autonome (sMSA, standalone Managed Service Account) si votre service le prend en charge.

* Utilisez un compte d’utilisateur de domaine doté seulement des autorisations nécessaires pour exécuter votre service.

## <a name="next-steps"></a>Étapes suivantes 

Pour plus d’informations sur la sécurisation des comptes de service, consultez les articles suivants :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)
* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)
* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)
* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)  
* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)
