---
title: Gérer l’accès aux ressources Azure avec Azure AD Privileged Identity Management (PIM)
description: En savoir plus sur la gestion de l’accès aux ressources Azure à l’aide d'Azure Active Directory Privileged Identity Management (PIM) et du contrôle d’accès en fonction du rôle (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 757068034868744b408c9402b521a0e4c73950f7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338198"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Gérer l’accès aux ressources Azure avec Azure AD Privileged Identity Management

Pour protéger des comptes privilégiés contre les cyberattaques malveillantes, vous pouvez utiliser Azure Active Directory Privileged Identity Management (PIM) afin de réduire le temps d’exposition des privilèges et d’augmenter la visibilité quant à leur utilisation via des rapports et des alertes. Pour cela, PIM n’autorise les utilisateurs à utiliser ces privilèges que pendant une période précise (« just in time » (JIT)), ou en leur attribuant des privilèges pour une plus courte durée après laquelle les privilèges sont automatiquement révoqués. 

Vous pouvez maintenant utiliser PIM avec le contrôle d’accès en fonction du rôle (RBAC) Azure pour gérer, contrôler et surveiller l’accès aux ressources Azure. PIM peut gérer l’appartenance des rôles intégrés et personnalisés pour vous aider à : 

- Activer à la demande l’accès aux ressources Windows Azure pendant une période précise (JIT)
- Faire automatiquement expirer l’accès aux ressources pour des utilisateurs et groupes attribués 
- Attribuer un accès temporaire à des ressources Azure pour les tâches rapides ou les planifications de l’appel
- Obtenir des alertes lorsque de nouveaux utilisateurs ou groupes se voient assigner un accès à des ressources, et lorsqu’ils activent des attributions éligibles

Pour plus d’informations, consultez [Qu’est-ce qu’Azure AD Privileged Identity Management ?](../active-directory/privileged-identity-management/pim-configure.md).