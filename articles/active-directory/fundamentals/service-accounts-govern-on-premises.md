---
title: Administrer les comptes de service locaux | Azure Active Directory
description: Guide de création et d'exécution d'un processus de gestion du cycle de vie des comptes de service
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
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557367"
---
# <a name="governing-on-premises-service-accounts"></a>Administrer les comptes de service locaux

Il existe quatre types de comptes de service locaux dans Windows Active Directory :

* [Comptes de service gérés de groupe](service-accounts-group-managed.md) (gMSA)

* [Comptes de service gérés autonomes](service-accounts-standalone-managed.md) (sMSA)

* [Comptes d'ordinateur](service-accounts-computer.md)

* [Comptes d'utilisateur employés en guise de comptes de service](service-accounts-user-on-premises.md)


Il est essentiel de bien administrer les comptes de service afin de : 

* protéger les comptes de service en fonction des exigences et des objectifs d'utilisation de ceux-ci ;

* gérer le cycle de vie des comptes de service et de leurs informations d'identification ;

* évaluer les comptes de service en fonction du risque auquel ils sont exposés et des autorisations qu'ils détiennent ; 

* veiller à ce qu'Active Directory et Azure Active Directory ne comportent pas de comptes de service obsolètes avec des autorisations potentiellement très étendues.

## <a name="principles-for-creating-a-new-service-account"></a>Principes liés à la création d'un compte de service

Utilisez les critères suivants lors de la création d'un compte de service.

| Principes| Considérations | 
| - |- | 
| Mappage de compte de service| Liez le compte de service à un service, une application ou un script unique. |
| Propriété| Veillez à ce qu'un propriétaire revendique et assume la responsabilité du compte. |
| Étendue| Définissez clairement l'étendue et anticipez la durée d'utilisation du compte de service. |
| Objectif| Créez des comptes de service dans un but spécifique. |
| Privilège| Appliquez le principe du privilège minimum comme suit : <br>N'attribuez jamais ces privilèges à des groupes intégrés comme les administrateurs.<br> Supprimez les privilèges des ordinateurs locaux, le cas échéant.<br>Personnalisez l'accès et utilisez la délégation Active Directory pour l'accès à l'annuaire.<br>Utilisez des autorisations d'accès claires.<br>Définissez des expirations et des restrictions en fonction de l'emplacement sur les comptes de service basés sur les utilisateurs. |
| Utilisation du contrôle et de l'audit| Supervisez les données de connexion et veillez à ce qu'elles soient conformes à l'utilisation prévue. Définissez des alertes en cas d'utilisation anormale. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Appliquer le principe du privilège minimum aux comptes d'utilisateur et limiter la surutilisation des comptes

Utilisez les paramètres suivants avec les comptes d'utilisateur employés en guise de comptes de service :

* [**Expiration du compte**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps) : configurez le compte de service pour qu'il expire automatiquement à une date/heure définie après sa période d'examen, sauf s'il est déterminé qu'il doit continuer.

*  **LogonWorkstations** : limitez les autorisations d'accès au compte de service. S'il est exécuté localement sur un ordinateur, et s'il a uniquement accès aux ressources de cet ordinateur, empêchez-le de se connecter ailleurs.

* [**Impossible de changer le mot de passe**](/powershell/module/addsadministration/set-aduser) : empêchez le compte de service de modifier son propre mot de passe en attribuant au paramètre la valeur false.

 
## <a name="build-a-lifecycle-management-process"></a>Mettre en place un processus de gestion du cycle de vie

Pour assurer la sécurité de vos comptes de service, vous devez les gérer depuis le moment où vous identifiez le besoin jusqu'à leur désactivation. 

Utilisez la procédure suivante pour gérer le cycle de vie des comptes de service :

1. Collecter les informations relatives à l'utilisation du compte
1. Intégrer le compte de service et l'application à la base de données de gestion de la configuration (CMDB)
1. Procéder à une évaluation des risques ou à un examen formel
1. Créer le compte de service et appliquer des restrictions
1. Planifier et effectuer des examens récurrents. Ajuster les autorisations et les étendues selon vos besoins
1. Déprovisionner le compte, le cas échéant

### <a name="collect-usage-information-for-the-service-account"></a>Collecter les informations relatives à l'utilisation du compte de service

Collectez les informations pertinentes pour chaque compte de service. Le tableau ci-dessous présente les informations minimales à collecter, mais vous devez collecter tout ce qui est nécessaire pour justifier l'existence des comptes.

| Données| Détails |
| - | - |
| Propriétaire| Utilisateur ou groupe responsable du compte de service |
| Objectif| Objectif du compte de service |
| Autorisations (étendues)| Jeu d'autorisations attendu |
| Liens vers la base de données de gestion de la configuration (CMDB)| Compte de service croisé avec script/application cible et propriétaire(s) |
| Risque| Évaluation des risques et de l'impact sur l'activité en fonction de l'évaluation des risques liés à la sécurité |
| Durée de vie| Durée de vie maximale prévue pour permettre la planification de l'expiration ou de la recertification du compte |


 

Idéalement, faites la demande d'un compte en libre-service, et exigez les informations pertinentes. Le propriétaire peut être un propriétaire d'application ou un chef d'entreprise, un membre d'une équipe informatique ou un propriétaire d'infrastructure. L'utilisation d'un outil tel que Microsoft Forms pour cette demande et les informations associées facilitera le transfert vers votre outil d'inventaire CMDB si le compte est approuvé.

### <a name="onboard-service-account-to-cmdb"></a>Intégrer un compte de service à la base de données CMDB

Stockez les informations collectées dans une application de type CMDB. Outre les informations liées à l'activité, incluez toutes les dépendances à d'autres infrastructures, applications et processus.  Ce référentiel central facilitera ce qui suit :

* Évaluation des risques

* Configuration du compte de service avec les restrictions requises

* Compréhension des dépendances fonctionnelles et de sécurité pertinentes

* Examens réguliers à des fins de sécurité et de continuité de l'activité

* Prise de contact avec le(s) propriétaire(s) pour l'examen, la suppression et la modification du compte de service

Prenons l'exemple d'un compte de service utilisé pour exécuter un site web et disposant des privilèges nécessaires pour se connecter à une ou plusieurs bases de données SQL. Les informations stockées dans votre base de données CMDB pour ce compte de service peuvent être les suivantes :

|Données | Détails|
| - | - |
| Propriétaire, adjoint(e)| John Bloom, Anna Mayers |
| Objectif| Lancer la page web des RH et se connecter aux bases de données des RH. Peut emprunter l'identité de l'utilisateur final lors de l'accès aux bases de données. |
| Autorisations, étendues| HR-WebServer : se connecter localement, exécuter une page web<br>HR-SQL1 : se connecter localement, accéder en lecture à l'ensemble de la base de données HR*<br>HR-SQL2 : se connecter localement, accéder en lecture à la base de données SALARY* |
| Cost Center| 883944 |
| Risque évalué| Moyen ; Impact sur l'activité : Moyen ; Informations privées ; Moyen |
| Restrictions de compte| Connexion : uniquement aux serveurs susmentionnés ; Impossible de changer le mot de passe ; Stratégie en matière de mot de passe MBI ; |
| Durée de vie| Illimitée |
| Cycle d'examen| Bi-annuel (par le propriétaire, par l'équipe chargée de la sécurité, par l'équipe chargée de la protection de la vie privée) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Procéder à une évaluation des risques ou à un examen formel de l'utilisation du compte de service

Compte tenu des autorisations et de l'objectif du compte, évaluez les risques qu'il peut présenter pour son application ou service associé et pour votre infrastructure s'il est compromis. Tenez compte des risques directs et indirects. 

* À quoi un adversaire aurait-il directement accès ?

* À quels autres systèmes ou informations le compte de service a-t-il accès ?

* Le compte peut-il être utilisé pour accorder des autorisations supplémentaires ?

* Comment saurez-vous quand les autorisations seront modifiées ?

L'évaluation des risques, une fois réalisée et documentée, peut avoir un impact sur ce qui suit :

* Restrictions de compte

* Durée de vie du compte

* Exigences relatives à l'examen du compte (cadence et réviseurs)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Créer un compte de service et appliquer des restrictions de compte

Ne créez un compte de service qu'après avoir documenté les informations pertinentes dans votre base de données CMDB et procédé à une évaluation des risques. Les restrictions de compte doivent être alignées sur l'évaluation des risques. Tenez compte des restrictions suivantes, à condition qu'elles soient pertinentes pour votre évaluation :

* [Expiration du compte](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * Pour tous les comptes d'utilisateur employés en guise de comptes de service, définissez une date de fin d'utilisation réaliste et précise. Définissez cette valeur à l'aide de l'indicateur « Expiration du compte ». Pour plus d'informations, consultez[ Set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration). 

* Connexion ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* Exigences en matière de [Stratégie de mot de passe](../../active-directory-domain-services/password-policy.md)

* Création dans un [emplacement UO](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) qui garantit la gestion uniquement pour les utilisateurs disposant de privilèges

* Configuration et collecte d'un audit [qui détecte les modifications](/windows/security/threat-protection/auditing/audit-directory-service-changes) apportées au compte de service – et à l'[utilisation du compte de service](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Lorsque vous êtes prêt pour la mise en production, accordez l'accès au compte de service en toute sécurité. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Planifier des examens réguliers des comptes de service

Mettez en place des examens réguliers des comptes de service classés comme à risque moyen et élevé. Les examens doivent inclure ce qui suit : 

* Attestation du propriétaire quant au besoin de conserver le compte, et justification des privilèges et des étendues

* Examen par les équipes chargées de la protection de la vie privée et de la sécurité, avec évaluation des connexions en amont et en aval

* Données issues d'audits garantissant qu'elles ne sont utilisées qu'aux fins prévues

### <a name="deprovision-service-accounts"></a>Déprovisionner des comptes de service

Dans le cadre de votre processus de déprovisionnement, commencez par supprimer les autorisations et la surveillance, puis, si nécessaire, supprimez le compte.

Les comptes de service peuvent être déprovisionnés dans les cas suivants :

* Le script ou l'application pour lequel le compte de service a été créé est mis hors service.

* La fonction du script ou de l'application pour laquelle le compte de service est utilisé (par exemple, accès à une ressource spécifique) est mise hors service.

* Le compte de service a été remplacé par un autre.

Après avoir supprimé toutes les autorisations, utilisez ce processus pour supprimer le compte.

1. Une fois l'application ou le script associé déprovisionnés, surveillez les connexions et l'accès aux ressources pour les comptes de service associés afin de vous assurer qu'ils ne sont pas utilisés dans un autre processus. Si vous êtes sûr qu'ils ne sont plus nécessaires, passez à l'étape suivante.

2. Désactivez le compte de service et assurez-vous qu'il n'est plus nécessaire. L'option Créer une stratégie d'entreprise pour les comptes occasionnels doit rester désactivée.

3. Supprimez ensuite le compte de service. 

   * Pour les comptes de service gérés, vous pouvez [le désinstaller](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) à l'aide de PowerShell ou le supprimer manuellement à partir du conteneur du compte.

   * Pour les comptes d'ordinateur ou d'utilisateur, vous pouvez supprimer manuellement le compte dans Active Directory.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants sur la sécurisation des comptes de service :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)

* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)

* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)

* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)

* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)