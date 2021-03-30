---
title: Comment configurer les métriques JMX – Azure Monitor Application Insights pour Java
description: Configurer une collection de métriques JMX supplémentaire pour l’agent Java Azure Monitor Application Insights
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608582"
---
# <a name="configuring-jmx-metrics"></a>Configuration des métriques JMX

L’agent Java Application Insights 3.0 collecte certaines des métriques JMX par défaut mais, dans de nombreux cas, cela ne suffit pas. Ce document décrit l’option de configuration de JMX en détail.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Comment faire pour collecter des métriques JMX supplémentaires ?

Vous pouvez configurer la collecte de métriques JMX en ajoutant une section ```"jmxMetrics"``` au fichier applicationinsights.jssur. Vous pouvez spécifier le nom de la métrique comme vous souhaitez qu’il apparaisse dans le portail Azure dans la ressource Application Insights. Vous devez définir le nom et l’attribut de l’objet pour chacune des métriques que vous souhaitez collecter.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Comment faire pour connaître les métriques disponibles pour la configuration ?

Vous avez mis le doigt dessus : vous devez connaître les noms et attributs d’objets ; ces propriétés diffèrent pour les bibliothèques, infrastructures et serveurs d’applications, et sont souvent mal documentées. Pour obtenir les noms et attributs d’objets, vous devez afficher l’arborescence MBean. Un MBean est un objet Java managé qui peut représenter un appareil, une application ou une ressource, et a un ensemble d’attributs. 

Pour afficher et parcourir les métriques disponibles, nous vous recommandons d’utiliser [Java Mission Control](https://www.oracle.com/java/technologies/jdk-mission-control.html) (JMC).

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Comment naviguer dans Java Mission Control pour accéder aux métriques appropriées ?

Lorsque vous exécutez l’outil Java Mission Control, vous disposez d’une sélection de machine virtuelle Java (JVM) sur le côté gauche ; cliquez sur le processus approprié sous l’onglet « JVM Browser ». Attendez que JMC charge le tableau de bord pour le processus, puis sélectionnez l’onglet « MBean Browser » en bas (voir ci-dessous). JMC doit se trouver dans le même dossier que la machine virtuelle Java, et votre processus ou votre application doivent être opérationnels.

![Capture d’écran du navigateur JCM MBean](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Comment faire pour accéder aux métriques que je souhaite, ainsi qu’aux attributs nécessaires ?

Le navigateur MBean ouvre l’arborescence MBean avec la liste des catégories qui peuvent être développées. La sélection d’une catégorie à gauche a pour effet d’ouvrir la liste d’attributs à droite. Voici un exemple de métrique, avec son nom d’objet et les attributs. Les attributs peuvent être imbriqués, comme dans l’exemple ci-dessous.

![Capture d’écran de l’arborescence JCM MBean](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Exemple de configuration

À partir de la sélection, comme dans l’image ci-dessus, configurons quelques métriques. La première est un exemple de métrique imbriquée : `LastGcInfo`, qui a plusieurs propriétés, et nous souhaitons capturer la propriété `GcThreadCount`.

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Types de métriques collectées et options de configuration disponibles ?

Nous prenons en charge les métriques JMX numériques et booléennes, tandis que les autres types de métriques ne sont pas prises en charge et seront ignorées. 

Actuellement, les caractères génériques et les attributs agrégés n’étant pas pris en charge, chaque paire nom d’objet/attribut doit être configurée séparément. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Où puis-je trouver les métriques JMX dans Application Insights ?

Lorsque votre application est en cours d’exécution et que les métriques JMX sont collectées, vous pouvez les afficher en accédant au portail Azure et accéder à votre ressource Application Insights. Sous l’onglet Métriques, sélectionnez la liste déroulante comme illustré ci-dessous pour afficher les métriques.

![Capture d’écran des métriques dans le portail](media/java-ipa/jmx/jmx-portal.png)
