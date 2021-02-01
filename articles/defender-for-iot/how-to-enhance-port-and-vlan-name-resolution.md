---
title: Améliorer la résolution des noms de ports et de VLAN
description: Personnalisez les noms de ports et de VLAN sur vos capteurs afin d’enrichir la résolution d’appareil.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9c976671bccb420ae24d8def7a6574098d86ce6d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98803577"
---
# <a name="enhance-port-and-vlan-name-resolution"></a>Améliorer la résolution des noms de ports et de VLAN

Vous pouvez personnaliser les noms de ports et de VLAN sur vos capteurs afin d’enrichir la résolution d’appareil.

## <a name="customize-port-names"></a>Personnaliser les noms de ports

Azure Defender pour IoT attribue automatiquement des noms à la plupart des ports universellement réservés, tels que DHCP ou HTTP. Vous pouvez personnaliser les noms de ports d’autres ports que Defender pour IoT détecte. Par exemple, attribuez un nom à un port non réservé parce que ce port affiche une activité anormalement élevée.

Ces noms s’affichent dans les cas suivants :

  - Vous sélectionnez **Groupes d’appareils** dans la table des périphériques.

  - Vous créez des widgets qui fournissent des informations de port.

### <a name="view-custom-port-names-in-the-device-map"></a>Afficher les noms de ports personnalisés dans la table des périphériques

Les ports qui incluent un nom défini par les utilisateurs apparaissent dans la table des périphériques, dans le groupe **Applications connues**.

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Capture d’écran de la table des périphériques, montrant le groupe Applications connues.":::

### <a name="view-custom-port-names-in-widgets"></a>Afficher les noms de ports personnalisés dans des widgets

Les noms de ports que vous avez définis apparaissent dans les widgets qui couvrent le trafic par port.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Couvrir le trafic.":::

Pour définir des noms de ports personnalisés :

1. Sélectionnez **Paramètres système** puis **Alias standard**.

2. Sélectionnez **Ajouter un alias de port**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Ajouter un alias de port.":::

3. Entrez le numéro de port, sélectionnez **TCP/UDP** ou **Les deux**, puis ajoutez le nom.

4. Sélectionnez **Enregistrer**.

## <a name="configure-vlan-names"></a>Configurer les noms de VLAN

Vous pouvez enrichir les données d’inventaire d’appareils à l’aide de numéros et d’étiquettes de VLAN d’appareils. Outre l’enrichissement des données, vous pouvez afficher le nombre d’appareils par VLAN, ainsi que la bande passante par widget de VLAN.

La prise en charge des VLAN est basée sur 802.1q (jusqu’à l’ID de VLAN 4094).

Deux méthodes sont disponibles pour récupérer des informations de VLAN :

- **Détectés automatiquement** : Par défaut, le capteur découvre automatiquement les VLAN. Les VLAN détectés avec le trafic apparaissent sur l’écran de configuration de VLAN, dans des rapports d’exploration de données, ainsi que dans d’autres rapports contenant des informations de VLAN. Les VLAN non utilisés n’apparaissent pas. Vous ne pouvez pas modifier ou supprimer ces VLAN. 

  Vous devez ajouter un nom unique à chaque VLAN. Si vous n’ajoutez pas de nom, le numéro de VLAN s’affiche dans tous les emplacements où le VLAN est signalé.

- **Ajoutés manuellement** : Vous pouvez ajouter des VLAN manuellement. Vous devez ajouter un nom unique pour chaque VLAN ajouté manuellement, et pouvez modifier ou supprimer ces VLAN.

Les noms de VLAN peuvent contenir jusqu’à 50 caractères ASCII.

> [!NOTE]
> Les noms de VLAN ne sont pas synchronisés entre le capteur et la console de gestion. Vous devez également définir le nom sur la console de gestion.  
Pour les commutateurs Cisco, ajoutez la ligne suivante à la configuration d’étendue : `monitor session 1 destination interface XX/XX encapsulation dot1q`. Dans cette commande, la chaîne *XX/XX* correspond au nom et au numéro du port.

Pour configurer des VLAN :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans la fenêtre **Paramètre système**, sélectionnez **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Utilisez les paramètres système pour modifier vos VLAN.":::

3. Ajoutez un nom unique en regard de chaque ID de VLAN.

## <a name="next-steps"></a>Étapes suivantes

Affichez des informations sur les appareils enrichis dans différents rapports :

- [Examiner les détections de capteur dans un inventaire d’appareils](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Rapports de tendances et de statistiques des capteurs](how-to-create-trends-and-statistics-reports.md)
- [Requêtes d’exploration de données de capteur](how-to-create-data-mining-queries.md)
