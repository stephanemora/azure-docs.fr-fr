---
title: Sécurisation des comptes de service basés sur l’utilisateur | Azure Active Directory
description: Guide de sécurisation des comptes d’utilisateur locaux.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416493"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Sécurisation des comptes de service basés sur l’utilisateur dans Active Directory

Les comptes d’utilisateur locaux représente l’approche traditionnelle pour sécuriser les services s’exécutant sous Windows. Utilisez ces comptes en dernier recours lorsque les comptes de service géré de groupe (gMSA) et les comptes de service géré autonomes (sMSA) ne sont pas pris en charge par votre service. Pour plus d’informations sur la sélection du meilleur type de compte à utiliser, consultez la présentation des comptes de service locaux. Examinez également si vous pouvez déplacer votre service pour utiliser un compte de service Azure comme identité managée ou principal de service. 

Les comptes d’utilisateur locaux peuvent être créés pour fournir un contexte de sécurité pour les services et accorder des privilèges requis pour que les services puissent accéder aux ressources locales et réseau. Ils nécessitent une gestion manuelle des mots de passe comme tout autre compte d’utilisateur Active Directory (AD). Les administrateurs de service et de domaine sont tenus d’observer des processus stricts de gestion des mots de passe afin de préserver la sécurité de ces comptes.

Lorsque vous utilisez un compte d’utilisateur en tant que compte de service, utilisez-le pour un seul service uniquement. Donnez-lui un nom qui indique clairement qu’il s’agit d’un compte de service et pour quel service. 

## <a name="benefits-and-challenges"></a>Avantages et défis

Avantages

Les comptes d’utilisateur locaux sont le type de compte le plus polyvalent à utiliser avec les services. Les comptes d’utilisateur utilisés comme comptes de service peuvent être contrôlés par toutes les stratégies qui régissent les comptes d’utilisateur normaux. Cela dit, utilisez-les uniquement si vous ne pouvez pas utiliser un MSA. Déterminez également si un compte d’ordinateur est une meilleure option. 

Défis liés aux comptes d’utilisateur locaux

Les défis suivants sont associés à l’utilisation de comptes d’utilisateur locaux.

| Défis| Corrections |
| - | - |
| La gestion des mots de passe est un processus manuel qui peut entraîner une sécurité plus faible et un temps d’arrêt du service.| Vérifiez que la complexité et la modification des mots de passe sont régies par un processus solide qui garantit des mises à jour régulières avec un mot de passe fort. <br> Coordonnez la modification des mots de passe avec une mise à jour du mot de passe sur le service, ce qui entraîne un temps d’arrêt du service. |
| Il peut s’avérer difficile d’identifier les comptes d’utilisateur locaux qui agissent en tant que comptes de service.| Documentez et tenez à jour les enregistrements des comptes de service déployés dans votre environnement. <br> Effectuez le suivi du nom du compte et des ressources auxquelles il a accès. <br> Envisagez d’ajouter un préfixe « svc_ » à tous les comptes d’utilisateur utilisés comme comptes de service. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Rechercher les comptes d’utilisateur locaux utilisés comme comptes de service

Les comptes d’utilisateur locaux sont comme n’importe quel autre compte d’utilisateur AD. Par conséquent, il peut être difficile de trouver ces comptes, car il n’existe pas d’attribut unique d’un compte d’utilisateur qui l’identifie en tant que compte de service. 

Nous vous recommandons de créer une convention d’affectation de noms facilement identifiable pour tout compte d’utilisateur utilisé comme compte de service.

Par exemple, ajoutez « service- » comme préfixe et nommez le service : «service-HRDataConnector ».

Vous pouvez utiliser certains des indicateurs ci-dessous pour rechercher ces comptes de service. Toutefois, il est possible que vous ne trouviez pas tous ces comptes :

* Comptes approuvés pour la délégation.

* Comptes avec des noms de principal du service.

* Comptes dont le mot de passe est défini pour ne jamais expirer.

Vous pouvez exécuter les commandes PowerShell suivantes pour rechercher les comptes d’utilisateur locaux créés pour les services.

### <a name="find-accounts-trusted-for-delegation"></a>Rechercher les comptes approuvés pour la délégation

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Rechercher les comptes ayant des noms de principal du service

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Rechercher les comptes dont les mots de passe sont définis pour ne jamais expirer

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Vous pouvez également auditer l’accès aux ressources sensibles et archiver les journaux d’audit dans un système SIEM (Informations de sécurité et gestion d’événements). À l’aide de systèmes tels qu’Azure Log Analytics ou Azure Sentinel, vous pouvez rechercher et analyser les comptes de service.

## <a name="assess-security-of-on-premises-user-accounts"></a>Évaluer la sécurité des comptes d’utilisateur locaux

Évaluez la sécurité de vos comptes d’utilisateur locaux utilisés comme comptes de service à l’aide des critères suivants :

* Quelle est la stratégie de gestion des mots de passe ?

* Le compte est-il membre d’un groupe privilégié ?

* Le compte dispose-t-il d’un accès en lecture/écriture aux ressources importantes ?

### <a name="mitigate-potential-security-issues"></a>Atténuer les problèmes de sécurité potentiels

Le tableau suivant indique les problèmes de sécurité potentiels et les atténuations correspondantes pour les comptes d’utilisateur locaux.

| Problèmes de sécurité| Corrections |
| - | - |
| Gestion des mots de passe|* Vérifiez que la complexité et la modification des mots de passe sont régies par un processus solide qui garantit des mises à jour régulières avec des exigences strictes en matière de mots de passe. <br> * Coordonnez la modification des mots de passe avec une mise à jour du mot de passe pour réduire le temps d’arrêt du service. |
| Le compte est membre de groupes privilégiés.| Vérifiez les appartenances aux groupes. Supprimez le compte des groupes privilégiés. Accordez au compte uniquement les droits et les autorisations dont il a besoin pour exécuter son service (consultez le fournisseur du service). Par exemple, vous pouvez être en mesure de refuser la connexion locale ou de refuser la connexion interactive. |
| Le compte dispose d’un accès en lecture/écriture aux ressources sensibles.| Auditez l’accès aux données sensibles. Archivez les journaux d’audit dans un système SIEM (Azure Log Analytics ou Azure Sentinel) à des fins d’analyse. Corrigez les autorisations de ressource si un niveau d’accès indésirable est détecté. |


## <a name="move-to-more-secure-account-types"></a>Passer à des types de comptes plus sécurisés

Microsoft déconseille aux clients d’utiliser des comptes d’utilisateur locaux comme comptes de service. Pour tout service utilisant ce type de compte, évaluez s’il peut être configuré pour utiliser un gMSA ou un sMSA.

En outre, évaluez si le service lui-même peut être déplacé vers Azure afin que des types de compte de service plus sécurisés puissent être utilisés. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants sur la sécurisation des comptes de service :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)

* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)

* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)

* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)

* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)

 
