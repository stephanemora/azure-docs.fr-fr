---
title: Configurer la surveillance et les métriques à l’aide d’Azure Monitor
titleSuffix: Azure Bastion
description: Découvrez la surveillance et les métriques Azure Bastion à l’aide d’Azure Monitor.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: bed26390a2a64b7bbb39f1df014d0d63ccce6a5f
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110534770"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Comment configurer la surveillance et les métriques pour Azure Bastion à l’aide d’Azure Monitor

Cet article vous aide à utiliser la surveillance et les métriques pour Azure Bastion à l’aide d’Azure Monitor.

>[!NOTE]
>L’utilisation des **métriques classiques** n’est pas recommandée.
>

## <a name="about-metrics"></a>À propos des métriques

Azure Bastion propose diverses métriques. Le tableau suivant présente la catégorie et les dimensions de chaque métrique disponible.

|**Mesure**|**Catégorie**|**Dimension(s)**|
| --- | --- | --- |
|Statut de la communication Bastion **|[Disponibilité](#availability)|NON APPLICABLE|
|Mémoire totale|[Disponibilité](#availability)|Instance|
|Utilisation du processeur|[Trafic](#traffic)|Instance
|Mémoire utilisée|[Trafic](#traffic)|Instance
|Nombre de sessions|[Performances](#performance)|Instance|

** L’état de la communication Bastion est uniquement applicable aux hôtes Bastions déployés après novembre 2020.

### <a name="availability-metrics"></a><a name="availability"></a>Métriques de disponibilité

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>État de communication Bastion

Vous pouvez afficher l’état de la communication d’Azure Bastion, agrégé sur toutes les instances comprenant l’hôte Bastion.

* La valeur **1** indique que Bastion est disponible.
* La valeur **0** indique que le service Bastion n’est pas disponible.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="Capture d’écran montrant le statut de communication.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Mémoire totale

Vous pouvez afficher la mémoire totale d’Azure Bastion, répartie sur chaque instance de Bastion.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="Capture d’écran montrant la mémoire totale.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Métriques de trafic

#### <a name="used-cpu"></a><a name="used-cpu"></a>Utilisation du processeur

Vous pouvez afficher l’utilisation du processeur d’Azure Bastion, répartie sur chaque instance de Bastion. La surveillance de cette métrique permet de mesurer la disponibilité et la capacité des instances qui composent Azure Bastion

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="Capture d’écran montrant l’utilisation du processeur.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Mémoire utilisée

Vous pouvez afficher l’utilisation de la mémoire sur chaque instance Bastion, répartie sur chaque instance de Bastion. La surveillance de cette métrique permet de mesurer la disponibilité et la capacité des instances qui composent Azure Bastion.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="Capture d’écran montrant la mémoire utilisée.":::

### <a name="performance-metrics"></a><a name="performance"></a>Métriques de performance

#### <a name="session-count"></a>Nombre de sessions

Vous pouvez afficher le nombre de sessions actives par instance de Bastion, agrégées par type de session (RDP et SSH). Chaque instance Azure Bastion peut prendre en charge une plage de sessions RDP et SSH actives. La surveillance de cette métrique vous aidera à comprendre si vous devez ajuster le nombre d’instances exécutant le service Bastion. Pour plus d’informations sur le nombre de sessions qu’Azure Bastion peut prendre en charge, reportez-vous au [Forum aux questions pour Azure Bastion](bastion-faq.md).

Les valeurs recommandées pour cette configuration de métrique sont les suivantes :

* **Agrégation :** Avg
* **Granularité :** 5 ou 15 minutes
* Le fractionnement par instance est recommandé pour obtenir un nombre plus précis

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="Capture d’écran montrant le nombre de sessions.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Comment afficher les métriques

1. Pour afficher les métriques, accédez à votre hôte Bastion.
1. Dans la liste **Surveillance**, sélectionnez **Métriques**.
1. Sélectionnez les paramètres. Si aucune métrique n’est définie, cliquez sur **Ajouter une métrique**, puis sélectionnez les paramètres.

   * **Portée :** Par défaut, la portée est définie sur l’hôte Bastion.
   * **Espace de noms de métriques :** Métriques standard.
   * **Métrique :** Sélectionnez la métrique que vous souhaitez afficher.

1. Une fois qu’une métrique est sélectionnée, l’agrégation par défaut est appliquée. Si vous le souhaitez, vous pouvez appliquer une division pour afficher les métriques avec différentes dimensions.

## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
  
