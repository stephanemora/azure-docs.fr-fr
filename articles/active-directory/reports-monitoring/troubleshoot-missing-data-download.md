---
title: 'Résolution des problèmes : Données manquantes dans les journaux d’activité Azure Active Directory téléchargés | Microsoft Docs'
description: Fournit une résolution au problème de données manquantes dans les journaux d’activité Azure Active Directory téléchargés.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9138da42eeb87e45b86be10aff67792ee6de09b4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42144015"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Aucune donnée n’apparaît dans les journaux d’activité Azure Active Directory que j’ai téléchargés


## <a name="symptoms"></a>Symptômes

J’ai téléchargé les journaux d’activité (d’audit ou de connexion) et tous les enregistrements correspondant à la période choisie n’apparaissent pas. Pourquoi ? 

 ![Reporting](./media/troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Cause :

Lorsque vous téléchargez des journaux d’activité dans le portail Azure, nous limitons l’échelle à des enregistrements de 5 000 Ko, triés du plus récent au moins récent. 

## <a name="resolution"></a>Résolution :

Vous pouvez tirer parti des [API de création de rapports Azure AD](concept-reporting-api.md) pour extraire jusqu’à un million d’enregistrements pour un point donné. Nous vous recommandons d’exécuter un script de façon planifiée qui appelle les API de création de rapports pour extraire des enregistrements de manière incrémentielle sur une période donnée (par exemple, quotidienne ou hebdomadaire).

## <a name="next-steps"></a>Étapes suivantes
Consultez le [FAQ sur les rapports Azure Active Directory](reports-faq.md).

