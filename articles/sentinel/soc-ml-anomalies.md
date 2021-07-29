---
title: Utiliser les anomalies SOC-ML pour détecter les menaces dans Azure Sentinel | Microsoft Docs
description: Cet article explique comment utiliser les nouvelles fonctionnalités de détection des anomalies SOC-ML dans Azure Sentinel.
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
ms.date: 04/28/2021
ms.author: yelevin
ms.openlocfilehash: ef38176db69ea019254ced89e3e18f035633f2d5
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109811740"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-azure-sentinel"></a>Utiliser des anomalies SOC-ML pour détecter les menaces dans Azure Sentinel

> [!IMPORTANT]
>
> - Les anomalies SOC-ML sont actuellement en **préversion**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.

## <a name="what-are-soc-ml-anomalies"></a>Que sont les anomalies SOC-ML ?

Avec les attaquants et les défenseurs qui luttent constamment pour prendre l’avantage dans la course aux armements de la cybersécurité, les attaquants trouvent toujours des moyens d’échapper à la détection. Inévitablement, toutefois, les attaques entraînent toujours un comportement inhabituel dans les systèmes attaqués. Les anomalies basées sur le Machine Learning SOC-ML d’Azure Sentinel peuvent identifier ce comportement à l’aide de modèles de règle d’analyse qui peuvent implémentés sans configuration supplémentaire. Même si les anomalies n’indiquent pas nécessairement un comportement malveillant ou suspect, elles peuvent être utilisées pour améliorer les détections, les investigations et la recherche de menaces :

- **Signaux supplémentaires pour améliorer la détection** : les analystes de sécurité peuvent utiliser les anomalies pour détecter les nouvelles menaces et rendre les détections existantes plus efficaces. Une anomalie seule n’est pas un signal fort de comportement malveillant, mais lorsqu’elle est associée à plusieurs anomalies qui se produisent à différents moments de la chaîne de destruction, leur effet cumulatif est bien plus fort. Les analystes de sécurité peuvent également améliorer les détections existantes en faisant en sorte que le comportement inhabituel identifié par les anomalies soit une condition de déclenchement d’alertes.

- **Preuve au cours des investigations** : les analystes de sécurité peuvent également utiliser les anomalies au cours des investigations pour vous aider à confirmer une violation, trouver de nouveaux chemins d’accès pour l’examiner et évaluer son impact potentiel. Ces gains d’efficacité réduisent le temps que les analystes de sécurité consacrent aux investigations.

- **Le début des recherches proactives de menaces** : les chasseurs de menaces peuvent utiliser les anomalies comme contexte pour déterminer si leurs requêtes présentent un comportement suspect non couvert. Lorsque le comportement est suspect, les anomalies pointent également vers des directions à suivre potentielles en vue d’une nouvelle chasse. Ces indices fournis par les anomalies réduisent le temps nécessaire à la détection d’une menace et son risque de nuire.

Les anomalies peuvent être des outils puissants, mais elles sont notoirement très bruyantes. Elles nécessitent généralement beaucoup de paramétrages fastidieux pour des environnements spécifiques ou un traitement complexe. Les modèles d’anomalies SOC-ML pour Azure Sentinel sont réglés par notre équipe de science des données de sorte à être prêts à l’emploi, mais si vous devez les adapter davantage, le processus est simple et ne nécessite aucune connaissance en Machine Learning. Les seuils et les paramètres de la plupart des anomalies peuvent être configurés et affinés par le biais de l’interface utilisateur de règles d’analyse qui vous est déjà familière. Les performances du seuil et des paramètres d’origine peuvent être comparées aux nouvelles valeurs de l’interface et être réglées en fonction des besoins au cours d’une phase de test ou d’évaluation. Une fois que l’anomalie atteint les objectifs de performance, l’anomalie avec le nouveau seuil ou les nouveaux paramètres peut être promue en production simplement en cliquant sur un bouton. Les anomalies SOC-ML pour Azure Sentinel vous permettent de tirer parti des anomalies sans avoir à effectuer de lourd travail.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment SOC-ML vous aide à détecter les anomalies dans Azure Sentinel.

- Découvrez comment [afficher, créer, gérer et affiner les règles d’anomalie](work-with-anomaly-rules.md).
- En savoir plus sur les [autres types de règles d’analyse](tutorial-detect-threats-built-in.md).
