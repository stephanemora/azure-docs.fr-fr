---
title: Sécurisation des comptes de service géré de groupe | Azure Active Directory
description: Guide de sécurisation des comptes de service géré de groupe.
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
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644825"
---
# <a name="securing-group-managed-service-accounts"></a>Sécurisation de comptes de service géré de groupe

Les comptes de service géré de groupe (gMSA) sont des comptes de domaine managé utilisés pour sécuriser les services. Les gMSA peuvent s’exécuter sur un seul serveur ou dans une batterie de serveurs, par exemple des systèmes se trouvant derrière un équilibreur de charge réseau (NLB) ou un serveur Internet Information Services (IIS). Une fois que vous avez configuré vos services pour utiliser un principal gMSA, la gestion des mots de passe de ce compte est gérée par Windows.

## <a name="benefits-of-using-gmsas"></a>Avantages de l’utilisation de gMSA

Les gMSA offrent une solution d’identité unique avec une sécurité accrue tout en réduisant la charge administrative :

* **Définition de mots de passe forts**. Les gMSA utilisent des mots de passe complexes générés de manière aléatoire de 240 octets. La complexité et la longueur des mots de passe de gMSA réduisent la probabilité qu’un service soit compromis par des attaques par force brute ou par dictionnaire.

* **Recyclage régulier des mots de passe**. Les gMSA transfèrent la gestion des mots de passe à Windows, ce qui permet de modifier le mot de passe tous les 30 jours. Les administrateurs de service et de domaine n’ont plus besoin de planifier des modifications de mot de passe ni de gérer les interruptions de service pour assurer la sécurité des comptes de service. 

* **Prise en charge du déploiement sur des batteries de serveurs**. La possibilité de déployer des gMSA sur plusieurs serveurs permet de prendre en charge des solutions avec équilibrage de charge où plusieurs hôtes exécutent le même service. 

* **Prise en charge de la gestion simplifiée du nom de principal du serveur (SPN)** . Vous pouvez configurer le SPN à l’aide de PowerShell au moment de la création du compte. En outre, les services qui prennent en charge les inscriptions automatiques de SPN peuvent le faire par rapport au gMSA, sous réserve que les autorisations de gMSA soient correctement définies. 

## <a name="when-to-use-gmsas"></a>Quand utiliser des gMSA

Utilisez des gMSA comme type de compte préféré pour les services locaux, à moins qu’un service, tel que le clustering de basculement, ne les prenne pas en charge.

> [!IMPORTANT]
> Vous devez tester votre service avec des gMSA avant le déploiement en production. Pour ce faire, configurez un environnement de test et assurez-vous que l’application peut utiliser le gMSA et accéder aux ressources auxquelles elle doit accéder. Pour plus d’informations, consultez [Prise en charge des comptes de service géré de groupe](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019).


Si un service ne prend pas en charge l’utilisation de gMSA, la meilleure solution consiste à utiliser un compte de service géré autonome (sMSA). Les sMSA offrent les mêmes fonctionnalités qu’un gMSA, mais ils sont destinés au déploiement sur un seul serveur.

Si vous ne pouvez pas utiliser un gMSA ou que le sMSA est pris en charge par votre service, le service doit être configuré pour s’exécuter en tant que compte d’utilisateur standard. Les administrateurs de service et de domaine sont tenus d’observer des processus stricts de gestion des mots de passe afin de préserver la sécurité des comptes.

## <a name="assess-the-security-posture-of-gmsas"></a>Évaluer la posture de sécurité de gMSA

Les gMSA sont par nature plus sécurisés que les comptes d’utilisateur standard, qui nécessitent une gestion continue des mots de passe. Toutefois, il est important de tenir compte de l’étendue de l’accès des gMSA lorsque vous examinez leur posture de sécurité globale.

Le tableau suivant indique les problèmes de sécurité potentiels et les atténuations pour l’utilisation de gMSA.

| Problèmes de sécurité| Corrections |
| - | - |
| Le gMSA est membre de groupes privilégiés. | Vérifiez vos appartenances à des groupes. Pour ce faire, vous pouvez créer un script PowerShell pour énumérer toutes les appartenances aux groupes, puis filtrer le fichier CSV résultant par les noms de vos fichiers gMSA. <br>Supprimez le gMSA des groupes privilégiés.<br> Accordez au gMSA uniquement les droits et les autorisations dont il a besoin pour exécuter son service (consultez votre fournisseur de services). 
| Le gMSA dispose d’un accès en lecture/écriture aux ressources sensibles. | Auditez l’accès aux données sensibles. Archivez les journaux d’audit dans un système SIEM, par exemple Azure Log Analytics ou Azure Sentinel, à des fins d’analyse. Supprimez les autorisations de ressources inutiles si un niveau d’accès indésirable est détecté. |


## <a name="find-gmsas"></a>Rechercher des gMSA

Votre organisation a peut-être déjà créé des gMSA. Exécutez la cmdlet PowerShell suivante pour récupérer ces comptes :

Pour fonctionner efficacement, les gMSA doivent se trouver dans l’unité d’organisation (UO) Comptes de service géré.

  
![Capture d’écran de l’UO des comptes de service géré.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Pour trouver les MSA de service qui ne sont peut-être pas là, consultez les commandes suivantes.

**Pour rechercher tous les comptes de service, notamment les gMSA et les sMSA :**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>Gérer des gMSA

Vous pouvez utiliser les cmdlets PowerShell Active Directory suivantes pour gérer des gMSA :

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> À partir de Windows Server 2012, les cmdlets *-ADServiceAccount fonctionnent avec par défaut avec les gMSA. Pour plus d’informations sur l’utilisation des cmdlets ci-dessus, consultez [**Prise en main des comptes de service géré de groupe**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

## <a name="move-to-a-gmsa"></a>Passer à un gMSA
Les gMSA sont le type de compte de service le plus sécurisé pour les besoins locaux. Si vous pouvez passer à un gMSA, vous devriez le faire. En outre, envisagez de déplacer vos services vers Azure et vos comptes de service vers Azure Active Directory.

1.  Assurez-vous que la [clé racine KDS est déployée dans la forêt](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Il s’agit d’une opération unique.

2. [Créez un gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Installez le nouveau gMSA sur chaque hôte exécutant le service.
   > [!NOTE] 
   > Pour plus d’informations sur la création et l’installation de gMSA sur un hôte, avant de configurer votre service pour utiliser un gMSA, consultez [Prise en main des comptes de service géré de groupe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)).

 
4. Remplacez votre identité de service par le gMSA et spécifiez un mot de passe vide.

5. Vérifiez que votre service fonctionne sous la nouvelle identité gMSA.

6. Supprimez l’ancienne identité du compte de service.

 

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants sur la sécurisation des comptes de service :

* [Présentation des comptes de service locaux](service-accounts-on-premises.md)

* [Sécuriser les comptes de service gérés de groupe](service-accounts-group-managed.md)

* [Sécuriser les comptes de service gérés autonomes](service-accounts-standalone-managed.md)

* [Sécuriser les comptes d’ordinateur](service-accounts-computer.md)

* [Sécuriser les comptes d’utilisateur](service-accounts-user-on-premises.md)

* [Administrer les comptes de service locaux](service-accounts-govern-on-premises.md)