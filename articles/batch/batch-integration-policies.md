---
title: Intégration avec les stratégies Azure - Azure Batch | Microsoft Docs
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617865"
---
# <a name="integration-with-azure-policy"></a>Intégration avec Azure Policy

Azure Policy est un service dans Azure qui vous permet de créer, d’affecter et de gérer des stratégies qui appliquent des règles sur vos ressources et garantissent ainsi que ces ressources restent conformes aux normes et aux contrats de niveau de service de votre entreprise. Azure Policy évalue vos ressources afin de détecter les non-conformités avec les stratégies affectées. 

Azure Batch fournit deux extensions intégrées pour vous aider à gérer la conformité des stratégies. 

|**Nom**...|   **Description**|**Effet(s)**|  **Version**|    **Source**
|----------------|----------|----------|----------------|---------------|
|Les journaux de diagnostic doivent être activés dans les comptes Batch|   Auditer l’activation des journaux de diagnostic Permet de recréer les pistes d’activité à utiliser à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.|AuditIfNotExists, Désactivé|  2.0.0|  GitHub|
|Des règles d’alerte de métrique doivent être configurées sur les comptes Batch| Auditez la configuration des règles d’alerte de métrique sur le compte Batch pour activer la métrique requise.|   AuditIfNotExists, Désactivé| 1.0.0|  GitHub|

Les définitions de stratégie décrivent les conditions qui doivent être respectées. Une condition compare la propriété de ressource à une valeur requise. Les champs de propriété de ressource sont accessibles à l’aide d’alias prédéfinis. Vous utilisez des alias de propriété pour accéder à des propriétés spécifiques d’un type de ressource. Les alias permettent de restreindre les valeurs ou les conditions autorisées pour la propriété d’une ressource. Chaque alias correspond aux chemins des différentes versions d’API d’un type de ressource donné. Lors de l’évaluation de la stratégie, le moteur de stratégie obtient le chemin de la propriété de cette version de l’API.

Les ressources requises par Batch sont les suivantes : compte, nœud de calcul, pool, travail et tâche. Vous utilisez donc les alias de propriété pour accéder aux propriétés spécifiques de ces ressources. En savoir plus sur les [alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Pour être sûr de connaître les alias actuels et passer en revue vos ressources et stratégies, utilisez l’extension de stratégie Azure pour Visual Studio Code. Cette extension peut être installée sur toutes les plateformes prises en charge par Visual Studio Code. Windows, Linux et macOS. Consultez les [instructions d’installation](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



