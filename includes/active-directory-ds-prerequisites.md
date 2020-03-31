---
title: Fichier Include
description: fichier Include avec conditions préalables
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426883"
---
> [!IMPORTANT]
> **Activez la synchronisation du hachage de mot de passe avec Azure AD Domain Services avant d’effectuer les tâches de cet article.**
>
> Suivez les instructions ci-dessous qui varient selon le type d’utilisateur de votre annuaire Azure AD. Effectuez les deux ensembles d’instructions si vous disposez d’une combinaison de comptes d’utilisateurs uniquement cloud et synchronisés dans votre annuaire Azure AD. Vous pouvez ne pas être en mesure d’effectuer les opérations suivantes dans le cas où vous essayez d’utiliser un compte invité B2B (par exemple, gmail ou MSA à partir d’un autre fournisseur d’identité que nous autorisons), car le mot de passe de ces utilisateurs n’est pas synchronisé avec un domaine géré car il s’agit de comptes d’invité dans le répertoire. Les informations complètes sur ces comptes, y compris leurs mots de passe se trouvent en dehors d’Azure AD. Comme ces informations ne sont pas dans Azure AD, elles ne sont pas synchronisées avec le domaine géré. 
> - [Instructions pour les comptes d’utilisateurs uniquement cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instructions pour les comptes d’utilisateurs synchronisés à partir d’un répertoire local](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
