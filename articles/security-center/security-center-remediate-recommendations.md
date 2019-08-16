---
title: Appliquer des recommandations dans Azure Security Center | Microsoft Docs
description: Ce document explique comment appliquer les recommandations d’Azure Security Center en vue de protéger vos ressources Azure et de rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778996"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Appliquer les recommandations d’Azure Security Center

Les recommandations fournissent des suggestions visant à mieux sécuriser vos ressources.  Pour appliquer une recommandation, vous devez suivre les étapes de correction qu’elle fournit. 

## Étapes de correction <a name="remediation-steps"></a>

Après avoir lu toutes les recommandations, vous devez décider laquelle appliquer en premier. Pour déterminer quelles recommandations appliquer en premier, il est recommandé de se baser sur l’[impact du degré de sécurisation](security-center-recommendations.md#monitor-recommendations).

1. Dans la liste, cliquez sur la recommandation.
1. Suivez les instructions dans la section **Étapes de correction**. Chaque recommandation comprend un ensemble d’instructions. L’exemple suivant montre les étapes de correction permettant de configurer les applications de manière à autoriser uniquement le trafic via HTTPS.

    ![Détails de la recommandation](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Une fois l’opération terminée, une notification s’affiche pour vous informer si la mise à jour a réussi.

## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment appliquer les recommandations fournies par Security Center. Pour plus d’informations sur Security Center, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
