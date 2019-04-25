---
title: Gérer des programmes et des contrôles pour les révisions d’accès - Azure Active Directory | Microsoft Docs
description: Apprenez à créer des programmes supplémentaires pour chaque gouvernance, de gestion des risques et initiative de conformité de votre organisation pour collecter et organiser des révisions d’accès Azure Active Directory en tant que contrôles.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60246029"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Gérer les programmes et les révisions d’accès de contrôles pour Azure AD

Azure Active Directory (Azure AD) inclut des révisions d’accès des membres du groupe et d’accès à l’application. Ces contrôles permettent de savoir qui a accès aux applications et aux groupes de votre organisation. Ils permettent aux organisations de gérer efficacement leur gouvernance, la gestion des risques et les exigences de conformité.

## <a name="create-and-manage-programs-and-their-controls"></a>Créer et gérer des programmes et leurs contrôles
Vous pouvez simplifier le suivi et la collecte des révisions d’accès à des fins différentes en les organisant dans des programmes. Chaque révision d’accès peut être liée à un programme. Ainsi, lorsque vous préparez des rapports pour un auditeur, vous pouvez vous concentrer sur les révisions d’accès qui sont associées à une certaine initiative.  Programmes et les résultats de révision d’accès sont visibles aux utilisateurs de l’administrateur général, administrateur de l’utilisateur, administrateur de sécurité ou rôle de lecteur de sécurité.

Pour voir la liste des programmes, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) et sélectionnez **Programmes**.

L’option **Programme par défaut** est toujours présente. Si vous êtes un administrateur général ou un rôle d’utilisateur administrateur, vous pouvez créer des programmes supplémentaires. Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise.

Si vous n’avez plus besoin d’un programme et qu’il n’est lié à aucun contrôle, vous pouvez le supprimer.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une révision d’accès des groupes ou des applications](create-access-review.md)
- [Récupérer les résultats de révision d’accès pour les applications ou les groupes](retrieve-access-review.md)
