---
title: 'Résolution des problèmes : Données manquantes dans les journaux d’activité Azure Active Directory téléchargés | Microsoft Docs'
description: Fournit une résolution au problème de données manquantes dans les journaux d’activité Azure Active Directory téléchargés.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2200a9c75b371ed72ffefe6900367e698101e0fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437094"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Je ne parviens pas à trouver toutes les donnée dans les journaux d’activité Azure Active Directory que j’ai téléchargés

## <a name="symptoms"></a>Symptômes

J’ai téléchargé les journaux d’activité (d’audit ou de connexion) et tous les enregistrements correspondant à la période choisie n’apparaissent pas. Pourquoi ? 

 ![Reporting](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Cause :

Lorsque vous téléchargez des journaux d'activité sur le portail Azure, nous limitons l'échelle à 250 000 enregistrements, classés dans l'ordre du plus récent au moins récent. 

## <a name="resolution"></a>Résolution :

Vous pouvez tirer parti des [API de création de rapports Azure AD](concept-reporting-api.md) pour extraire jusqu’à un million d’enregistrements pour un point donné.

## <a name="next-steps"></a>Étapes suivantes

* [FAQ sur les rapports Azure Active Directory](reports-faq.md)

