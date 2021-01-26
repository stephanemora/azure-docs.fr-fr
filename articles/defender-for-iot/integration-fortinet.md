---
title: À propos de l’intégration de Fortinet
titleSuffix: Azure Defender for IoT
description: Defender pour IoT et Fortinet ont établi un partenariat afin de détecter et de bloquer les attaques sur les réseaux IoT et ICS.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 313db90d4c9be30ef588b00caf1d6e4ce32b113b
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556657"
---
# <a name="defender-for-iot-and-fortinet-iiot-and-ics-threat-detection--prevention"></a>Détection et prévention des menaces IIoT et ICS par Defender pour IoT et Fortinet

Defender pour IoT atténue le risque IIoT, ICS et SCADA avec des moteurs d’auto-apprentissage brevetés et prenant en charge ICS, qui fournissent des insights immédiats sur les appareils, les vulnérabilités et les menaces ICS en moins d’une heure et sans s’appuyer sur des agents, des règles ou des signatures, des compétences spécialisées ou une connaissance préalable de l’environnement.

Defender pour IoT et Fortinet ont établi un partenariat afin de détecter et de bloquer les attaques sur les réseaux IoT et ICS.

Ensemble, Fortinet et Defender pour IoT empêchent les éléments suivants :

- Changements non autorisés apportés aux contrôleurs logiques programmables.

- Programmes malveillants qui manipulent les appareils ICS et IoT par le biais de leurs protocoles natifs.
- Outils de reconnaissance issus de la collecte de données.
- Violations de protocole provoquées par des configurations incorrectes ou des attaquants malveillants.

## <a name="defender-for-iot-and-fortigate-joint-solution"></a>Solution conjointe Defender pour IoT et FortiGate

Defender pour IoT détecte un comportement anormal dans les réseaux IoT et ICS, et fournit ces informations à FortiGate et FortiSIEM, comme suit :

- **Visibilité :** Les informations fournies par Defender pour IoT permettent aux administrateurs FortiSIEM d’avoir une visibilité sur les réseaux IoT et ICS auparavant invisibles.

- **Blocage des attaques malveillantes :** Les administrateurs FortiGate peuvent utiliser les informations découvertes par Defender pour IoT afin de créer des règles destinées à faire cesser le comportement anormal, qu’il soit causé par des acteurs chaotiques ou des appareils mal configurés, avant qu’il entraîne des dommages pour la production, les bénéfices ou les personnes.

## <a name="the-defender-for-iot-platform"></a>Plateforme Defender pour IoT

La plateforme Defender pour IoT découvre automatiquement tous les appareils IoT et ICS non gérés et prend leur empreinte, tout en supervisant en continu les attaques ciblées et les logiciels malveillants. Les fonctionnalités de gestion des risques et des vulnérabilités incluent la modélisation automatisée des menaces, ainsi que la génération de rapports complets sur les vulnérabilités des points de terminaison et du réseau, avec des recommandations d’atténuation axées sur les risques.  

La plateforme Defender pour IoT est sans agent, non intrusive et facile à déployer, et fournit des insights actionnables moins d’une heure après la connexion au réseau.

## <a name="fortinet-fortisiem"></a>Fortinet FortiSIEM

Une sécurité efficace demande une visibilité de tous les appareils et de l’ensemble de l’infrastructure en temps réel, mais également avec un contexte. Les appareils qui représentent une menace, leur capacité à vous permettre de gérer la menace à laquelle sont confrontées les entreprises, et non le bruit que créent plusieurs outils de sécurité.

Les points de terminaison, IoT, l’infrastructure, les outils de sécurité, les applications, les machines virtuelles et le cloud sont des éléments que les administrateurs doivent sécuriser et superviser en permanence.

FortiSIEM, et la solution multifournisseur SIEM (Security Information and Event Management, gestion des informations de sécurité et des événements) de Fortinet, regroupent la visibilité, la corrélation, la réponse automatisée et la correction dans une solution unique et scalable.

À l’aide d’une vue Services aux entreprises, la complexité de la gestion des opérations réseau et de sécurité est réduite, ce qui permet de libérer des ressources et d’améliorer la détection des violations. FortiSIEM fournit une corrélation croisée tout en appliquant le machine learning et UEBA pour améliorer la réponse, afin d’arrêter les violations avant qu’elles ne se produisent.

## <a name="fortinet-fortigate-next-generation-firewalls"></a>Pare-feu de nouvelle génération Fortinet FortiGate

Les pare-feu de nouvelle génération (NGFW, Next Generation Firewall) de FortiGate utilisent des processeurs de sécurité spécialement conçus et des services de sécurité et d’informations sur les menaces provenant de labos FortiGuard basés sur l’intelligence artificielle pour offrir une protection optimale, et une inspection haute performance du trafic en texte clair et chiffré.

Les pare-feu de nouvelle génération réduisent les coûts et la complexité grâce à une visibilité complète des applications, des utilisateurs et des réseaux, et offrent une sécurité de pointe. En tant que partie intégrante de Fortinet Security Fabric, les pare-feu de nouvelle génération peuvent communiquer dans la gamme complète de produits de sécurité de Fortinet et les solutions de sécurité partenaires dans un environnement multifournisseur pour partager les informations sur les menaces et améliorer la posture de sécurité.

## <a name="use-cases"></a>Cas d'utilisation

### <a name="prevent-unauthorized-changes-to-programmable-logic-controllers"></a>Empêcher les changements non autorisés apportés aux contrôleurs logiques programmables

Les organisations utilisent des contrôleurs logiques programmables (PLC) pour gérer des processus physiques comme des bras robotisés dans des usines, des turbines en rotation dans des champs d’éoliennes et des centrifugeuses dans des centrales nucléaires.

Une mise à jour de la logique d’échelle ou du microprogramme d’un contrôleur PLC peut représenter une activité légitime ou une tentative de compromission de l’appareil en insérant du code malveillant.  Defender pour IoT peut détecter les changements non autorisés apportés aux contrôleurs PLC, puis fournir des informations sur ces changements à FortiSIEM et à FortiGate. Grâce à ces informations, les administrateurs FortiSIEM peuvent décider comment limiter au mieux l’impact sur la solution. Une option d’atténuation consisterait à créer, dans FortiGate, une règle qui interrompt la communication avec l’appareil affecté.

### <a name="stop-ransomware-before-it-damages-iot-and-ics-networks"></a>Arrêter le ransomware avant qu’il n’endommage les réseaux IoT et ICS

Defender pour IoT supervise en permanence les réseaux IoT et ICS pour voir s’ils présentent des comportements provoqués par des ransomware comme LockerGoga, WannaCry et NotPetya. Quand il est intégré à FortiSIEM et à FortiGate, Defender pour IoT peut fournir des informations sur la présence de ces types de ransomware pour permettre aux opérateurs FortiSIEM de voir où se trouvent les logiciels malveillants, et aux administrateurs FortiGate d’empêcher le ransomware de se propager et de causer davantage de dégâts.

## <a name="set-sending-defender-for-iot-alerts-to-fortisiem"></a>Définir l’envoi d’alertes Defender pour IoT sur FortiSIEM

Les alertes Defenders pour IoT fournissent des informations sur un large éventail d’événements de sécurité, notamment :

- Écarts par rapport à l’activité réseau de ligne de base acquise
- Détections de programmes malveillants
- Détections basées sur des changements opérationnels suspects
- Anomalies réseau
- Écarts de protocole par rapport aux spécifications de protocole

:::image type="content" source="media/integration-fortinet/address-scan.png" alt-text="Capture d’écran de la fenêtre Address Scan Detected (Analyse des adresses détectée).":::

Vous pouvez configurer Defender pour IoT afin d’envoyer des alertes au serveur FortiSIEM, où les informations concernant les alertes s’affichent dans la fenêtre Analytics (Analytique) :

:::image type="content" source="media/integration-fortinet/analytics.png" alt-text="Capture d’écran de la fenêtre Analytics.":::

Chaque alerte Defender pour IoT est analysée sans aucune autre configuration côté FortiSIEM et elle est présentée dans FortiSIEM sous la forme d’un événement de sécurité. Les détails d’événement suivants s’affichent par défaut :

:::image type="content" source="media/integration-fortinet/event-detail.png" alt-text="Consultez les détails de vos événements dans la fenêtre Event Details (Détails de l’événement).":::

## <a name="define-alert-forwarding-rules"></a>Définir les règles de transfert des alertes

Utilisez la fonctionnalité Forwarding Rules (Règles de transfert) de Defender pour IoT pour envoyer des informations d’alerte à FortiSIEM.

Des options sont disponibles pour personnaliser les règles d’alerte en fonction des éléments suivants :

- Protocoles spécifiques détectés.

- Gravité de l’événement.

- Moteur Defender pour IoT qui détecte les événements.

Pour créer une règle de transfert

1. À partir du capteur ou du volet de gauche de la console de gestion locale, sélectionnez **Forwarding** (Transfert).

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="Consultez vos règles de transfert dans la fenêtre Forwarding (Transfert).":::](media/integration-fortinet/forwarding-view.png#lightbox)

2. Sélectionnez **Create Forwarding Rule** (Créer une règle de transfert). Dans la fenêtre **Create Forwarding Rule**, définissez les paramètres de votre règle.

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="Fenêtre Create Forwarding Rule.":::

    | Paramètre | Description |
    |--|--|
    | **Nom** | Nom de la règle de transfert. |
    | **Select Severity** (Sélectionner une gravité) | Incident de niveau de sécurité minimal à transférer. Par exemple, si **Minor** (Mineur) est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Protocoles** | Par défaut, tous les protocoles sont sélectionnés.<br><br>Pour sélectionner un protocole spécifique, sélectionnez **Specific**, puis sélectionnez le protocole pour lequel cette règle est appliquée. |
    | **Engines** (Moteurs) | Par défaut, tous les moteurs de sécurité sont impliqués.<br><br>Pour sélectionner un moteur de sécurité spécifique auquel cette règle est appliquée, sélectionnez **Specific**, puis sélectionnez le moteur. |
    | **System Notifications** (Notifications système) | État en ligne/hors connexion du capteur de transfert. Cette option est disponible uniquement si vous êtes connecté à la console de gestion locale. |

3. Pour indiquer à Defender pour IoT d’envoyer les informations d’alerte à FortiSIEM, sélectionnez **Action**, puis **Send to FortiSIEM** (Envoyer à FortiSIEM).

    :::image type="content" source="media/integration-fortinet/forward-rule.png" alt-text="Créez une règle de transfert, puis sélectionnez Send to FortiSIEM.":::

4. Entrez les détails du serveur FortiSIEM.

    :::image type="content" source="media/integration-fortinet/details.png" alt-text="Ajoutez les informations concernant FortiSIEM à la règle de transfert":::

    | Paramètre | Description |
    | --------- | ----------- |
    | **Hôte** | Adresse du serveur FortiSIEM. |
    | **Port** | Port du serveur FortiSIEM. |
    | **Fuseau horaire** | Horodatage de la détection d’alerte. |

5. Sélectionnez **Envoyer**.

## <a name="set-blocking-suspected-traffic-using-fortigate-firewall"></a>Définir le blocage du trafic suspect à l’aide du pare-feu FortiGate

Vous pouvez définir automatiquement des stratégies de blocage dans le pare-feu FortiGate en fonction d’alertes Defender pour IoT.

:::image type="content" source="media/integration-fortinet/firewall.png" alt-text="Vue de la fenêtre FortiGate Firewall.":::

Par exemple, l’alerte suivante peut bloquer la source malveillante :

:::image type="content" source="media/integration-fortinet/suspicion.png" alt-text="Fenêtre de suspicion de programme malveillant NotPetya":::

Pour définir une règle de pare-feu FortiGate qui bloque cette source malveillante :

1. Dans FortiGate, créez une clé API nécessaire pour la règle de transfert Defender pour IoT. Pour plus d’informations, consultez [Créer une clé API dans FortiGate](#create-an-api-key-in-fortigate).

1. Dans la fenêtre **Forwarding** (Transfert) de Defender pour IoT, définissez une règle de transfert qui bloque les alertes liées aux logiciels malveillants. Pour plus d’informations, consultez [Bloquer le trafic suspect à l’aide du pare-feu FortiGate](#block-suspected-traffic-using-the-fortigate-firewall).

1. Dans Defender pour IoT, les alertes (**Alerts**) bloquent une source malveillante. Pour plus d’informations, consultez [Bloquer la source suspecte](#block-the-suspicious-source).

    L’adresse de la source malveillante s’affiche dans la fenêtre **Administrator** de FortiGate.

   :::image type="content" source="media/integration-fortinet/administrator.png" alt-text="Vue de la fenêtre Administrator de FortiGate.":::

   La stratégie de blocage a été automatiquement créée et s’affiche dans la fenêtre **IPv4 Policy** (Stratégie IPv4) de FortiGate.

   :::image type="content" source="media/integration-fortinet/policy.png" alt-text="Vue de la fenêtre IPv4 Policy (Stratégie IPv4) de FortiGate.":::

   :::image type="content" source="media/integration-fortinet/edit.png" alt-text="Vue de la fenêtre d’édition de la stratégie IPv4 de FortiGate.":::

## <a name="create-an-api-key-in-fortigate"></a>Créer une clé API dans FortiGate

1. Dans FortiGate, sélectionnez **System** > **Admin Profiles** (Profils administrateur).

1. Créez un profil avec les autorisations suivantes :

    :::image type="content" source="media/integration-fortinet/admin-profile.png" alt-text="Description d’ordinateur générée automatiquement":::

1. Sélectionnez **System** > **Administrators** (Administrateurs), puis créez un administrateur d’API REST (**REST API Admin**).

    :::image type="content" source="media/integration-fortinet/cellphone.png" alt-text="Description de téléphone portable générée automatiquement":::

    | Paramètre | Description |
    | --------- | ----------- |
    | **Nom d’utilisateur** | Nom de la règle de transfert. |
    | **Commentaires** | Incident de niveau de sécurité minimal à transférer. Par exemple, si Minor (Mineur) est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Administrator Profile** (Profil administrateur) | Dans la liste déroulante, sélectionnez le nom de profil que vous avez défini à l’étape précédente. |
    | **PKI Group** (Groupe PKI) | Désactivez. |
    | **CORS Allow Origin** (Autoriser l’origine CORS) | Activer. |
    | **Restrict login to trusted hosts** (Limiter la connexion aux hôtes approuvés) | Ajoute l’adresse IP des capteurs et des CM qui se connecteront à FortiGate. |

1. Enregistrez la clé API.

    :::image type="content" source="media/integration-fortinet/api-key.png" alt-text="La description de téléphone portable génère automatiquement une nouvelle clé API":::

## <a name="block-suspected-traffic-using-the-fortigate-firewall"></a>Bloquer le trafic suspect à l’aide du pare-feu FortiGate

1. Dans le volet de gauche, sélectionnez **Forwarding** (Transfert).

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="Option de la fenêtre Forwarding (Transfert) dans un capteur.":::](media/integration-fortinet/forwarding-view.png#lightbox)

1. Sélectionnez **Create Forwarding Rule** (Créer une règle de transfert), puis définissez les paramètres de la règle.

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="Capture d’écran de la fenêtre Create Forwarding Rule":::

    | Paramètre | Description |
    | --------- | ----------- |
    | **Nom** | Nom de la règle de transfert. |
    | **Select Severity** (Sélectionner une gravité) | Incident de niveau de sécurité minimal à transférer. Par exemple, si **Minor** est sélectionné, les alertes mineures et toute alerte de niveau de gravité supérieur à ce niveau seront transférées. |
    | **Protocoles** | Par défaut, tous les protocoles sont sélectionnés.<br><br>Pour sélectionner un protocole spécifique, sélectionnez **Specific**, puis sélectionnez le protocole pour lequel cette règle est appliquée. |
    | **Engines** (Moteurs) | Par défaut, tous les moteurs de sécurité sont impliqués.<br><br>Pour sélectionner un moteur de sécurité spécifique auquel cette règle est appliquée, sélectionnez **Specific**, puis sélectionnez le moteur. |
    | **System Notifications** (Notifications système) | État *en ligne et hors connexion* du capteur de transfert. Cette option est disponible uniquement si vous êtes connecté à la console de gestion locale. |

1. Pour indiquer à Defender pour IoT d’envoyer les règles de pare-feu à FortiGate, sélectionnez **Action**, puis **Send to FortiGate** (Envoyer à FortiGate).

    :::image type="content" source="media/integration-fortinet/fortigate.png" alt-text="Send to FortiGate dans la fenêtre Create Forwarding Rule":::

1. Pour configurer la règle de transfert FortiGate :

    :::image type="content" source="media/integration-fortinet/configure.png" alt-text="Configure la fenêtre Create Forwarding Rule":::

1. Dans le volet **Actions**, définissez les paramètres suivants :

    | Paramètre | Description |
    |--|--|
    | Host | Type d’adresse IP du serveur FortiGate. |
    | Port | Type de port du serveur FortiGate. |
    | Nom d’utilisateur | Type de nom d’utilisateur du serveur FortiGate. |
    | Clé d’API | Entrez la clé API que vous avez créée dans FortiGate. |
    | Incoming Interface (Interface entrante)| Définissez la façon dont le blocage est exécuté :<br /><br />**Par adresse IP** : Crée toujours des stratégies de blocage sur Panorama en fonction de l’adresse IP.<br /><br />**Par nom de domaine complet ou adresse IP** : Crée des stratégies de blocage sur Panorama en fonction du nom de domaine complet (FQDN), s’il en existe un ; sinon, en fonction de l’adresse IP.| 
    | Outgoing Interface (Interface sortante) |Définissez l’adresse e-mail de notification de stratégie. <br /><br /> **Remarque** : Vérifiez que vous avez configuré un serveur de messagerie dans XSense. Si aucune adresse e-mail n’est entrée, XSense n’envoie pas d’e-mail de notification.| 
    |Configurer| Configurez les options suivantes pour autoriser le blocage des sources suspectes par le pare-feu FortiGate : <br /><br />**Block illegal function codes** (Bloquer les codes de fonction non conformes) : Violations de protocole - Valeur de champ non conforme qui ne respecte pas la spécification de protocole ICS (attaque potentielle)<br /><br />**Block unauthorized PLC programming / firmware updates** (Bloquer les mises à jour non autorisées des microprogrammes/programmes PLC) : Changements de contrôleur PLC non autorisés<br /><br />**Block unauthorized PLC stop** (Bloquer l’arrêt de PLC non autorisé) : Arrêt du PLC (temps d’arrêt)<br /><br />**Block malware-related alerts** (Bloquer les alertes liées aux programmes malveillants) : Blocage des tentatives de programmes malveillants industriels (TRITON, NotPetya, etc.). Vous pouvez sélectionner l’option de blocage automatique (**Automatic blocking**). Dans ce cas, le blocage est exécuté automatiquement et immédiatement.<br /><br />*Block unauthorized scanning* (Bloquer l’analyse non autorisée) : Analyse non autorisée (reconnaissance potentielle)

1. Sélectionnez **Envoyer**.

## <a name="block-the-suspicious-source"></a>Bloquer la source suspecte

1. Dans le volet **Alerts**, sélectionnez l’alerte liée à l’intégration de Fortinet.

    :::image type="content" source="media/integration-fortinet/unauthorized.png" alt-text="Fenêtre Unauthorized PLC Programming":::

1. Pour bloquer automatiquement la source suspecte, sélectionnez **Block Source** (Bloquer la source).

    :::image type="content" source="media/integration-fortinet/confirm.png" alt-text="Fenêtre de confirmation.":::

1. Dans la boîte de dialogue **Please Confirm**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [transférer des informations d’alerte](how-to-forward-alert-information-to-partners.md).
