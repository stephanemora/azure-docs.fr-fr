---
title: Présentation des comptes de service Active Directory | Azure Active Directory
description: Présentation des types de comptes de service dans Active Directory et de la façon de les sécuriser.
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
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645614"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Présentation des comptes de service Active Directory

Un service possède une identité de sécurité principale qui détermine les droits d’accès pour les ressources locales et réseau. Le contexte de sécurité d’un service Microsoft Win32 est déterminé par le compte de service utilisé pour démarrer le service. Un compte de service est utilisé pour :
* identifier et authentifier un service ;
* réussir le démarrage d’un service ;
* accéder au code ou à une application et les exécuter ;
* démarrer un processus. 

## <a name="types-of-on-premises-service-accounts"></a>Types de comptes de service locaux

Selon votre cas d’usage, vous pouvez utiliser un compte de service géré (MSA), un compte d’ordinateur ou un compte d’utilisateur pour exécuter un service. Les services doivent être testés pour confirmer qu’ils peuvent utiliser un compte de service géré. Si c’est le cas, vous devez en utiliser un.

### <a name="group-msa-accounts"></a>Comptes MSA de groupe

Utilisez des [comptes de service géré de groupe](service-accounts-group-managed.md) (gMSA) chaque fois que cela est possible pour les services qui s’exécutent dans votre environnement local. Les gMSA fournissent une solution d’identité unique pour un service s’exécutant sur une batterie de serveurs ou derrière un équilibreur de charge réseau. Ils peuvent également être utilisés pour un service s’exécutant sur un serveur unique. [Les gMSA ont des exigences spécifiques qui doivent être respectées](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

### <a name="standalone-msa-accounts"></a>Comptes MSA autonomes

Si vous ne pouvez pas utiliser un gMSA, utilisez des [comptes de service géré autonomes](service-accounts-standalone-managed.md) (sMSA). Les sMSA nécessitent au moins Windows Server 2008R2. Contrairement aux gMSA, les sMSA s’exécutent uniquement sur un serveur. Ils peuvent être utilisés pour plusieurs services sur ce serveur.

### <a name="computer-account"></a>Compte d'ordinateur

Si vous ne pouvez pas utiliser de MSA, effectuez des recherches à l’aide de [comptes d’ordinateur](service-accounts-computer.md). Le compte LocalSystem est un compte local prédéfini qui dispose de privilèges étendus sur l’ordinateur local et fait office d’identité de l’ordinateur sur le réseau.   
‎Les services qui fonctionnent comme un compte LocalSystem accèdent aux ressources du réseau en utilisant les informations d’identification du compte de l’ordinateur sous le format <domain_name>\<computer_name>.

NT AUTHORITY\SYSTEM est le nom prédéfini pour le compte LocalSystem. Il peut être utilisé pour démarrer un service et fournir le contexte de sécurité pour ce service.

> [!NOTE]
> Lorsqu’un compte d’ordinateur est utilisé, vous ne pouvez pas déterminer quel service sur l’ordinateur utilise ce compte et, par conséquent, ne pouvez pas auditer le service qui apporte des modifications. 

### <a name="user-account"></a>Compte d’utilisateur

Si vous ne pouvez pas utiliser de MSA, effectuez des recherches à l’aide de [comptes d’utilisateur](service-accounts-user-on-premises.md). Les comptes d’utilisateur peuvent être un compte d’utilisateur de domaine ou un compte d’utilisateur local.

Un compte d’utilisateur de domaine permet au service de tirer pleinement parti des fonctionnalités de sécurité du service de Windows et de Microsoft Active Directory Domain Services. Le service dispose de l’accès local et de l’accès au réseau accordés au compte. Il dispose également des autorisations de tous les groupes dont le compte est membre. Les comptes de service de domaine prennent en charge l’authentification mutuelle Kerberos.

Un compte d’utilisateur local (format de nom : « .\UserName ») existe uniquement dans la base de données SAM de l’ordinateur hôte ; il n’a pas d’objet utilisateur dans Active Directory Domain Services. Un compte local ne peut pas être authentifié par le domaine. Par conséquent, un service qui s’exécute dans le contexte de sécurité d’un compte d’utilisateur local n’a pas accès aux ressources réseau (sauf s’il s’agit d’un utilisateur anonyme). Les services qui s’exécutent dans le contexte d’un utilisateur local ne peuvent pas prendre en charge l’authentification mutuelle Kerberos dans laquelle le service est authentifié par ses clients. Pour ces raisons, les comptes d’utilisateur locaux sont généralement inappropriés pour les services d’annuaire.

> [!IMPORTANT]
> Les comptes de service ne doivent pas être membres de groupes privilégiés, car l’appartenance à un groupe privilégié confère des autorisations qui peuvent constituer un risque pour la sécurité. Chaque service doit avoir son propre compte de service à des fins d’audit et de sécurité.

## <a name="choose-the-right-type-of-service-account"></a>Choisir le type de compte de service approprié


| Critères| gMSA| sMSA| Compte d'ordinateur| Compte d’utilisateur |
| - | - | - | - | - |
| L’application s’exécute sur un serveur unique| Oui| Oui. Utilisez un gMSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible. |
| L’application s’exécute sur plusieurs serveurs| Oui| Non| Non. Le compte est lié au serveur.| Oui. Utilisez un MSA dans la mesure du possible. |
| L’application s’exécute derrière des équilibreurs de charge| Oui| Non| Non| Oui. À utiliser uniquement si vous ne pouvez pas utiliser un gMSA. |
| L’application s’exécute sous Windows Server 2008 R2| Non| Oui| Oui. Utilisez un MSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible. |
| S’exécute sous Windows Server 2012| Oui| Oui. Utilisez un gMSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible. |
| Obligation de limiter le compte de service à un seul serveur| Non| Oui| Oui. Utilisez un sMSA dans la mesure du possible.| Non. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Utiliser les journaux du serveur et PowerShell pour examiner

Vous pouvez utiliser les journaux du serveur pour déterminer les serveurs et le nombre de serveurs sur lesquels une application s’exécute.

Vous pouvez exécuter la commande PowerShell suivante pour obtenir un liste de la version de Windows Server pour tous les serveurs de votre réseau. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Rechercher des comptes de service locaux

Nous vous recommandons d’ajouter un préfixe, par exemple « svc », à tous les comptes utilisés comme comptes de service. Cette convention d’affectation de noms les rendra plus faciles à trouver et à gérer. Envisagez également l’utilisation d’un attribut de description pour le compte de service et le propriétaire du compte de service : il peut s’agir d’un alias d’équipe ou du propriétaire de l’équipe de sécurité.

La recherche des comptes de service locaux est essentielle pour garantir leur sécurité. Cela peut être difficile pour les comptes non MSA. Nous vous recommandons d’examiner tous les comptes qui ont accès à vos ressources locales importantes et de déterminer quels comptes d’utilisateur ou d’ordinateur peuvent faire office de comptes de service. Vous pouvez également utiliser les méthodes suivantes pour trouver des comptes.

* Les articles relatifs à chaque type de compte comportent des étapes détaillées permettant de trouver ce type de compte. Pour obtenir les liens vers ces articles, consultez la section Étapes suivantes de cet article.

## <a name="document-service-accounts"></a>Documenter les comptes de service

Une fois que vous avez trouvé les comptes de service dans votre environnement local, documentez les informations suivantes sur chaque compte. 

* Propriétaire. Personne responsable de la gestion du compte.

* Objectif. L’application que le compte représente, tout autre objectif. 

* Étendues des autorisations. Quelles sont les autorisations dont il dispose et devrait disposer ? De quels groupes est-il membre, le cas échéant ?

* Profil de risque. Quel est le risque pour votre entreprise si ce compte est compromis ? En cas de risque élevé, utilisez un MSA.

* Durée de vie anticipée et attestation périodique. Combien de temps prévoyez-vous que ce compte sera en service ? À quelle fréquence le propriétaire doit-il examiner et attester de la nécessité de maintenir le compte ?

* Sécurité du mot de passe. Pour les comptes d’utilisateur et d’ordinateur locaux, où le mot de passe est-il stocké ? Veillez à ce que les mots de passe soient conservés en toute sécurité et documentez qui y a accès. Envisagez d’utiliser [Privileged Identity Management](../privileged-identity-management/pim-configure.md) pour sécuriser les mots de passe stockés. 

  

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation des comptes de service :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)

* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)

* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)

* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)

* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)

