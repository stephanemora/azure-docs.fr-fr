---
title: Gérer les événements d’alerte
description: Gérez les événements d’alerte détectés au sein de votre réseau.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: c0670f37da0cead5e3bd05a1d69e17191e8c0ccf
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508741"
---
# <a name="manage-alert-events"></a>Gérer les événements d’alerte

Les options suivantes sont disponibles pour gérer les événements d’alerte :

 | Action | Description |
 |--|--|
 | **Learn** | Autoriser l’événement détecté. Pour plus d’informations, consultez la section [Apprendre et désapprendre des événements](#about-learning-and-unlearning-events). |
 | **Reconnaître** | Masquer l’alerte une seule fois pour l’événement détecté. L’alerte sera de nouveau déclenchée si l’événement est de nouveau détecté. Pour plus d’informations, consultez la section [Reconnaitre et cesser de reconnaître des événements](#about-acknowledging-and-unacknowledging-events). |
 | **Désactiver le son** | Ignorer continuellement l’activité avec des appareils identiques et un trafic comparable. Pour plus d’informations, consultez la section [Désactiver et réactiver des événements](#about-muting-and-unmuting-events). |

## <a name="about-learning-and-unlearning-events"></a>Apprendre et désapprendre des événements

Les événements qui indiquent des écarts du réseau appris peuvent refléter des modifications valides du réseau. Il peut s’agir, par exemple, d’un nouvel appareil autorisé ayant rejoint le réseau ou d’une mise à jour de microprogramme autorisée.

Lorsque vous sélectionnez **Apprendre**, le capteur considère le trafic, les configurations ou l’activité comme valides. Cela signifie que les alertes ne seront plus déclenchées pour l’événement. Par ailleurs, l’événement ne sera pas calculé lorsque le capteur génère les rapports d’évaluation des risques, des vecteurs d’attaque, etc.

Par exemple, vous recevez une alerte indiquant une activité d’analyse d’adresse sur un appareil qu’un scanneur réseau n’avait pas définie précédemment. Si cet appareil a été ajouté au réseau à des fins d’analyse, vous pouvez demander au capteur d’apprendre l’appareil comme appareil d’analyse.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Fenêtre Analyse d’adresse détectée.":::

Les événements appris peuvent être désappris. Quand le capteur désapprend un événement, il redéclenche les alertes liées à cet événement.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Reconnaitre et cesser de reconnaître des événements

Dans certaines situations, il peut arriver que vous ne souhaitiez pas qu’un capteur apprenne un événement détecté ou que l’option ne soit pas disponible. L’incident peut nécessiter une atténuation à la place. Par exemple :

- **Atténuer une configuration réseau ou un appareil** : vous recevez une alerte indiquant qu’un nouvel appareil a été détecté sur le réseau. Après enquête, vous découvrez qu’il s’agit d’un appareil réseau non autorisé. Vous gérez l’incident en déconnectant l’appareil du réseau.
- **Mettre à jour une configuration de capteur** : vous recevez une alerte indiquant qu’un serveur a initié un nombre excessif de connexions distantes. Cette alerte a été déclenchée car les seuils d’anomalie du capteur ont été définis pour déclencher des alertes au-delà d’un certain nombre de sessions par minute. Vous gérez l’incident en mettant à jour les seuils.

Après atténuation ou enquête, vous pouvez demander au capteur de masquer l’alerte en sélectionnant **Reconnaître**. L’alerte sera de nouveau déclenchée si l’événement est de nouveau détecté.

Pour effacer l’alerte :

  - Sélectionnez **Reconnaître**.

Pour afficher de nouveau l’alerte :

  - Accédez à l’alerte, puis sélectionnez **Cesser de reconnaître**.

Cessez de reconnaître des alertes s’il est nécessaire de mener une enquête plus approfondie.

## <a name="about-muting-and-unmuting-events"></a>Désactiver et réactiver des événements

Dans certaines circonstances, vous pouvez demander à votre capteur d’ignorer un scénario spécifique sur votre réseau. Par exemple :

  - Le moteur d’**anomalie** déclenche une alerte suite à l’observation d’un pic d’utilisation de la bande passante entre deux appareils, mais le pic est valide pour les appareils.

  - Le moteur de **violation de protocole** déclenche une alerte sur un écart de protocole détecté entre deux appareils, mais l’écart est valide entre les appareils.

Dans ces situations, l’apprentissage n’est pas disponible. Lorsqu’il n’est pas possible de procéder à l’apprentissage et que vous souhaitez supprimer l’alerte et retirer l’appareil du calcul des risques et vecteurs d’attaque, vous pouvez désactiver l’événement d’alerte à la place.

> [!NOTE] 
> Vous ne pouvez pas désactiver les événements pour lesquels un appareil Internet est défini comme source ou destination.

### <a name="what-traffic-is-muted"></a>Quel type de trafic est désactivé ?

Un scénario de désactivation inclut les appareils réseau et le trafic détecté pour un événement. Le titre de l’alerte décrit le trafic en cours de désactivation.

Le ou les appareils en cours de désactivation apparaissent sous forme d’images dans l’alerte. Si deux appareils apparaissent, le trafic d’alerte spécifique entre eux est désactivé.

**Exemple 1**

Lorsqu’un événement est désactivé, il est ignoré chaque fois que l’appareil principal (source) envoie à l’appareil secondaire (destination) un code de fonction illégal tel que défini par le fournisseur.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Appareil secondaire reçu.":::

**Exemple 2**

Lorsqu’un événement est désactivé, il est ignoré chaque fois que la source envoie un en-tête HTTP avec un contenu illégal, quelle que soit la destination.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Capture d’écran d’un contenu d’en-tête HTTP illégal.":::

**Lorsqu’un événement est désactivé :**

- l’alerte reste accessible dans l’affichage des alertes **reconnues** jusqu’à sa réactivation.

- L’action de désactivation apparaît dans la **chronologie des événements**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Événement détecté et désactivé.":::

- Le capteur recalcule les appareils lors de la génération des rapports d’évaluation des risques, des vecteurs d’attaque, etc. Par exemple, si vous avez désactivé une alerte ayant détecté du trafic malveillant sur un appareil, ce dernier ne sera pas calculé dans le rapport d’évaluation des risques.

**Pour désactiver et réactiver une alerte :**

- Sélectionnez l’icône **Désactiver** sur l’alerte.

**Pour afficher les alertes désactivées :**

1. Sélectionnez l’option **Reconnues** dans l’écran principal des **alertes**.

2. Pointez sur une alerte pour voir si elle est désactivée.  

## <a name="see-also"></a>Voir aussi

[Contrôler quel trafic est surveillé](how-to-control-what-traffic-is-monitored.md)
