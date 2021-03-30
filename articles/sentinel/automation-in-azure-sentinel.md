---
title: Introduction à l’automatisation dans Azure Sentinel | Microsoft Docs
description: 'Cet article présente les fonctionnalités d’orchestration, d’automatisation et de réponse aux incidents de sécurité (SOAR) dans Azure Sentinel et décrit ses composants SOAR : les règles d’automatisation et les playbooks.'
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608636"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Orchestration, automatisation et réponse aux incidents de sécurité (SOAR) dans Azure Sentinel

Cet article décrit les fonctionnalités d’orchestration, d’automatisation et de réponse aux incidents de sécurité (SOAR) d’Azure Sentinel, et montre comment l’utilisation de règles d’automatisation et de playbooks en réponse aux menaces de sécurité augmente l’efficacité de votre SOC et vous fait gagner du temps et des ressources.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel comme solution SOAR

### <a name="the-problem"></a>La problématique

Les équipes SIEM/SOC sont généralement submergées d’alertes de sécurité et d’incidents régulièrement, avec des volumes si conséquents que le personnel disponible est surchargé. Il en résulte trop souvent des situations où de nombreuses alertes sont ignorées et où les incidents ne peuvent pas être examinés. L’organisation reste donc vulnérable aux attaques qui passent inaperçues.

### <a name="the-solution"></a>La solution

Azure Sentinel, en plus d’être un système d’informations de sécurité et gestion d’événements (SIEM), est également une plateforme pour l’orchestration, l’automatisation et la réponse aux incidents de sécurité (SOAR). Un de ses objectifs principaux est d’automatiser les tâches de mise à jour, de réponse et d’enrichissement récurrentes et prévisibles qui sont la responsabilité du centre d’opérations de sécurité et du personnel (SOC/SecOP), en libérant du temps et des ressources en vue d’une investigation plus approfondie et de la recherche de menaces avancées. L’automatisation prend plusieurs formes différentes dans Azure Sentinel, avec notamment des règles d’automatisation qui gèrent de manière centralisée l’automatisation de la gestion et de la réponse aux incidents, et des playbooks qui exécutent des séquences d’actions prédéterminées pour fournir une automatisation avancée, puissante et flexible à vos tâches de réponse aux menaces.

## <a name="automation-rules"></a>Règles d’automatisation

Les règles d’automatisation sont un concept nouveau dans Azure Sentinel. Cette fonctionnalité permet aux utilisateurs de gérer de manière centralisée l’automatisation de la gestion des incidents. En plus de vous permettre d’affecter des playbooks aux incidents (pas seulement aux alertes comme auparavant), les règles d’automatisation vous permettent également d’automatiser les réponses pour plusieurs règles d’analyse à la fois, d’étiqueter, d’attribuer ou de fermer automatiquement des incidents sans nécessiter de playbooks, et de contrôler l’ordre des actions exécutées. Les règles d’automatisation simplifient l’utilisation de l’automatisation dans Azure Sentinel et vous permettent de simplifier des flux de travail complexes pour vos processus d’orchestration d’incident.

Pour en savoir plus, consultez cette [explication complète sur les règles d’automatisation](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - Les **règles d’automatisation** sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="playbooks"></a>Playbooks

Un playbook est un ensemble logique d’actions de réponse et de correction qui peuvent être exécutées à partir d’Azure Sentinel en tant que routine. Un playbook peut vous aider à automatiser et orchestrer votre réponse aux menaces. Il peut s’intégrer à d’autres systèmes, aussi bien internes qu’externes, et être configuré pour s’exécuter automatiquement en réponse à des alertes ou des incidents spécifiques, lorsqu’il est déclenché par une règle d’analyse ou une règle d’automatisation, respectivement. Il peut également être exécuté manuellement à la demande, en réponse aux alertes, à partir de la page Incidents.

Les playbooks dans Azure Sentinel sont basés sur des workflows intégrés à [Azure Logic Apps](../logic-apps/logic-apps-overview.md), service cloud qui vous permet de planifier, d’automatiser et d’orchestrer des tâches et des workflows à travers l’entreprise. Cela signifie que les playbooks peuvent tirer parti de toute la puissance et de la personnalisation des fonctionnalités d’intégration et d’orchestration de Logic Apps, ainsi que d’outils de conception faciles à utiliser, tout en bénéficiant de l’évolutivité, de la fiabilité et du niveau de service d’un service Azure de niveau 1.

Pour en savoir plus, consultez cette [explication complète sur les playbooks](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment Azure Sentinel utilisait l’automatisation pour aider votre SOC à fonctionner de façon plus efficace et efficiente.

- Pour en savoir plus sur l’automatisation de la gestion des incidents, consultez [Automatiser la gestion des incidents dans Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- Pour en savoir plus sur les options d’automatisation avancées, consultez [Automatiser la réponse aux menaces à l’aide de playbook dans Azure Sentinel](automate-responses-with-playbooks.md).
- Pour obtenir de l’aide sur l’implémentation des playbooks et des règles d’automatisation, consultez [Didacticiel : utiliser des playbooks pour automatiser les réponses aux menaces dans Azure Sentinel](tutorial-respond-threats-playbook.md).
