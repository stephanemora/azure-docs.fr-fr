---
title: Prise en charge des versions d’Apache Spark
description: Versions de Spark, Scala, Python et .NET prises en charge
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 05/26/2021
ms.author: midesa
ms.openlocfilehash: c312aad4cd3cea5af3c074c7e32498d9ee30cad5
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110450222"
---
# <a name="azure-synapse-runtimes"></a>Runtimes Azure Synapse
Les pools Apache Spark dans Azure Synapse utilisent des runtimes pour lier des versions des composants essentiels, des optimisations, des packages et des connecteurs d’Azure Synapse à une version Apache Spark spécifique. Ces runtimes sont mis à niveau régulièrement pour inclure les derniers correctifs, améliorations et fonctionnalités. 

Quand vous créez un pool Apache Spark serverless, vous avez la possibilité de sélectionner la version Apache Spark correspondante. En fonction de ce que vous avez indiqué, le pool est préinstallé avec les packages et composants runtime associés. Ces runtimes présentent les avantages suivants :

- Temps de démarrage de session plus courts
- Compatibilité testée avec des versions Apache Spark spécifiques
- Accès aux connecteurs populaires et compatibles, ainsi qu’aux packages open source

> [!NOTE]
> - Les mises à jour de maintenance sont automatiquement appliquées aux nouvelles sessions pour un pool Apache Spark serverless donné. 
> - Vous devez tester vos applications et vérifier qu’elles s’exécutent correctement quand vous utilisez de nouvelles versions du runtime.

## <a name="supported-azure-synapse-runtime-releases"></a>Versions du runtime Azure Synapse prises en charge 
Le tableau suivant répertorie le nom du runtime, la version Apache Spark et la date de publication des versions du runtime Azure Synapse prises en charge.

|  Nom du runtime  | Date de publication |  Étape de publication |
| ----- | ----- | ----- |
| [Runtime Azure Synapse pour Apache Spark 2.4](./apache-spark-24-runtime.md) | 15 décembre 2020 | GA|
| [Runtime Azure Synapse pour Apache Spark 3.0](./apache-spark-3-runtime.md) | 26 mai 2021 | PRÉVERSION |

## <a name="runtime-release-stages"></a>Étapes de mise publication du runtime

## <a name="preview-runtimes"></a>Runtime en préversion
Azure Synapse Analytics fournit des préversions qui permettent d’évaluer et de partager des commentaires sur les fonctionnalités avant la mise en disponibilité générale (GA). Quand un runtime est disponible en préversion, de nouvelles dépendances et de nouvelles versions de composants peuvent être introduites. Les contrats SLA de support ne s’appliquent pas aux runtimes en préversion. 

## <a name="generally-available-runtimes"></a>Runtimes en disponibilité générale
Les runtimes en disponibilité générale (GA) sont ouverts à tous les clients et sont prêts pour une utilisation en production. Une fois qu’un runtime est en disponibilité générale, les correctifs de sécurité et les améliorations concernant la stabilité peuvent être rétroportés. En outre, les nouveaux composants sont introduits uniquement s’ils ne modifient pas les dépendances sous-jacentes ni les versions des composants. 