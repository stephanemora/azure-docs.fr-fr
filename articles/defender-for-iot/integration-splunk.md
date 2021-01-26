---
title: À propos de l’intégration de Splunk
titleSuffix: Azure Defender for IoT
description: Pour répondre à un manque de visibilité sur la sécurité et la résilience des réseaux OT, Defender pour IoT a développé Defender pour IoT, IIoT, et l’application de surveillance des menaces ICS pour Splunk, une intégration native entre Defender pour IoT et Splunk qui permet une approche unifiée de sécurité informatique et OT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556661"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Defender pour IoT et application de surveillance des menaces ICS pour Splunk

Defender pour IoT atténue le risque IIoT, ICS et SCADA avec des moteurs d’apprentissage automatique brevetés et prenant en charge ICS, qui fournissent des insights immédiats sur les appareils, les vulnérabilités et les menaces ICS en moins d’une heure et sans s’appuyer sur des agents, des règles ou des signatures, des compétences spécialisées ou une connaissance préalable de l’environnement.

Pour répondre à un manque de visibilité sur la sécurité et la résilience des réseaux OT, Defender pour IoT a développé Defender pour IoT, IIoT, et l’application de surveillance des menaces ICS pour Splunk, une intégration native entre Defender pour IoT et Splunk qui permet une approche unifiée de sécurité informatique et OT.

> [!Note]
> Les références à CyberX concernent Azure Defender pour IoT.

## <a name="about-the-splunk-application"></a>À propos de l’application Splunk

L’application fournit aux analystes SOC une visibilité multidimensionnelle sur les protocoles OT et les appareils IoT spécialisés déployés dans des environnements industriels, ainsi qu’une analytique des comportements prenant en charge ICS pour détecter rapidement les comportements suspects ou anormaux. L’application active également la réponse aux incidents IT et OT au sein d’un service SOC d’entreprise. Il s’agit d’une évolution importante en raison de la convergence continue des secteurs IT et OT à prendre en charge de nouvelles initiatives IIoT, telles que les machines intelligentes et l’intelligence en temps réel.

L’application Splunk peut être installée localement ou exécutée sur un cloud. L’intégration à Defender pour IoT prend en charge les deux déploiements.

## <a name="about-the-integration"></a>À propos de l’intégration

L’intégration de Defender pour IoT et Splunk via l’application native permet aux utilisateurs de :

- Réduire le temps nécessaire aux organisations dotées d’une infrastructure industrielle et critique pour détecter, examiner les cybermenaces et agir en conséquence.

- Obtenir un renseignement en temps réel sur les risques OT.

- Mettre en corrélation les alertes Defender pour IoT avec les référentiels Splunk Enterprise Security Threat Intelligence.

- Surveiller et répondre depuis un volet unique.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Page principale de l’outil Splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Page des alertes dans Splunk.":::

L’application permet aux administrateurs Splunk d’analyser les alertes OT que Defender envoie, et de surveiller l’ensemble du déploiement de sécurité, y compris les détails suivants :

- Parmi les cinq moteurs d’analyse, celui qui a détecté l’alerte.

- Le protocole qui a généré l’alerte.

- Le capteur Defender pour IoT qui a généré l’alerte.

- Le niveau de gravité de l’alerte.

- La source et la destination de la communication.

## <a name="requirements"></a>Conditions requises

### <a name="version-requirements"></a>Configuration requise pour la version

Les versions suivantes sont requises.

- Defender pour IoT versions 2.4 et ultérieures.

- Splunkbase versions 11 et ultérieures.

- Splunk Enterprise versions 7.2 et ultérieures.
  
## <a name="download-the-application"></a>Télécharger l’application

Téléchargez l’application *CyberX ICS Threat Monitoring for Splunk* à partir de [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Spécifications relatives aux autorisations Splunk

L’autorisation Splunk suivante est requise :

- Toute personne disposant des autorisations du rôle utilisateur *admin*.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Envoyer les alertes Defender pour IoT à Splunk

Les alertes Defender pour IoT fournissent des informations sur un large éventail d’événements de sécurité, notamment :

- Écarts par rapport à l’activité réseau de ligne de base acquise.

- Détections de programmes malveillants.

- Détections basées sur des changements opérationnels suspects.

- Anomalies réseau.

- Écarts de protocole par rapport aux spécifications de protocole.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="L’écran des détections.":::

Vous pouvez configurer Defender pour IoT afin d’envoyer des alertes au serveur Splunk, où les informations concernant les alertes s’affichent dans le tableau de bord Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Affichez toutes les alertes et leurs détails.":::

Les informations d’alerte suivantes sont envoyées au serveur Splunk.

- Date et heure de l’alerte.

- Moteur Defender pour IoT qui a détecté l’événement : Violation de protocole, Violation de stratégie, Programme malveillant, Anomalie, ou Opérationnelle.

- Titre de l’alerte.

- Message d’alerte.

- Gravité de l’alerte : Avertissement, Mineur, Majeur ou Critique.

- Nom de l’appareil source.

- Adresse IP de l’appareil source.

- Nom de l’appareil de destination.

- Adresse IP de l’appareil de destination.

- Adresse IP de la plateforme Defender pour IoT (hôte).

- Nom d’appliance de la plateforme Defender pour IoT (type de source).

Voici un exemple de sortie obtenue :

| Temps | Événement |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Alerte de plateforme Defender pour IoT** : un appareil a été arrêté par une commande PLC<br /><br />**Type** : Violation opérationnelle <br /><br />**Gravité** : Majeure <br /><br />**Nom de la source** : my_device1 <br /><br />**IP source** : 192.168.110.131 <br /><br />**Nom de la destination** : my_device2<br /><br /> **IP de destination** : 10.140.33.238 <br /><br />**Message** : Un périphérique réseau a été arrêté à l’aide d’une commande PLC Stop. Ce périphérique ne fonctionne pas tant qu’une commande de démarrage n’a pas été envoyée. 192.168.110.131 a été arrêté par 10.140.33.238 (un appareil Siemens S7), à l’aide d’une commande PLC Stop.<br /><br />**Hôte** : 192.168.90.43 <br /><br />**Sourcetype** : Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Définir les règles de transfert des alertes

Utilisez la fonctionnalité *Forwarding Rules* (Règles de transfert) de Defender pour IoT pour envoyer des informations d’alerte aux serveurs Splunk.

Des options sont disponibles pour personnaliser les règles d’alerte en fonction des éléments suivants :

- Protocoles spécifiques détectés.

- Gravité de l’événement.

- Moteur Defender pour IoT qui détecte les événements.

Pour créer une règle de transfert :

1. À partir du capteur ou du volet de gauche de la console de gestion locale, sélectionnez **Forwarding** (Transfert).

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Sélectionnez le bouton bleu Create Forwarding Alert"::: (Créer une alerte de transfert).

1. Sélectionnez **Create Forwarding Rules** (Créer des règles de transfert). Dans la fenêtre **Create Forwarding Rule** (Créer une règle de transfert), définissez les paramètres de la règle.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Créez les règles pour votre règle de transfert.":::

    | Paramètre | Description |
    |--|--|
    | **Nom** | Nom de la règle de transfert. |
    | **Select Severity** (Sélectionner une gravité) | Incident de niveau de sécurité minimal à transférer. Par exemple, si Minor (Mineur) est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Protocoles** | Par défaut, tous les protocoles sont sélectionnés. Pour sélectionner un protocole spécifique, sélectionnez **Specific** (Spécifique), puis sélectionnez le protocole pour lequel cette règle est appliquée. |
    | **Engines** (Moteurs) | Par défaut, tous les moteurs de sécurité sont impliqués. Pour sélectionner un moteur de sécurité spécifique auquel cette règle est appliquée, sélectionnez **Specific** (Spécifique), puis sélectionnez le moteur. |
    | **System Notifications** (Notifications système) | État en ligne/hors connexion du capteur de transfert. Cette option est disponible uniquement si vous êtes connecté à Central Manager. |                                            |

1. Afin d’indiquer à Defender pour IoT d’envoyer les informations sur des éléments à Splunk, sélectionnez **Action**, puis **Send to Splunk Server** (Envoyer au serveur Splunk).

1. Entrez les paramètres Splunk suivants.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Les paramètres Splunk que vous devez entrer sur cet écran.":::

    | Paramètre | Description |
    |--|--|
    | **Hôte** | Adresse du serveur Splunk |
    | **Port** | 8089 |
    | **Nom d’utilisateur** | Nom d’utilisateur du serveur Splunk |
    | **Mot de passe** | Mot de passe du serveur Splunk |

1. Sélectionnez **Envoyer**

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
