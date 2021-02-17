---
title: Générer des rapports de tendances et de statistiques
description: Familiarisez-vous avec l’activité du réseau, les statistiques et les tendances du réseau en utilisant les widgets Tendances et Statistiques Defender pour IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 20b47204ea8f64bc430f436a9bcf1b2311409aa6
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509484"
---
# <a name="sensor-trends-and-statistics-reports"></a>Rapports de tendances et de statistiques des capteurs

## <a name="about-sensor-trends-and-statistics-reports"></a>À propos des rapports de tendances et de statistiques des capteurs

Vous pouvez créer des graphiques de widget et des graphiques en secteurs pour obtenir des informations sur les tendances et les statistiques du réseau. Les widgets peuvent être regroupés dans des tableaux de bord définis par l’utilisateur.

> [!NOTE]
> Les analystes d’administration et de sécurité peuvent créer des rapports de tendances et de statistiques.

Le tableau de bord comprend des widgets qui décrivent graphiquement les types d’informations suivants :

- Trafic par port
- Trafic principal par port
- Bande passante du canal
- Bande passante totale
- Connexion TCP active
- Bande passante supérieure par VLAN
- Appareils :
  - Nouveaux appareils
  - Appareils occupés
  - Appareils par fournisseur
  - Appareils par système d’exploitation
  - Nombre d’appareils par VLAN
  - Appareils déconnectés
- Pertes de connectivité par heure
- Alertes pour les incidents par type
- Accès à la table de base de données
- Widgets de dissection de protocole
- DELTAV
  - Distribution des opérations roc DeltaV
  - Événements roc DeltaV par nom
  - Événements DeltaV par heure
- AMS
  - Trafic AMS par port du serveur
  - Trafic AMS par commande
- Ethernet et adresse IP :
  - Trafic d’adresses Ethernet et IP par service CIP
  - Trafic d’adresses Ethernet et IP par classe CIP
  - Trafic d’adresses Ethernet et IP par commande
- OPC :
  - Principales opérations de gestion OPC
  - Principales opérations d’E/S OPC
- Siemens S7 :
  - Trafic S7 par fonction de contrôle
  - Trafic S7 par sous-fonction
- VLAN
  - Nombre d’appareils par VLAN
  - Bande passante supérieure par VLAN
- 60870-5-104
  - Trafic IEC-60870 par ASDU
- BACNET
  - Services BACnet
- DNP3
  - Trafic DNP3 par fonction
- SRTP :
  - Trafic SRTP par code de service
  - Erreurs SRTP par jour
- SuiteLink :
  - Principales balises SuiteLink interrogées
  - Comportement de la balise numérique SuiteLink
- Trafic IEC-60870 par ASDU
- Trafic DNP3 par fonction
- Trafic MMS par service
- Trafic Modbus par fonction
- Trafic OPC-UA par service

> [!NOTE]
>  L’heure dans les widgets est définie en fonction de l’heure du capteur.

## <a name="create-reports"></a>Créer des rapports

Pour afficher les tableaux de bord et les widgets :

Sélectionnez **Tendances et statistiques** dans le menu latéral.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Capture d’écran d’une investigation.":::

Par défaut, les résultats sont affichés pour les détections au cours des 7 derniers jours. Vous pouvez utiliser les outils de filtre pour modifier cette plage. Pat exemple, une recherche de texte libre.

## <a name="see-also"></a>Voir aussi

[Rapports d’évaluation des risques](how-to-create-risk-assessment-reports.md)
[Requêtes d’exploration de données des capteurs](how-to-create-data-mining-queries.md)
[Création de rapports de vecteur d'attaque](how-to-create-attack-vector-reports.md)