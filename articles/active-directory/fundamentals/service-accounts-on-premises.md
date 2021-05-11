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
ms.openlocfilehash: 4551050cd8606c577edbbdfd85debc06ac12020c
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206490"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Présentation des comptes de service Active Directory

Un service possède une identité de sécurité principale qui détermine les droits d’accès pour les ressources locales et réseau. Le contexte de sécurité d’un service Microsoft Win32 est déterminé par le compte de service utilisé pour démarrer le service. Vous utilisez un compte de service pour :
* identifier et authentifier un service ;
* réussir le démarrage d’un service ;
* accéder au code ou à une application, ou les exécuter ;
* démarrer un processus. 

## <a name="types-of-on-premises-service-accounts"></a>Types de comptes de service locaux

Selon votre cas d’usage, vous pouvez utiliser un compte de service géré (MSA), un compte d’ordinateur ou un compte d’utilisateur pour exécuter un service. Vous devez d’abord tester un service pour confirmer qu’il peut utiliser un compte de service géré. Si le service peut utiliser un MSA, vous devez en utiliser un.

### <a name="group-managed-service-accounts"></a>Comptes de service gérés de groupe

Pour les services qui s’exécutent dans votre environnement local, utilisez des [comptes de service géré de groupe (gMSA)](service-accounts-group-managed.md) chaque fois que cela est possible. Les gMSA fournissent une solution d’identité unique pour les services qui s’exécutent sur une batterie de serveurs ou derrière un équilibreur de charge réseau. Les gMSA peuvent également être utilisés pour les services qui s’exécutent sur un serveur unique. Pour en savoir plus sur les conditions requises pour les gMSA, consultez [Prise en main des comptes de service géré de groupe](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

### <a name="standalone-managed-service-accounts"></a>Comptes de service géré autonomes

Si vous ne pouvez pas utiliser de gMSA, utilisez un [compte de service géré autonome (sMSA)](service-accounts-standalone-managed.md). Les sMSA nécessitent au moins Windows Server 2008 R2 pour fonctionner. Contrairement aux gMSA, les sMSA s’exécutent sur un seul serveur. Ils peuvent être utilisés pour plusieurs services sur ce serveur.

### <a name="computer-accounts"></a>Comptes d'ordinateur

Si vous ne pouvez pas utiliser de MSA, envisagez d’utiliser un [compte d’ordinateur](service-accounts-computer.md). Le compte LocalSystem est un compte local prédéfini qui dispose d’autorisations étendues sur l’ordinateur local et fait office d’identité de l’ordinateur sur le réseau.

Les services qui fonctionnent comme un compte LocalSystem accèdent aux ressources du réseau en utilisant les informations d’identification du compte d’ordinateur sous le format <domain_name>\\<computer_name>. Son nom prédéfini est NT AUTHORITY\SYSTEM. Vous pouvez l’utiliser pour démarrer un service et fournir le contexte de sécurité associé.

> [!NOTE]
> Lorsque vous utilisez un compte d’ordinateur, vous ne pouvez pas déterminer quel service sur l’ordinateur utilise ce compte. Par conséquent, vous ne pouvez pas vérifier quel service effectue les modifications. 

### <a name="user-accounts"></a>Comptes d'utilisateurs

Si vous ne pouvez pas utiliser de MSA, envisagez d’utiliser un [compte d’utilisateur](service-accounts-user-on-premises.md). Un compte d’utilisateur peut être un compte d’utilisateur *de domaine* ou un compte d’utilisateur *local*.

Un compte d’utilisateur de domaine permet au service de tirer pleinement parti des fonctionnalités de sécurité du service de Windows et de Microsoft Active Directory Domain Services. Le service dispose des autorisations locales et réseau accordées au compte. Il dispose également des autorisations de tous les groupes dont le compte est membre. Les comptes de service de domaine prennent en charge l’authentification mutuelle Kerberos.

Un compte d’utilisateur local (format de nom : *.\UserName*) existe uniquement dans la base de données du gestionnaire des comptes de sécurité de l’ordinateur hôte. Il n’a pas d’objet utilisateur dans Active Directory Domain Services. Un compte local ne peut pas être authentifié par le domaine. Par conséquent, un service qui s’exécute dans le contexte de sécurité d’un compte d’utilisateur local n’a pas accès aux ressources réseau (sauf s’il s’agit d’un utilisateur anonyme). Les services qui s’exécutent dans le contexte d’un utilisateur local ne peuvent pas prendre en charge l’authentification mutuelle Kerberos dans laquelle le service est authentifié par ses clients. Pour ces raisons, les comptes d’utilisateur locaux sont généralement inappropriés pour les services d’annuaire.

> [!IMPORTANT]
> Les comptes de service ne doivent pas être membres de groupes privilégiés, car l’appartenance à un groupe privilégié confère des autorisations qui peuvent constituer un risque pour la sécurité. Chaque service doit avoir son propre compte de service à des fins d’audit et de sécurité.

## <a name="choose-the-right-type-of-service-account"></a>Choisir le type de compte de service approprié


| Critère| gMSA| sMSA| Compte&nbsp;d’ordinateur| Compte&nbsp;d’utilisateur |
| - | - | - | - | - |
| L’application s’exécute sur un seul serveur| Oui| Oui. Utilisez un gMSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible. |
| L’application s’exécute sur plusieurs serveurs| Oui| Non| Non. Le compte est lié au serveur.| Oui. Utilisez un MSA dans la mesure du possible. |
| L’application s’exécute derrière un équilibreur de charge| Oui| Non| Non| Oui. À utiliser uniquement si vous ne pouvez pas utiliser un gMSA. |
| L’application s’exécute sous Windows Server 2008 R2| Non| Oui| Oui. Utilisez un MSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible. |
| L’application s’exécute sous Windows Server 2012| Oui| Oui. Utilisez un gMSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible.| Oui. Utilisez un MSA dans la mesure du possible. |
| Obligation de limiter le compte de service à un seul serveur| Non| Oui| Oui. Utilisez un sMSA dans la mesure du possible.| Non |
| | |


### <a name="use-server-logs-and-powershell-to-investigate"></a>Utiliser les journaux du serveur et PowerShell pour examiner

Vous pouvez utiliser les journaux du serveur pour déterminer les serveurs et le nombre de serveurs sur lesquels une application s’exécute.

Pour obtenir un liste de la version de Windows Server pour tous les serveurs de votre réseau, vous pouvez exécuter la commande PowerShell suivante : 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>Rechercher des comptes de service locaux

Nous vous recommandons d’ajouter un préfixe tel que « svc- » à tous les comptes que vous utilisez en tant que comptes de service. Cette convention d’affectation de noms rendra les comptes plus faciles à trouver et à gérer. Pensez également à utiliser un attribut de description pour le compte de service et le propriétaire du compte de service. La description peut être un alias d’équipe ou le propriétaire de l’équipe de sécurité.

La recherche des comptes de service locaux est essentielle pour garantir leur sécurité. Cela peut être difficile pour les comptes non MSA. Nous vous recommandons d’examiner tous les comptes qui ont accès à vos ressources locales importantes et de déterminer quels comptes d’utilisateur ou d’ordinateur peuvent faire office de comptes de service. 

Pour savoir comment trouver un compte de service, consultez l’article relatif à ce type de compte dans la [section « Étapes suivantes »](#next-steps).

## <a name="document-service-accounts"></a>Documenter les comptes de service

Une fois que vous avez trouvé les comptes de service dans votre environnement local, documentez les informations suivantes : 

* **Propriétaire** : Personne responsable de la gestion du compte.

* **Objectif** : Application que le compte représente, ou tout autre objectif. 

* **Étendues des autorisations** : Autorisations dont il dispose ou qu’il doit avoir, ainsi que tous les groupes dont il est membre.

* **Profil de risque** : Risque pour votre entreprise si ce compte est compromis. Si le risque est élevé, utilisez un MSA.

* **Durée de vie prévue et attestation périodique** : Durée de vie prévue de ce compte et fréquence à laquelle le propriétaire doit examiner et attester de son utilité.

* **Sécurité du mot de passe** : Pour les comptes d’utilisateur et les comptes d’ordinateur locaux, emplacement où le mot de passe est stocké. Veillez à ce que les mots de passe soient conservés en toute sécurité et documentez qui y a accès. Envisagez d’utiliser [Privileged Identity Management](../privileged-identity-management/pim-configure.md) pour sécuriser les mots de passe stockés. 

  

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation des comptes de service, consultez les articles suivants :

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)  
* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)  
* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)  
* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)  
* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)

