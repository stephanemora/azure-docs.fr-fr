---
title: Audit et création de rapports relatifs à un utilisateur Azure Active Directory B2B Collaboration | Microsoft Docs
description: Les propriétés de l’utilisateur invité sont configurables dans Azure Active Directory B2B Collaboration
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 06622c093ca90b3873365e6c93c40fc7221a6398
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075191"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Audit et création de rapports relatifs à un utilisateur B2B Collaboration
Avec les utilisateurs invités, vous disposez de fonctionnalités d’audit identiques à celles des utilisateurs membres. 

## <a name="access-reviews"></a>Révisions d’accès
Vous pouvez utiliser les révisions d’accès pour vérifier périodiquement si les utilisateurs invités ont toujours besoin d’accéder à vos ressources. La fonctionnalité **Révisions d’accès** est disponible dans **Azure Active Directory** sous **Gérer** > **Relations organisationnelles**. (Vous pouvez également rechercher des « révisions d’accès » à partir de **Tous les services** dans le portail Azure.) Pour apprendre à utiliser les révisions d’accès, consultez [Gérer l’accès invité avec les révisions d’accès Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit d’Azure AD fournissent des enregistrements de l’activité du système et des utilisateurs, y compris les activités initiées par les utilisateurs invités. Pour accéder aux journaux d’audit, dans **Azure Active Directory**, sous **Surveillance**, sélectionnez **Journaux d’Audit**. Voici un exemple de l’historique d’invitation et d’échange pour l’invité Sam Oogle :

![journal d’audit](./media/auditing-and-reporting/audit-log.png)

Vous pouvez explorer chacun de ces événements pour en obtenir les détails. Examinons, par exemple, les détails de l’acceptation.

![détails de l’activité](./media/auditing-and-reporting/activity-details.png)

Vous pouvez également exporter ces journaux à partir d’Azure AD et utiliser l’outil de création de rapports de votre choix afin d’obtenir des rapports personnalisés.

### <a name="next-steps"></a>Étapes suivantes

- [Propriétés de l’utilisateur B2B Collaboration](user-properties.md)

