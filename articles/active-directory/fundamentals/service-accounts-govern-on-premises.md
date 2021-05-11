---
title: Administrer les comptes de service locaux | Azure Active Directory
description: Utilisez ce guide pour créer et exécuter un processus de gestion du cycle de vie des comptes de service.
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
ms.openlocfilehash: 50ba13a49d8e08b70ebf8a8bb12dfe92d8a35bb7
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206581"
---
# <a name="govern-on-premises-service-accounts"></a>Administrer les comptes de service locaux

Active Directory propose quatre types de comptes de service locaux :

* [Comptes de service géré de groupe (gMSA)](service-accounts-group-managed.md)  
* [Comptes de service géré autonomes (sMSA)](service-accounts-standalone-managed.md)  
* [Comptes d'ordinateur](service-accounts-computer.md)  
* [Comptes d’utilisateur faisant office de comptes de service](service-accounts-user-on-premises.md)


Il est essentiel de bien administrer les comptes de service afin de pouvoir : 

* les protéger en fonction des exigences et des objectifs d’utilisation de ceux-ci ;  
* gérer le cycle de vie des comptes et de leurs informations d’identification ;  
* les évaluer en fonction du risque auquel ils sont exposés et des autorisations qu’ils détiennent ;  
* veiller à ce qu'Active Directory et Azure Active Directory ne comportent pas de comptes de service obsolètes avec des autorisations potentiellement très étendues.

## <a name="principles-for-creating-a-new-service-account"></a>Principes liés à la création d'un compte de service

Lorsque vous créez un compte de service, vous devez comprendre les considérations indiquées dans le tableau suivant :

| Principe| Considération | 
| - |- | 
| Mappage de compte de service| Liez le compte de service à un service, une application ou un script unique. |
| Propriété| Veillez à ce qu'un propriétaire revendique et assume la responsabilité du compte. |
| Étendue| Définissez clairement l’étendue et anticipez la durée d’utilisation du compte de service. |
| Objectif| Créez des comptes de service dans un but spécifique. |
| Autorisations | Appliquez le principe de l’*autorisation minimale*. Pour cela, procédez de la façon suivante :<li>N’attribuez jamais d’autorisations à des groupes prédéfinis, tels que les administrateurs.<li>Supprimez les autorisations de l’ordinateur local, le cas échéant.<li>Personnalisez l’accès et utilisez la délégation Active Directory pour l’accès au répertoire.<li>Utilisez des autorisations d’accès granulaires.<li>Définissez des expirations de compte et des restrictions en fonction de l’emplacement sur les comptes de service basés sur l’utilisateur. |
| Utilisation du contrôle et de l'audit| Supervisez les données de connexion et veillez à ce qu’elles soient conformes à l’utilisation prévue. Définissez des alertes en cas d'utilisation anormale. |
| | |

### <a name="set-restrictions-for-user-accounts"></a>Définir des restrictions pour les comptes d’utilisateur

Pour les comptes d’utilisateur utilisés comme comptes de service, appliquez les paramètres suivants :

* [**Expiration du compte**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps&preserve-view=true) : Configurez le compte de service pour qu’il expire automatiquement à une date/heure définie après sa période d’examen, sauf si vous avez déterminé que le compte doit continuer.

*  **LogonWorkstations** : Limitez les autorisations d’accès du compte de service. S’il est exécuté localement sur un ordinateur, et qu’il a uniquement accès aux ressources de cet ordinateur, empêchez-le de se connecter ailleurs.

* [**Impossible de changer le mot de passe**](/powershell/module/addsadministration/set-aduser) : Empêchez le compte de service de modifier son propre mot de passe en attribuant au paramètre la valeur false.
 
## <a name="build-a-lifecycle-management-process"></a>Mettre en place un processus de gestion du cycle de vie

Pour contribuer à assurer la sécurité de vos comptes de service, vous devez les gérer depuis le moment où vous identifiez le besoin jusqu’à leur désactivation. 

Pour gérer le cycle de vie des comptes de service, utilisez la procédure suivante :

1. Collectez les informations relatives à l’utilisation du compte.
1. Déplacez le compte de service et l’application vers la base de données de gestion de la configuration (CMDB).
1. Procédez à une évaluation des risques ou à un examen formel.
1. Créer le compte de service et appliquer des restrictions
1. Planifier et effectuer des examens récurrents. Ajuster les autorisations et les étendues selon vos besoins
1. Supprimez les privilèges d’accès du compte, le cas échéant.

### <a name="collect-usage-information-for-the-service-account"></a>Collecter les informations relatives à l'utilisation du compte de service

Collectez les informations pertinentes pour chaque compte de service. Le tableau suivant indique la quantité minimale d’informations à collecter, mais vous devez collecter tout ce qui est nécessaire pour justifier l’existence de chaque compte.

| Données| Description |
| - | - |
| Propriétaire| Utilisateur ou groupe responsable du compte de service |
| Objectif| Objectif du compte de service |
| Autorisations (étendues)| Jeu d’autorisations attendu |
| Liens CMDB| Compte de service de liaison croisée avec le script ou l’application cible et les propriétaires |
| Risque| Évaluation des risques et de l’impact sur l’activité en fonction de l’évaluation des risques liés à la sécurité |
| Durée de vie| Durée de vie maximale prévue pour permettre la planification de l’expiration ou de la recertification du compte |
| | |

Idéalement, faites la demande d’un compte en libre-service et exigez les informations pertinentes. Le propriétaire peut être un propriétaire d’application ou un chef d’entreprise, un membre d’une équipe informatique ou un propriétaire d’infrastructure. En utilisant un outil tel que Microsoft Forms pour cette demande et les informations associées, vous faciliterez le transfert vers votre outil d’inventaire CMDB si le compte est approuvé.

### <a name="onboard-service-account-to-cmdb"></a>Intégrer un compte de service à la base de données CMDB

Stockez les informations collectées dans une application de type CMDB. Outre les informations liées à l’activité, incluez toutes les dépendances à d’autres infrastructures, applications et processus.  Ce référentiel central facilite les opérations suivantes :

* Évaluation des risques  
* Configuration du compte de service avec les restrictions requises  
* Compréhension des dépendances fonctionnelles et de sécurité pertinentes  
* Examens réguliers à des fins de sécurité et de continuité de l'activité  
* Prise de contact avec les propriétaires pour l’examen, la suppression et la modification du compte de service

Prenons l’exemple d’un compte de service utilisé pour exécuter un site web et disposant des autorisations nécessaires pour se connecter à une ou plusieurs bases de données SQL de ressources humaines (RH). Les informations stockées dans votre CMDB pour le compte de service, y compris les exemples de description, sont répertoriées dans le tableau suivant :

|Données | Exemple de description|
| - | - |
| Propriétaire, adjoint(e)| John Bloom, Anna Mayers |
| Objectif| Exécuter la page web des RH et se connecter aux bases de données des RH. Peut emprunter l’identité des utilisateurs finaux lors de l’accès aux bases de données. |
| Autorisations, étendues| HR-WEBServer : se connecter localement, exécuter une page web<br>HR-SQL1 : se connecter localement, lire les autorisations sur toutes les bases de données de RH<br>HR-SQL2 : se connecter localement, lire les autorisations sur la base de données Salaires uniquement |
| Cost Center| 883944 |
| Risque évalué| Moyen ; Impact sur l'activité : Moyen ; Informations privées ; Moyen |
| Restrictions de compte| Connexion : uniquement aux serveurs susmentionnés ; Impossible de changer le mot de passe ; Stratégie en matière de mot de passe MBI ; |
| Durée de vie| Non restreint |
| Cycle d'examen| Bi-annuel (par le propriétaire, par l’équipe chargée de la sécurité, par l’équipe chargée de la protection de la vie privée) |
| | |

### <a name="perform-a-risk-assessment-or-formal-review-of-service-account-usage"></a>Procéder à une évaluation des risques ou à un examen formel de l’utilisation du compte de service

Supposons que votre compte soit compromis par une source non autorisée. Évaluez les risques que le compte pourrait poser à l’application ou au service qui lui est associé et à votre infrastructure. Tenez compte des risques directs et indirects. 

* À quoi un utilisateur non autorisé aurait-il directement accès ?  
* À quels autres systèmes ou informations le compte de service a-t-il accès ?  
* Le compte peut-il être utilisé pour accorder des autorisations supplémentaires ?  
* Comment saurez-vous que les autorisations ont été modifiées ?

Une fois que vous avez effectué et documenté l’évaluation des risques, vous pouvez constater que les risques ont un impact sur :

* les restrictions du compte ;  
* la durée de vie du compte ;  
* les exigences relatives à l’examen du compte (cadence et réviseurs).

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Créer un compte de service et appliquer des restrictions de compte

Créez un compte de service uniquement après avoir terminé l’évaluation des risques et documenté les informations pertinentes dans votre CMDB. Alignez les restrictions du compte avec l’évaluation des risques. Tenez compte des restrictions suivantes, à condition qu’elles soient pertinentes pour votre évaluation :

* Pour tous les comptes d’utilisateur que vous utilisez comme comptes de service, définissez une date de fin réaliste et précise. Définissez la date à l’aide de l’indicateur **Expiration du compte**. Pour plus d’informations, consultez [Set-ADAccountExpiration](/powershell/module/activedirectory/set-adaccountexpiration). 

* Connectez-vous à [LogonWorkstation](/powershell/module/activedirectory/set-aduser).

* Exigences en matière de [stratégie de mot de passe](../../active-directory-domain-services/password-policy.md).

* Création de comptes dans un [emplacement d’unité d’organisation](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) qui garantit la gestion aux seuls utilisateurs autorisés.

* Configuration et collecte d’un audit [qui détecte les modifications](/windows/security/threat-protection/auditing/audit-directory-service-changes) apportées au compte de service et l’[utilisation du compte de service](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Lorsque vous êtes prêt à mettre le compte de service en production, accordez-lui un accès plus sécurisé. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Planifier des examens réguliers des comptes de service

Configurez des examens réguliers des comptes de service qui sont classés comme à risque moyen et élevé. Les examens doivent inclure ce qui suit : 

* Attestation du propriétaire quant au besoin de conserver le compte, et une justification des autorisations et des étendues.

* Examen par les équipes chargées de la protection de la vie privée et de la sécurité, avec une évaluation des connexions en amont et en aval.

* Données issues d’audits, en s’assurant qu’elles ne sont utilisées qu’aux fins prévues.

### <a name="deprovision-service-accounts"></a>Déprovisionner des comptes de service

Dans le cadre de votre processus de suppression des privilèges d’accès, commencez par supprimer les autorisations et la surveillance, puis supprimez le compte, le cas échéant.

Vous supprimez les privilèges d’accès des comptes de service dans les cas suivants :

* Le script ou l’application pour lequel le compte de service a été créé est mis hors service.

* La fonction du script ou de l’application pour laquelle le compte de service est utilisé (par exemple, accès à une ressource spécifique) est mise hors service.

* Le compte de service a été remplacé par un autre.

Une fois que vous avez supprimé toutes les autorisations, supprimez le compte en procédant comme suit :

1. Lorsque les privilèges d’accès de l’application ou du script associé ont été supprimés, surveillez les connexions et l’accès aux ressources pour les comptes de service associés afin de vous assurer qu’ils ne sont pas utilisés dans un autre processus. Si vous êtes sûr qu’ils ne sont plus nécessaires, passez à l’étape suivante.

1. Désactivez le compte de service pour empêcher la connexion et assurez-vous qu’il n’est plus nécessaire. Créez une stratégie d’entreprise pour la période pendant laquelle les comptes doivent rester désactivés.

1. Une fois que la stratégie de désactivation est satisfaite, supprimez le compte de service. 

   * **Pour les MSA** : [Désinstallez le compte](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps&preserve-view=true) à l’aide de PowerShell, ou supprimez-le manuellement du conteneur de comptes de service géré.

   * **Pour les comptes d’ordinateur ou d’utilisateur** : Supprimez manuellement le compte dans Active Directory.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation des comptes de service, consultez les articles suivants :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)  
* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)  
* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)  
* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)  
* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)
