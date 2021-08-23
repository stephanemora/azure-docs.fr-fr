---
title: À propos de l’intégration de ServiceNow
description: L’application ICS Management Defender pour IoT pour ServiceNow fournit aux analystes SOC une visibilité multidimensionnelle sur les protocoles OT et les appareils IoT spécialisés déployés dans des environnements industriels, ainsi qu’une analytique des comportements prenant en charge ICS pour détecter rapidement les comportements suspects ou anormaux.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015230"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>Application ICS Management Defender pour IoT pour ServiceNow

L’application ICS Management Defender pour IoT pour ServiceNow fournit aux analystes SOC une visibilité multidimensionnelle sur les protocoles OT et les appareils IoT spécialisés déployés dans des environnements industriels, ainsi qu’une analytique des comportements prenant en charge ICS pour détecter rapidement les comportements suspects ou anormaux. Il s’agit d’une évolution importante en raison de la convergence continue des secteurs IT et OT à prendre en charge de nouvelles initiatives IoT, telles que les machines intelligentes et l’intelligence en temps réel.

L’application active également la réponse aux incidents IT et OT au sein d’un service SOC d’entreprise.

## <a name="about-defender-for-iot"></a>À propos de Defender pour IoT

Defender pour IoT offre la seule plateforme de cybersécurité ICS et IoT, créée par des experts de l’équipe bleue disposant d’une solide expérience dans la défense de l’infrastructure nationale critique, et la seule plateforme offrant une analytique des menaces prenant en charge ICS et le Machine Learning. Defender pour IoT offre les fonctionnalités suivantes :

- Insights immédiats sur ICS dans le paysage de l’appareil avec un large éventail de détails sur les attributs.

- Connaissance intégrée prenant en charge ICS sur les protocoles, les appareils, les applications OT et leur comportement.

- Insights immédiats sur les vulnérabilités et menaces zero-day connues.

- Technologie de modélisation des menaces ICS automatisée pour prédire les chemins les plus probables des attaques ICS ciblées via une analytique propriétaire.

> [!Note]
> Les références à CyberX concernent Azure Defender pour IoT.

## <a name="about-the-integration"></a>À propos de l’intégration

L’intégration de Defender pour IoT avec ServiceNow offre un nouveau niveau de visibilité, de surveillance et de contrôle centralisés pour le paysage IoT et OT. Ces plateformes reliées par un pont activent la visibilité automatisée des appareils et la gestion des menaces pour les appareils ICS et IoT qui n’étaient pas accessibles précédemment.

### <a name="threat-management"></a>Gestion des menaces

L’application ICS Management Defender pour IoT permet ce qui suit :

- Réduire le temps nécessaire aux organisations dotées d’une infrastructure industrielle et critique pour détecter, examiner les cybermenaces et agir en conséquence.

- Obtenir un renseignement en temps réel sur les risques OT.

- Mettre en corrélation les alertes Defender pour IoT avec les flux de travail de surveillance des menaces et de gestion des incidents ServiceNow.

- Déclencher des tickets et des flux de travail ServiceNow avec d’autres services et applications sur la plateforme ServiceNow.

Les menaces de sécurité ICS et SCADA sont identifiées par les moteurs de sécurité Defender pour IoT, qui fournissent une réponse immédiate aux attaques de programmes malveillants, aux écarts de stratégie de la sécurité et du réseau, ainsi qu’aux anomalies opérationnelles et de protocole. Pour plus d’informations sur les détails des alertes envoyés à ServiceNow, consultez [Rapports d’alerte](#alert-reporting).

### <a name="device-visibility-and-management"></a>Visibilité et gestion des appareils

La base de données de gestion de la configuration (CMDB) de ServiceNow est enrichie et complétée par un ensemble complet d’attributs d’appareil envoyés (Push) par la plateforme Defender pour IoT. Cela garantit une visibilité complète et continue sur le paysage des appareils et vous permet de surveiller et de répondre en un même endroit. Pour plus d’informations sur les attributs d’appareil envoyés à ServiceNow, consultez [Afficher les détections Defender pour IoT dans ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Configuration système requise et architecture

Cet article aborde les points suivants :

- **Configuration logicielle requise**  
- **Architecture**

## <a name="software-requirements"></a>Configuration logicielle requise

- ServiceNow Service Management version 3.0.2

- Correctif Defender pour IoT 2.8.11.1 ou versions ultérieures

> [!Note]
> Si vous utilisez déjà une intégration Defender pour IoT et ServiceNow et que vous effectuez une mise à niveau à l’aide de la console de gestion locale, les données précédemment reçues des capteurs Defender pour IoT devraient être effacées dans ServiceNow.

## <a name="architecture"></a>Architecture

### <a name="on-premises-management-console-architecture"></a>Architecture de la console de gestion locale

La console de gestion locale fournit une source unifiée pour toutes les informations sur les appareils et les alertes envoyées à ServiceNow.

Vous pouvez configurer une console de gestion locale pour communiquer avec une instance de ServiceNow. La console de gestion locale envoie (Push) les données de capteur à l’application Defender pour IoT à l’aide de l’API REST.

Si vous configurez votre système pour qu’il fonctionne avec une console de gestion locale, désactivez les configurations de la synchronisation ServiceNow, des règles de transfert et du proxy dans les capteurs, le cas échéant.

Ces configurations doivent être configurées pour la console de gestion locale. Les instructions de configuration sont décrites dans cet article.

### <a name="sensor-architecture"></a>Architecture du capteur

Si vous souhaitez configurer votre environnement pour inclure une communication directe entre les capteurs et ServiceNow, pour chaque capteur, définissez la configuration de la synchronisation ServiceNow, des règles de transfert et du proxy (si un proxy est requis).

Il est recommandé de configurer votre intégration à l’aide de la console de gestion locale pour communiquer avec ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Créer des jetons d’accès dans ServiceNow

Cet article explique comment créer un jeton d’accès dans ServiceNow. Le jeton est nécessaire pour communiquer avec Defender pour IoT.

Vous aurez besoin de **l’ID client** et du **secret client** lors de la création des règles de transfert Defender pour IoT, qui transfèrent les informations d’alerte à ServiceNow, et lors de la configuration de Defender pour IoT pour envoyer (Push) les attributs d’appareil aux tables ServiceNow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configurer Defender pour IoT pour qu’il communique avec ServiceNow

Cet article explique comment configurer Defender pour IoT pour qu’il communique avec ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Envoyer des informations d’alerte Defender pour IoT

Cet article explique comment configurer Defender pour IoT afin qu’il envoie (Push) les informations d’alerte aux tables ServiceNow. Pour plus d’informations sur les données d’alerte envoyées, consultez [Rapports d’alerte](#alert-reporting).

Les alertes Defender pour IoT apparaissent dans ServiceNow en tant qu’incidents de sécurité.

Définissez une règle de *transfert* Defender pour IoT afin d’envoyer des informations d’alerte à ServiceNow.

Pour définir la règle :

1. Dans le volet gauche de Defender pour IoT, sélectionnez **Transfert**.  

1. Sélectionnez :::image type="content" source="media/integration-servicenow/plus.png" alt-text="le bouton de l’icône Plus."::: . La boîte de dialogue Créer une règle de transfert s’ouvre.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Créer une règle de transfert":::

1. Entrez un nom pour la règle.

1. Définissez les critères selon lesquels Defender pour IoT déclenchera la règle de transfert. L’utilisation de critères de règle de transfert permet d’identifier et de gérer le volume d’informations envoyé à ServiceNow par Defender pour IoT. Les options suivantes sont disponibles :

    - **Niveaux de gravité :** Incident de niveau de sécurité minimal à transférer. Par exemple, si l’option **Minor** (Mineur) est sélectionnée, les alertes mineures et toute alerte avec un niveau de gravité supérieur seront transférées. Les niveaux sont prédéfinis par Defender pour IoT.

    - **Protocoles :** Déclenche uniquement la règle de transfert si le trafic détecté s’exécutait sur des protocoles spécifiques. Sélectionnez les protocoles requis dans la liste déroulante ou sélectionnez-les tous.

    - **Moteurs :** Sélectionnez les moteurs requis ou choisissez-les tous. Les alertes des moteurs sélectionnés seront envoyées.

1. Vérifiez que l’option **Notifications d’alerte de rapport** est sélectionnée.

1. Dans la section Actions, sélectionnez **Ajouter** puis **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Sélectionnez ServiceNow dans les options de la liste déroulante.":::

1. Entrez les paramètres d’action de ServiceNow :

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Renseignez les paramètres d’action de ServiceNow":::

1. Dans le volet **Actions**, définissez les paramètres suivants :

  | Paramètre | Description |
  |--|--|
  | Domain | Entrez l’adresse IP du serveur ServiceNow. |
  | Nom d’utilisateur | Entrez le nom d’utilisateur du serveur ServiceNow. |
  | Mot de passe | Entrez le mot de passe du serveur ServiceNow. |
  | ID client | Entrez l’ID client que vous avez reçu pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
  | Clé secrète client | Entrez la chaîne de secret client que vous avez créée pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
  | Type de rapport | **Incidents** : Transférer une liste des alertes présentées dans ServiceNow avec un ID d’incident et une brève description de chaque alerte.<br /><br />**Application Defender pour IoT** : Transférer toutes les informations d’alerte, notamment les détails sur le capteur, le moteur, les adresses source et de destination. Les informations sont transférées à Defender pour IoT sur l’application ServiceNow. |

1. Sélectionnez **SAVE** (Enregistrer). Les alertes Defender pour IoT apparaissent en tant qu’incidents dans ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Envoyer des attributs d’appareil Defender pour IoT

Cet article explique comment configurer Defender pour IoT afin qu’il envoie (Push) un ensemble étendu d’attributs d’appareil aux tables ServiceNow. Pour plus d’informations sur le type d’informations envoyées (Push) à ServiceNow, consultez ***Informations de l’inventaire***.

Pour envoyer des attributs à ServiceNow, vous devez mapper votre console de gestion locale à une instance ServiceNow. Cela garantit que la plateforme Defender pour IoT peut communiquer et s’authentifier auprès de l’instance.

Pour ajouter une instance ServiceNow :

1. Connectez-vous à votre console de gestion locale Defender pour IoT.

1. Sélectionnez **Paramètres système**, puis **ServiceNow** dans la section Intégration de la console de gestion locale.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Sélectionnez le bouton ServiceNow.":::

1. Entrez les paramètres de synchronisation suivants dans la boîte de dialogue de synchronisation ServiceNow.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="Boîte de dialogue de synchronisation ServiceNow.":::

     Paramètre | Description |
    |--|--|
    | Activer la synchronisation | Activez et désactivez la synchronisation après avoir défini les paramètres. |
    | Fréquence de synchronisation (minutes) | Par défaut, les informations sont envoyées (Push) à ServiceNow toutes les 60 minutes. La valeur minimale est 5 minutes. |
    | Instance ServiceNow | Entrez l’URL de l’instance ServiceNow. |
    | ID client | Entrez l’ID client que vous avez reçu pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
    | Clé secrète client | Entrez la chaîne de secret client que vous avez créée pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
    | Nom d’utilisateur | Entrez le nom d’utilisateur pour cette instance. |
    | Mot de passe | Entrez le mot de passe pour cette instance. |

1. Sélectionnez **SAVE** (Enregistrer).

## <a name="verify-communication"></a>Vérifier la communication

Vérifiez que la console de gestion locale est connectée à l’instance ServiceNow en examinant la date de la *dernière synchronisation*.

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Vérifiez que la communication a eu lieu en examinant la dernière synchronisation.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Configurer les intégrations à l’aide du proxy HTTPS

Lors de la configuration de l’intégration de Defender pour IoT et ServiceNow, la console de gestion locale et le serveur ServiceNow communiquent à l’aide du port 443. Si le serveur ServiceNow se trouve derrière le proxy, le port par défaut ne peut pas être utilisé.

Defender pour IoT prend en charge un proxy HTTPS dans l’intégration ServiceNow en permettant la modification du port par défaut utilisé pour l’intégration.

Pour configurer le proxy :

1. Modifiez les propriétés générales dans la console de gestion locale :  
    `sudo vim /var/cyberx/properties/global.properties`

2. Ajoutez les paramètres suivants :

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Enregistrez et quittez.

4. Exécutez la commande suivante : `sudo monit restart all`

Après la configuration, toutes les données ServiceNow sont transférées à l’aide du proxy configuré.

## <a name="download-the-defender-for-iot-application"></a>Télécharger l’application Defender pour IoT

Cet article explique comment télécharger l’application.

Pour accéder à l’application Defender pour IoT :

1. Accédez à <https://store.servicenow.com/>.

2. Recherchez `Defender for IoT` ou `CyberX IoT/ICS Management`.

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Recherchez CyberX dans la barre de recherche.":::

3. Sélectionnez l’application.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Sélectionnez l’application dans la liste.":::

4. Sélectionnez **Demander l’application.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Connectez-vous à l’application à l’aide de vos informations d’identification.":::

5. Connectez-vous et téléchargez l’application.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Afficher les détections Defender pour IoT dans ServiceNow

Cet article décrit les attributs d’appareil et les informations d’alerte présentés dans ServiceNow.

Pour afficher les attributs d’appareil :

1. Connectez-vous à ServiceNow.

2. Accédez à **Plateforme CyberX**.

3. Accédez à **Inventaire** ou **Alerte**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventaire ou alerte":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Informations d’inventaire

La base de données de gestion de la configuration (CMDB) est enrichie et complétée par les données envoyées à ServiceNow par Defender pour IoT. Avec l’ajout ou la mise à jour des attributs d’appareil dans les tables d’éléments de la configuration CMDB de ServiceNow, Defender pour IoT peut déclencher les règles métier et les flux de travail ServiceNow.

Les informations suivantes sont disponibles :

- Attributs d’appareil, par exemple, l’adresse MAC, le système d’exploitation, le fournisseur ou le protocole détecté de l’appareil.

- Informations sur le microprogramme, par exemple la version du microprogramme et le numéro de série.

- Informations sur l’appareil connecté, par exemple la direction du trafic entre la source et la destination.

### <a name="devices-attributes"></a>Attributs des appareils

Cet article décrit les attributs d’appareil envoyés (Push) à ServiceNow.

| Élément | Description |
|--|--|
| Appliance | Nom du capteur ayant détecté le trafic. |
| id | ID d’appareil attribué par Defender pour IoT. |
| Nom | Le nom de l'appareil. |
| Adresse IP | Adresse(s) IP de l’appareil. |
| Type | Type d’appareil, par exemple commutateur, PLC, historien ou contrôleur de domaine. |
| Adresse MAC | Adresse(s) MAC de l’appareil. |
| Système d’exploitation | Système d’exploitation de l’appareil. |
| Fournisseur | Le fournisseur de l’appareil. |
| Protocoles | Les protocoles détectés dans le trafic généré par l’appareil. |
| Propriétaire | Entrez le nom du propriétaire de l’appareil. |
| Emplacement | Entrez l’emplacement physique de l’appareil. |

Affichez les appareils connectés à un appareil dans cette vue.

Pour afficher les appareils connectés :

1. Sélectionnez un appareil, puis l’option **Appliance** répertoriée pour cet appareil.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Sélectionnez l’appliance souhaitée dans la liste.":::

1. Dans la boîte de dialogue **Détails de l’appareil**, sélectionnez **Appareils connectés**.

### <a name="firmware-details"></a>Détails du microprogramme

Cet article décrit les informations sur le microprogramme de l’appareil envoyées (Push) à ServiceNow.

| Élément | Description |
|--|--|
| Appliance | Nom du capteur ayant détecté le trafic. |
| Appareil | Le nom de l'appareil. |
| Adresse | L’adresse IP de l’appareil. |
| Adresse du module | Le modèle d’appareil et le numéro ou l’ID de l’emplacement. |
| Série | Le numéro de série de l’appareil. |
| Modèle | Numéro de modèle de l’appareil. |
| Version | Le numéro de version du microprogramme. |
| Données supplémentaires | Données supplémentaires sur le microprogramme, telles que définies par le fournisseur, par exemple le type d’appareil. |

### <a name="connection-details"></a>Détails de la connexion

Cet article décrit les informations sur la connexion de l’appareil envoyées (Push) à ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Informations sur la connexion de l’appareil":::

| Élément | Description |
|--|--|
| Appliance | Nom du capteur ayant détecté le trafic. |
| Sens | Direction du trafic. <br /> <br /> - **Sens unique** indique que la destination est le serveur et que la source est le client. <br /> <br /> - **Bidirectionnelle** indique que la source et la destination sont des serveurs, ou que le client est inconnu. |
| ID d’appareil source | Adresse IP de l’appareil qui a communiqué avec l’appareil connecté. |
| Nom de l’appareil source | Nom de l’appareil qui a communiqué avec l’appareil connecté. |
| ID de l’appareil de destination | Adresse IP de l’appareil connecté. |
| Nom de l’appareil de destination | Nom de l’appareil connecté. |

## <a name="alert-reporting"></a>Rapports d’alerte

Les alertes sont déclenchées lorsque les moteurs Defender pour IoT détectent des changements qui nécessitent votre attention dans le comportement et le trafic réseau. Pour plus d’informations sur les types d’alertes générés par chaque moteur, consultez [À propos des moteurs d’alerte](#about-alert-engines).

Cet article décrit les informations sur les alertes d’appareil envoyées (Push) à ServiceNow.

| Élément | Description |
|--|--|
| Date de création | Heure et date auxquelles l’alerte a été générée. |
| Moteur | Moteur ayant détecté l’événement. |
| Intitulé | Titre de l’alerte. |
| Description | Description de l’alerte. |
| Protocol | Protocole détecté dans le trafic. |
| severity | Gravité de l’alerte définie par Defender pour IoT. |
| Appliance | Nom du capteur ayant détecté le trafic. |
| Nom de la source | Nom de la source. |
| Adresse IP source| Adresse IP de la source. |
| Nom de la destination | Nom de la destination. |
| Adresse IP de destination | Adresse IP de la destination. |
| Destinataire | Entrez le nom de la personne attribuée au ticket. |

### <a name="updating-alert-information"></a>Mise à jour des informations d’alerte

Sélectionnez l’entrée dans la colonne créée pour afficher les informations d’alerte dans un formulaire. Vous pouvez mettre à jour les détails de l’alerte et attribuer l’alerte à une personne qui devra l’examiner et la gérer.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Afficher les informations relatives à l’alerte.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>À propos des moteurs d’alerte

Cet article décrit le type d’alertes déclenchées par chaque moteur.

| Type d’alerte | Description |
|--|--|
| Alertes de violation de stratégie | Déclenchées lorsque le moteur de violation de stratégie détecte un écart par rapport au trafic précédemment appris. Par exemple : <br /><br />- un nouvel appareil est détecté <br /><br />- une nouvelle configuration est détectée sur un appareil <br /><br />- un appareil non défini en tant que périphérique de programmation effectue une modification de programmation <br /><br />- une version de microprogramme est modifiée. |
| Alertes de violation de protocole | Déclenchées lorsque le moteur de violation du protocole détecte des structures de paquets ou des valeurs de champ qui ne sont pas conformes avec la spécification du protocole. |
| Alertes opérationnelles | Déclenchées lorsque le moteur opérationnel détecte des incidents opérationnels réseau ou un appareil défectueux. Par exemple, un périphérique réseau a été arrêté à l’aide d’une commande Stop PLC ou une interface sur un capteur a cessé d’analyser le trafic. |
| Alertes de programmes malveillants | Déclenchées lorsque le moteur de programmes malveillants détecte une activité réseau malveillante, par exemple, des attaques connues comme Conficker. |
| Alertes concernant des anomalies | Déclenchées lorsque le moteur Anomalie détecte un écart. Par exemple, un appareil effectue une analyse réseau, mais n’est pas défini en tant qu’appareil d’analyse. |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
