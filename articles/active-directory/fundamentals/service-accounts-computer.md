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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100416502"
---
# <a name="securing-computer-accounts"></a>Sécurisation des comptes d’ordinateur

Le compte d’ordinateur, ou compte LocalSystem, est un compte intégré doté de privilèges élevés qui donne accès à pratiquement toutes les ressources de l’ordinateur local. Ce compte n’est associé à aucun compte d’utilisateur connecté. Les services qui s’exécutent en tant que LocalSystem accèdent aux ressources réseau en présentant les informations d’identification de l’ordinateur aux serveurs distants. Il présente les informations d’identification sous la forme <domain_name>\<computer_name>$. Le nom prédéfini d’un compte d’ordinateur est NT AUTHORITY\SYSTEM. Il peut être utilisé pour démarrer un service et fournir le contexte de sécurité pour ce service.

![[Image 4](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Avantages de l’utilisation du compte d’ordinateur

Le compte d’ordinateur offre les avantages suivants :

* **Accès local sans restriction** : Le compte d’ordinateur donne un accès complet aux ressources locales de la machine.

* **Gestion automatique des mots de passe** : Le compte d’ordinateur vous évite de devoir modifier manuellement les mots de passe. Au lieu de cela, ce compte est membre d’Active Directory et le mot de passe du compte est modifié automatiquement. Cela évite également d’avoir à inscrire le nom de principal du service pour le service.

* **Droits d’accès limités hors machine** : La liste de contrôle d’accès (ACL, access-control list) par défaut dans Active Directory Domain Services permet un accès minimal aux comptes d’ordinateur. Si ce service devait être piraté, il n’aurait qu’un accès limité aux ressources de votre réseau.

## <a name="assess-security-posture-of-computer-accounts"></a>Évaluer la posture de sécurité de comptes d’ordinateur

Défis potentiels et atténuations associées lors de l’utilisation de comptes d’ordinateur : 

| Problèmes| Corrections |
| - | - |
| Les comptes d’ordinateur sont sujets à la suppression et à la récréation lorsque l’ordinateur quitte et rejoint le domaine.| Validez la nécessité d’ajouter un ordinateur à un groupe AD et vérifiez quel compte d’ordinateur a été ajouté à un groupe en utilisant les exemples de scripts fournis sur cette page.| 
| Si vous ajoutez un compte d’ordinateur à un groupe, tous les services qui s’exécutent en tant que LocalSystem sur cet ordinateur reçoivent les droits d’accès du groupe.| Soyez sélectif quant à l’appartenance de votre compte d’ordinateur à un groupe. Évitez de faire des comptes d’ordinateur des membres d’un groupe d’administrateurs de domaine, car le service associé dispose d’un accès complet à Active Directory Domain Services. |
| Mauvaise configuration par défaut du réseau pour LocalSystem| Ne partez pas du principe que le compte d’ordinateur dispose par défaut d’un accès limité aux ressources réseau. Vérifiez plutôt avec soin l’appartenance de ce compte à un groupe. |
| Services inconnus exécutés en tant que LocalSystem| Assurez-vous que tous les services exécutés sous le compte LocalSystem sont des services Microsoft ou des services approuvés de tiers. |


## <a name="find-services-running-under-the-computer-account"></a>Rechercher les services s’exécutant sous le compte d’ordinateur

Utilisez la cmdlet PowerShell suivante pour rechercher les services s’exécutant sous le contexte LocalSystem.

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Rechercher les comptes d’ordinateur qui sont membres d’un groupe spécifique**

Utilisez la cmdlet PowerShell suivante pour rechercher les comptes d’ordinateur qui sont membres d’un groupe spécifique.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Rechercher les comptes d’ordinateur qui sont membres d’un groupe privilégié**

Utilisez la cmdlet PowerShell suivante pour rechercher les comptes d’ordinateur qui sont membres de groupes d’administrateurs d’identité (Administrateurs de domaine, Administrateurs de l’entreprise, Administrateurs).

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Passer de comptes d’ordinateur à un autre type de compte

> [!IMPORTANT]
> Les comptes d’ordinateur sont des comptes à privilèges élevés et doivent être utilisés uniquement quand votre service a besoin d’un accès illimité aux ressources locales sur l’ordinateur et que vous ne pouvez pas utiliser un compte de service géré (MSA).

* Demandez au propriétaire de votre service si ce dernier peut être exécuté à l’aide d’un MSA, et utilisez un compte de service géré de groupe (gMSA) ou un compte de service géré autonome (sMSA) si votre service le prend en charge.

* Utilisez un compte d’utilisateur de domaine avec juste les privilèges nécessaires pour exécuter votre service.

## <a name="next-steps"></a>Étapes suivantes 

Consultez les articles suivants sur la sécurisation des comptes de service :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)

* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)

* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)

* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)

* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)

 

 
