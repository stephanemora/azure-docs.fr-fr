---
title: Configurer votre réseau
description: En savoir plus sur l’architecture de la solution, la préparation du réseau, les conditions préalables et d’autres informations nécessaires pour vous assurer que votre réseau est correctement configuré pour fonctionner avec les appliances Azure Defender pour IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b5cb7d0e421657b84c365d8dca536191cafd4e72
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861971"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>À propos de la configuration du réseau d’Azure Defender pour IoT

Azure Defender pour IoT assure la surveillance continue des menaces ICS et la détection des appareils. La plateforme comprend les composants suivants :

**Capteurs Defender pour IoT :** Les capteurs collectent le trafic de partage de connexion Internet (ICS) à l’aide de la surveillance passive (sans agent). Passifs et non intrusifs, les capteurs n’ont aucun impact sur les performances des réseaux et appareils OT et IoT. Le capteur se connecte à un port SPAN ou à un TAP réseau et commence immédiatement à surveiller votre réseau. Les détections s’affichent dans la console du capteur. Dans cette console, vous pouvez les visualiser, les examiner et les analyser dans une carte du réseau, un inventaire des appareils et un large éventail de rapports. Les exemples incluent des rapports d’évaluation des risques, des requêtes d’exploration de données et des vecteurs d’attaque. 

**Console de gestion locale Defender pour IoT** : La console de gestion locale fournit une vue consolidée de tous les périphériques réseau. Elle fournit une vue en temps réel des principaux indicateurs de risque IoT et OT et des alertes dans toutes vos installations. Étroitement intégrée à vos workflows SOC et à vos guides opérationnels, elle permet de hiérarchiser facilement les activités d’atténuation et de corréler les menaces entre les différents sites. 

**Portail Defender pour IoT :** L’application Defender pour IoT peut vous aider à acheter des appliances de solution, à installer et à mettre à jour des logiciels et à mettre à jour les packages TI. 

Cet article fournit des informations sur l’architecture de la solution, la préparation du réseau, les conditions préalables, et bien plus encore pour vous aider à configurer votre réseau de façon à ce qu’il fonctionne avec les appliances Defender pour IoT. Les lecteurs qui utilisent les informations de cet article doivent maîtriser le fonctionnement et la gestion des réseaux OT et IoT. Par exemple : les ingénieurs en automatisation, les directeurs d’usine, les fournisseurs de services d’infrastructure de réseau OT, les équipes de cybersécurité, les responsables de la sécurité des systèmes d’information ou les directeurs des systèmes d’information.

Pour obtenir de l’aide ou un support, contactez [Support Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Tâches de déploiement sur site

Les tâches de déploiement sur site comprennent :

- [Collecter des informations sur le site](#collect-site-information)

- [Préparer une station de travail de configuration](#prepare-a-configuration-workstation)

- [Planifier l’installation de racks](#planning-rack-installation)

### <a name="collect-site-information"></a>Collecter des informations sur le site

Enregistrez les informations du site telles que celles ci-dessous :

- Informations sur le réseau de gestion de capteur.

- Architecture réseau du site.

- Environnement physique.

- Intégrations du système.

- Informations d’identification prévues pour les utilisateurs.

- Station de travail de configuration.

- Certificats SSL (facultatif, mais recommandé).

- Authentification SMTP (facultatif). Pour utiliser le serveur SMTP avec l’authentification, préparez les informations d’identification requises pour votre serveur.

- Serveurs DNS (facultatif). Préparez l’adresse IP et le nom d’hôte de votre serveur DNS.

Pour obtenir une liste et une description détaillées des informations importantes relatives au site, consultez [Exemple de livre de site](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Instructions de surveillance réussies

Pour trouver l’emplacement optimal pour connecter l’appliance à chacun de vos réseaux de production, nous vous recommandons de suivre la procédure suivante :

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Exemple de diagramme de la configuration d’un réseau de production.":::

### <a name="prepare-a-configuration-workstation"></a>Préparer une station de travail de configuration

Préparez une station de travail Windows, notamment les éléments suivants :

- Connectivité à l’interface de gestion du capteur.

- Navigateur pris en charge.

- Logiciel de terminal, tel que PuTTY.

Assurez-vous que les règles de pare-feu requises sont ouvertes sur la station de travail. Pour plus d’informations, consultez [Conditions requises pour l’accès au réseau](#network-access-requirements).

#### <a name="supported-browsers"></a>Navigateurs pris en charge

Les navigateurs suivants sont pris en charge par les capteurs et les applications web de la console de gestion locale :

- Chrome 32 et versions ultérieures

- Microsoft Edge 86 et versions ultérieures

- Internet Explorer 10+

### <a name="network-access-requirements"></a>Conditions requises pour l’accès au réseau

Vérifiez que la stratégie de sécurité de votre organisation autorise l’accès aux éléments suivants :

| **Objectif** | **Protocole** | **Transport** | **Entrant ou sortant** | **Port** | **Catégorie** |
| ----------- | ----------- | ------------ | ---------- | -------- | ------------ |
| **Accès à la console Web** | HTTPS | TCP | Entrant ou sortant | 443 | Console de gestion locale pour la plateforme Defender pour IoT |
| **Accès à l’interface de ligne de commande** | SSH | TCP | Entrant ou sortant | 22 | Interface de ligne de commande |
| **Connexion entre la plateforme Defender pour IoT et la console de gestion locale** | SSL | TCP | Entrant ou sortant | 443 | Capteur et console de gestion locale|
| **Console de gestion locale utilisée comme NTP sur le capteur** | NTP | UDP| Entrant sur CM | 123 | Synchronisation de l’heure | 
| **Capteur connecté au serveur NTP externe (le cas échéant)** | NTP | UDP | Entrant ou sortant| 123 | Synchronisation de l’heure |
| **Connexion entre la plateforme Defender pour IoT, la plateforme de gestion et le serveur de messagerie (le cas échéant)** | SMTP | TCP | Sortant de la plateforme de gestion du capteur | 25 | Courrier |
| **Journaux envoyés de la console de gestion locale au serveur Syslog (le cas échéant)** | syslog | UDP | Sortant de la plateforme de gestion du capteur| 514 | LEEF |
| **Port du serveur DNS (le cas échéant)** | DNS | N/A | Entrant ou sortant| 53 | DNS |
| **Connexion entre la plateforme Defender pour IoT et la console de gestion locale vers Active Directory (le cas échéant)** | LDAPS | TCP | Entrant ou sortant | 636 <br />389 | Active Directory |
| **Collecteurs SNMP distants (le cas échéant)** | SNMP | UDP | Sortant de la plateforme de gestion du capteur| 161 | Surveillance |
| **Surveillance des points de terminaison Windows (le cas échéant)** | WMI | UDP | Sortant de la plateforme de gestion du capteur| 135 | Surveillance |
| **Surveillance des points de terminaison Windows (le cas échéant)** | WMI | TCP | Sortant de la plateforme de gestion du capteur| 1024 et plus | Surveillance |
| **Tunneling (le cas échéant)** | Tunneling | TCP | Entrant sur CM | 9000<br />En plus du port 443<br />De l’utilisateur final à la console de gestion locale <br />Port 22 du capteur à la console de gestion locale | Surveillance |
| **Sortant vers le hub Defender pour IoT** | HTTPS | TCP | Sortant de la plateforme de gestion du capteur| **URL**<br />*.azure-devices.net:443<br />ou si les caractères génériques ne sont pas pris en charge :<br />{nom de votre hub IoT}.azure-devices.net:443 |

### <a name="planning-rack-installation"></a>Planifier l’installation de racks

Pour planifier votre installation de racks :

1. Préparez un moniteur et un clavier pour les paramètres réseau de votre appliance.
2. Allouez l’espace de rack de l’appliance.
3. Prévoyez une alimentation en courant alternatif pour l’appliance.
4. Préparez le câble LAN pour connecter la console de gestion au commutateur réseau.
5. Préparez les câbles LAN pour connecter les ports SPAN (miroir) du commutateur et/ou les TAP réseau à l’appliance Defender pour IoT. 
6. Configurez, connectez et validez les ports SPAN dans les commutateurs mis en miroir comme décrit dans la session de révision de l’architecture.
7. Connectez le port SPAN configuré à un ordinateur exécutant Wireshark et vérifiez que le port est correctement configuré.
8. Ouvrez tous les ports de pare-feu appropriés.

## <a name="about-passive-network-monitoring"></a>À propos de la surveillance passive du réseau

L’appliance reçoit le trafic de plusieurs sources, soit par des ports miroirs du commutateur (ports SPAN), soit par des TAP réseau. Le port de gestion est connecté au réseau de gestion de l’entreprise, de la société ou du capteur avec une connectivité à une console de gestion locale ou au portail Defender pour IoT.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagramme d’un commutateur géré avec mise en miroir des ports.":::

### <a name="purdue-model"></a>Modèle Purdue

Les sections suivantes décrivent les niveaux Purdue.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagramme du modèle Purdue.":::

####  <a name="level-0-cell-and-area"></a>Niveau 0 : Cellule et zone  

Le niveau 0 est constitué d’un large éventail de capteurs, d’actionneurs et de dispositifs impliqués dans le processus de fabrication de base. Ces dispositifs exécutent les fonctions de base du système industriel d’automatisation et de contrôle, telles que :

- Entraînement d’un moteur.
- Mesure de variables.
- Définition d’une sortie.
- Exécution de fonctions clés, telles que la peinture, le soudage et le pliage.

#### <a name="level-1-process-control"></a>Niveau 1 : Contrôle du processus

Le niveau 1 comprend des contrôleurs intégrés qui contrôlent et manipulent le processus de fabrication et dont la fonction principale est de communiquer avec les dispositifs de niveau 0. En production discrète, ces dispositifs sont des contrôleurs logiques programmables (PLC) ou des unités de télémétrie à distance (RTU). Dans l’industrie de type process, le contrôleur de base est appelé un système de contrôle distribué (DC).

#### <a name="level-2-supervisory"></a>Niveau 2 : Supervision

Le niveau 2 représente les systèmes et fonctions associés à la supervision et à l’exploitation du runtime d’une zone d’une installation de production. Il s’agit généralement des éléments suivants : 

- Interfaces opérateur ou IHM

- Alertes ou systèmes d’alerte

- Historien des processus et systèmes de gestion des lots

- Stations de travail de la salle de contrôle

Ces systèmes communiquent avec les PLC et les RTU du niveau 1. Dans certains cas, ils communiquent ou partagent des données avec les systèmes et les applications du site ou de l’entreprise (niveau 4 et 5). Ces systèmes sont principalement basés sur des équipements informatiques et des systèmes d’exploitation standard (Unix ou Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Niveaux 3 et 3,5 : Niveau de site et réseau de périmètre industriel

Le niveau de site représente le niveau le plus élevé de systèmes d’automatisation et de contrôle industriels. Les systèmes et les applications qui existent à ce niveau gèrent les fonctions d’automatisation et de contrôle industrielles à l’échelle du site. Les niveaux 0 à 3 sont considérés comme critiques pour les opérations du site. Les systèmes et fonctions qui existent à ce niveau peuvent inclure les éléments suivants :

- Rapports de production (par exemple, durées de cycle, index de qualité, maintenance prédictive)

- Historien de l’usine

- Planification détaillée de la production

- Gestion des opérations au niveau du site

- Gestion des dispositifs et du matériel

- Serveur de lancement des correctifs

- Serveur de fichiers

- Domaine industriel, Active Directory, serveur de terminal

Ces systèmes communiquent avec la zone de production et partagent des données avec les systèmes et applications de l’entreprise (niveau 4 et 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Niveaux 4 et 5 : Réseaux commerciaux et d’entreprise

Les niveaux 4 et 5 représentent le réseau du site ou de l’entreprise sur lequel se trouvent les fonctions et les systèmes informatiques centralisés. L’organisation informatique gère directement les services, les systèmes et les applications à ces niveaux.

## <a name="planning-for-network-monitoring"></a>Planification de la surveillance du réseau

Les exemples suivants présentent différents types de topologies pour les réseaux de contrôle industriel, ainsi que des considérations pour une surveillance et un placement optimaux des capteurs.

### <a name="what-should-be-monitored"></a>Qu’est-ce qui doit être surveillé ?

Le trafic qui traverse les couches 1 et 2 doit être surveillé.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>À quoi l’appliance Defender pour IoT doit-elle se connecter ?

L’appliance Defender pour IoT doit se connecter aux commutateurs gérés qui voient les communications industrielles entre les couches 1 et 2 (également la couche 3 dans certains cas).

Le diagramme suivant est une abstraction générale d’un réseau multilocataire et multicouche, avec un écosystème de cybersécurité étendu, généralement exploité par un centre des opérations de sécurité et un fournisseur de services managés de sécurité.

En règle générale, les capteurs NTA sont déployés dans les couches 0 à 3 du modèle OSI.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagramme du modèle OSI.":::

#### <a name="example-ring-topology"></a>Exemple : Topologie en anneau

Le réseau en anneau est une topologie dans laquelle chaque commutateur ou nœud se connecte à exactement deux autres commutateurs, formant ainsi une seule voie continue pour le trafic.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagramme de la topologie en anneau.":::

#### <a name="example-linear-bus-and-star-topology"></a>Exemple : Topologie en bus linéaire et en étoile

Dans un réseau en étoile, chaque hôte est connecté à un hub central. Dans sa forme la plus simple, un hub central agit comme un conduit pour transmettre des messages. Dans l’exemple suivant, les commutateurs inférieurs ne sont pas surveillés, et le trafic qui reste localement sur ces commutateurs ne sera pas visible. Les appareils peuvent être identifiés sur la base des messages ARP, mais les informations de connexion seront manquantes.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagramme de la topologie en bus linéaire et en étoile.":::

#### <a name="multisensor-deployment"></a>Déploiement multicapteur

Voici quelques recommandations pour le déploiement de plusieurs capteurs :

| **Nombre**| **Mètres** | **Dépendance** | **Nombre de capteurs** |
|--|--|--|--|
| Distance maximale entre les commutateurs | 80 mètres | Câble Ethernet préparé | Plus de 1 |
| Nombre de réseaux OT | Plus de 1 | Aucune connectivité physique | Plus de 1 |
| Nombre de commutateurs | Peut utiliser la configuration RSPAN | Jusqu’à huit commutateurs avec une étendue locale proche du capteur par distance de câblage | Plus de 1 |

#### <a name="traffic-mirroring"></a>Mise en miroir du trafic  

Pour afficher uniquement les informations pertinentes pour l’analyse du trafic, vous devez connecter la plateforme Defender pour IoT à un port de mise en miroir sur un commutateur ou un TAP qui comprend uniquement le trafic SCADA et ICS industriel. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Utilisez ce commutateur pour votre installation.":::

Vous pouvez surveiller le trafic du commutateur à l’aide des méthodes suivantes :

- [Port SPAN du commutateur](#switch-span-port)

- [SPAN distant (RSPAN)](#remote-span-rspan)

- [TAP d’agrégation actif et passif](#active-and-passive-aggregation-tap)

Les termes « SPAN » et « RSPAN » appartiennent à la terminologie de Cisco. D’autres marques de commutateurs ont des fonctionnalités similaires, mais peuvent utiliser une terminologie différente.

#### <a name="switch-span-port"></a>Port SPAN du commutateur

Un analyseur de port de commutateur reflète le trafic local provenant des interfaces sur le commutateur vers l’interface sur le même commutateur. Voici quelques éléments à prendre en compte :

- Vérifiez que le commutateur approprié prend en charge la fonction de mise en miroir des ports.  

- L’option de mise en miroir est désactivée par défaut.

- Nous vous recommandons de configurer tous les ports du commutateur, même si aucune donnée n’y est connectée. Sinon, un appareil non autorisé pourrait être connecté à un port non surveillé et cela ne serait pas signalé sur le capteur.

- Sur les réseaux OT qui utilisent la diffusion ou la multidiffusion de messages, configurez le commutateur pour qu’il mette en miroir uniquement les transmissions RX (réception). Sinon, les messages multidiffusés seront répétés pour autant de ports actifs, et la bande passante sera multipliée.

Les exemples de configuration suivants sont fournis à titre indicatif uniquement et sont basés sur un commutateur Cisco 2960 (24 ports) sous iOS. Il s’agit uniquement d’exemples typiques, ne les utilisez donc pas comme instructions. Les ports de mise en miroir des autres systèmes d’exploitation Cisco et des autres marques de commutateurs sont configurés différemment.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagramme d’un terminal de configuration de port SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagramme du mode de configuration du port SPAN.":::

##### <a name="monitoring-multiple-vlans"></a>Surveillance de plusieurs VLAN

Defender pour IoT permet de surveiller les VLAN configurés dans votre réseau. Aucune configuration du système Defender pour IoT n’est requise. L’utilisateur doit s’assurer que le commutateur de son réseau est configuré pour envoyer des balises VLAN à Defender pour IoT.

L’exemple suivant montre les commandes requises qui doivent être configurées sur le commutateur Cisco pour activer la surveillances des VLAN dans Defender pour IoT :

**Surveiller la session** : Cette commande est responsable du processus d’envoi des VLAN au port SPAN.

- monitor session 1 source interface Gi1/2

- monitor session 1 filter packet type good Rx

- monitor session 1 destination interface fastEthernet1/1 encapsulation dot1q

**Surveiller le port trunk F.E. Gi1/1** : Les VLAN sont configurés sur le port trunk.

- interface GigabitEthernet1/1

- switchport trunk encapsulation dot1q

- switchport mode trunk

#### <a name="remote-span-rspan"></a>SPAN distant (RSPAN)

La session SPAN distante met en miroir le trafic de plusieurs ports sources distribués vers un VLAN distant dédié.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagramme du SPAN distant.":::

Les données dans le VLAN sont ensuite remises via des ports en mode trunk sur plusieurs commutateurs à un commutateur spécifique qui contient le port de destination physique. Ce port se connecte à la plateforme Defender pour IoT.  

##### <a name="more-about-rspan"></a>En savoir plus sur RSPAN

- RSPAN est une fonctionnalité avancée qui requiert un VLAN spécial pour acheminer le trafic que le SPAN surveille entre les commutateurs. RSPAN n’est pas pris en charge sur tous les commutateurs. Vérifiez que le commutateur prend en charge la fonction RSPAN.

- L’option de mise en miroir est désactivée par défaut.

- Le VLAN distant doit être autorisé sur le port en mode trunk entre les commutateurs source et destination.

- Tous les commutateurs qui se connectent à la même session RSPAN doivent provenir du même fournisseur.

> [!NOTE]
> Assurez-vous que le port trunk qui partage le VLAN distant entre les commutateurs n’est pas défini comme port source de la session mise en miroir.
>
> Le VLAN distant augmente la bande passante sur le port en mode trunk en fonction de la taille de la bande passante de la session mise en miroir. Vérifiez que le port trunk du commutateur prend cela en charge.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagramme d’un VLAN distant.":::

#### <a name="rspan-configuration-examples"></a>Exemples de configuration de RSPAN

RSPAN : Basé sur Cisco Catalyst 2960 (24 ports).

**Exemple de configuration du commutateur source :**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Capture d’écran de la configuration de RSPAN.":::

1. Entrez le mode de configuration globale.

1. Créez un VLAN dédié.

1. Identifiez le VLAN comme étant le VLAN RSPAN.

1. Revenez au mode « Configurer le terminal ».

1. Configurez les 24 ports comme sources de session.

1. Configurez le VLAN RSPAN comme destination de la session.

1. Revenez au mode EXEC privilégié.

1. Vérifiez la configuration de la mise en miroir des ports.

**Exemple de configuration du commutateur de destination :**

1. Entrez le mode de configuration globale.

1. Configurez le VLAN RSPAN comme source de la session.

1. Configurez le port physique no 24 comme destination de la session.

1. Revenez au mode EXEC privilégié.

1. Vérifiez la configuration de la mise en miroir des ports.

1. Enregistrez la configuration.

#### <a name="active-and-passive-aggregation-tap"></a>TAP d’agrégation actif et passif

Un TAP d’agrégation actif ou passif est installé inlined sur le câble réseau. Il duplique le trafic d’envoi et de réception sur le capteur de surveillance.

Le point d’accès terminal (TAP) est un périphérique matériel qui permet au trafic de circuler du port A au port B et du port B au port A, sans interruption. Il crée une copie exacte des deux côtés du flux de trafic, en continu, sans compromettre l’intégrité du réseau. Certains TAP regroupent le trafic de transmission et de réception en utilisant les paramètres du commutateur, le cas échéant. Si l’agrégation n’est pas prise en charge, chaque TAP utilise deux ports de capteur pour surveiller le trafic d’envoi et de réception.

Les TAP sont avantageux pour diverses raisons. Ils sont basés sur le matériel et ne peuvent pas être compromis. Ils transmettent tout le trafic, même les messages corrompus, que les commutateurs abandonnent souvent. Comme ils ne sont pas soumis au processeur, la synchronisation des paquets est exacte, alors que les commutateurs gèrent la fonction de mise en miroir comme une tâche de faible priorité qui peut influer sur la synchronisation des paquets mis en miroir. À des fins d’investigation, un TAP est le meilleur appareil.

Des agrégateurs de TAP peuvent également être utilisées pour la surveillance des ports. Ces appareils sont basés sur un processeur et ne sont pas aussi intrinsèquement sûrs que les TAP matériels. Ils peuvent ne pas refléter la synchronisation exacte des paquets.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagramme des TAP actifs et passifs.":::

##### <a name="common-tap-models"></a>Modèles de TAP courants

La compatibilité de ces modèles a été testée. D’autres fournisseurs et modèles peuvent également être compatibles.

| Image | Modèle |
| -- | -- |
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Capture d’écran de Garland P1GCCAS.":::  | Garland P1GCCAS  |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Capture d’écran de IXIA TPA2-CU3.":::  | IXIA TPA2-CU3  |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Capture d’écran de US Robotics USR 4503.":::  | US Robotics USR 4503  |

##### <a name="special-tap-configuration"></a>Configuration spéciale de TAP

| TAP Garland | TAP US Robotics |
| ----------- | --------------- |
| Assurez-vous que les cavaliers sont définis comme suit :<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Capture d’écran du commutateur US Robotics.":::| Assurez-vous que le mode d’agrégation est actif. |

## <a name="deployment-validation"></a>Validation du déploiement

### <a name="engineering-self-review"></a>Auto-évaluation de l’ingénierie  

L’examen de votre diagramme réseau OT et ICS est le moyen le plus efficace de définir le meilleur emplacement pour la connexion, et où vous pouvez obtenir le trafic le plus pertinent pour la surveillance.

Les ingénieurs du site savent à quoi ressemble leur réseau. Une session d’évaluation avec les équipes opérationnelles et celles du réseau local permet généralement de clarifier les attentes et de définir le meilleur emplacement pour connecter l’appareil.

Informations pertinentes :

- Liste des appareils connus (feuille de calcul)  

- Nombre estimé d’appareils  

- Fournisseurs et protocoles industriels

- Modèle de commutateurs, pour vérifier que l’option de mise en miroir des ports est disponible

- Informations sur la personne qui gère les commutateurs (par exemple, les services informatiques) et sur le fait qu’il s’agisse ou non de ressources externes

- Liste des réseaux OT sur le site

#### <a name="common-questions"></a>Questions courantes

- Quels sont les objectifs globaux de l’implémentation ? Un inventaire complet et une carte précise du réseau sont-ils importants ?

- Existe-t-il des réseaux multiples ou redondants dans le partage de connexion Internet ? Tous les réseaux sont-ils surveillés ?

- Existe-t-il des communications entre le partage de connexion Internet et le réseau d’entreprise (commercial) ? Ces communications sont-elles surveillées ?

- Les VLAN sont-ils configurés dans la conception du réseau ?

- Comment la maintenance de l’ICS est-elle assurée, avec des appareils fixes ou temporaires ?

- Où sont installés les pare-feu dans les réseaux surveillés ?

- Y a-t-il un routage dans les réseaux surveillés ?

- Quels protocoles OT sont-ils actifs sur les réseaux surveillés ?

- Si nous nous connectons à ce commutateur, verrons-nous une communication entre l’IHM et les PLC ?

- Quelle est la distance physique entre les commutateurs ICS et le pare-feu d’entreprise ? 

- Les commutateurs non gérés peuvent-ils être remplacés par des commutateurs gérés, ou l’utilisation de TAP de réseau est-elle une option ?

- Existe-t-il des communications en série sur le réseau ? Si oui, indiquez-le sur le diagramme du réseau.

- Si l’appliance Defender pour IoT doit être connectée à ce commutateur, un espace physique est-il disponible dans cette armoire pour les racks ?

#### <a name="additional-considerations"></a>Considérations supplémentaires

L’objectif de l’appliance Defender pour IoT est de surveiller le trafic des couches 1 et 2.

Pour certaines architectures, l’appliance Defender pour IoT surveille également la couche 3, si le trafic existe sur cette couche. Pendant que vous examinez l’architecture du site et décidez s’il faut surveiller un commutateur, prenez en compte les variables suivantes :

- Quel est le coût/avantage par rapport à l’importance de la surveillance de ce commutateur ?

- Si un commutateur n’est pas géré, est-il possible de surveiller le trafic à partir d’un commutateur de niveau supérieur ?

  Si l’architecture ICS est une topologie en anneau, un seul commutateur de cet anneau doit être surveillé.

- Quel est le risque sécuritaire ou opérationnel de ce réseau ?

- Est-il possible de surveiller le VLAN du commutateur ? Ce VLAN est-il visible dans un autre commutateur que nous pouvons surveiller ?

#### <a name="technical-validation"></a>Validation technique

La réception d’un échantillon du trafic enregistré (fichier PCAP) à partir du port SPAN du commutateur (ou miroir) peut permettre d’effectuer les opérations suivantes :

- Vérifier si le commutateur est correctement configuré.

- Confirmer si le trafic qui passe par le commutateur est pertinent pour la surveillance (trafic OT).

- Identifier la bande passante et le nombre estimé d’appareils dans ce commutateur.

Vous pouvez enregistrer un exemple de fichier PCAP (quelques minutes) en connectant un ordinateur portable à un port SPAN déjà configuré via l’application Wireshark.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Capture d’écran d’un ordinateur portable connecté à un port SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Capture d’écran de l’enregistrement d’un exemple de fichier PCAP.":::

#### <a name="wireshark-validation"></a>Validation Wireshark

- Vérifiez que des *paquets de monodiffusion* sont présents dans le trafic d’enregistrement. La monodiffusion se fait d’une adresse à une autre. Si la majeure partie du trafic est constituée de messages ARP, la configuration du commutateur est incorrecte.

- Accédez à **Statistiques** > **Hiérarchie des protocoles**. Vérifiez que des protocoles OT industriels sont présents.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Capture d’écran de la validation Wireshark.":::

## <a name="troubleshooting"></a>Résolution des problèmes

Utilisez les sections suivantes pour résoudre les problèmes :

- [Connexion impossible à l’aide d’une interface web](#cant-connect-by-using-a-web-interface)

- [L’appliance ne répond pas](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Connexion impossible à l’aide d’une interface web

1. Vérifiez que l’ordinateur que vous essayez de connecter se trouve sur le même réseau que l’appliance.

2. Vérifiez que le réseau de l’interface graphique utilisateur est connecté au port de gestion sur la capteur.

3. Effectuez un test ping sur l’adresse IP de l’appliance. S’il n’y a pas de réponse au test Ping :

    1. Connectez un moniteur et un clavier à l’appliance.

    1. Utilisez l’utilisateur **Support** et son mot de passe pour vous connecter.

    1. Utilisez la commande **network list** pour afficher l’adresse IP actuelle.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Capture d’écran de la commande network list.":::

4. Si les paramètres du réseau sont mal configurés, utilisez la procédure suivante pour les modifier :

    1. Utilisez la commande **network edit-settings**.

    1. Pour modifier l’adresse IP du réseau de gestion, sélectionnez **O**.

    1. Pour modifier le masque de sous-réseau, sélectionnez **O**.

    1. Pour modifier le DNS, sélectionnez **O**.

    1. Pour modifier l’adresse IP de la passerelle par défaut, sélectionnez **O**.

    1. Pour la modification de l’interface d’entrée (pour capteur uniquement), sélectionnez **Y**.

    1. Pour l’interface de pont, sélectionnez **N**.

    1. Pour appliquer les paramètres, sélectionnez **O**.

5. Après avoir redémarré, connectez-vous avec l’utilisateur Support et utilisez la commande **network list** pour vérifier que les paramètres ont été modifiés.

6. Essayez d’effectuer un test ping et de vous reconnecter à partir de l’interface graphique utilisateur.

### <a name="appliance-is-not-responding"></a>L’appliance ne répond pas

1. Connectez un moniteur et un clavier à l’appliance, ou utilisez PuTTY pour vous connecter à distance à l’interface de ligne de commande.

2. Utilisez les informations d’identification de Support pour vous connecter.

3. Utilisez la commande **system sanity** et vérifiez que tous les processus sont en cours d’exécution.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Capture d’écran de la commande system sanity.":::

Pour tout autre problème, contactez [Support Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Exemple de livre de site

Utilisez l’exemple de livre de site pour récupérer et passer en revue les informations importantes dont vous avez besoin pour la configuration du réseau.

### <a name="site-checklist"></a>Liste de vérification de site

Passez en revue cette liste avant le déploiement à l’échelle du site :

| **#** | **Tâche ou activité** | **État** | **Commentaires** |
|--|--|--|--|
| 1 | Fournir des services globaux. | ☐ |  |
| 3 | Commander des appliances. | ☐ |  |
| 4 | Préparer une liste de sous-réseaux dans le réseau. | ☐ |  |
| 5 | Fournir une liste de VLAN des réseaux de production. | ☐ |  |
| 6 | Fournir une liste de modèles de commutateur dans le réseau. | ☐ |  |
| 7 | Fournir une liste des fournisseurs et des protocoles de l’équipement industriel. | ☐ |  |
| 8 | Fournir les détails du réseau pour les capteurs (adresse IP, sous-réseau, D-GW, DNS). | ☐ |  |
| 9 | Créer les règles de pare-feu nécessaires et la liste d’accès. | ☐ |  |
| 10 | Créer des ports SPAN sur des commutateurs pour la surveillance des ports, ou configurer les TAP de réseau selon les besoins. | ☐ |  |
| 11 | Préparer l’espace rack pour les appliances de détection. | ☐ |  |
| 12 | Préparer une station de travail pour le personnel. | ☐ |  |
| 13 | Fournir un clavier, un moniteur et une souris pour les appareils racks Defender pour IoT. | ☐ |  |
| 14 | Installer le rack et les câbles des appliances. | ☐ |  |
| 15 | Allouer les ressources du site pour prendre en charge le déploiement. | ☐ |  |
| 16 | Créer des groupes Active Directory ou des utilisateurs locaux. | ☐ |  |
| 17 | Configurer une formation (auto-apprentissage). | ☐ |  |
| 18 | Prêt ou non prêt. | ☐ |  |
| 19 | Planifier la date de déploiement. | ☐ |  |


| **Date** | **Remarque** | **Date du déploiement** | **Remarque** |
|--|--|--|--|
| Defender pour IoT |  | Nom du site* |  |
| Nom |  | Nom |  |
| Position |  | Position |  |

#### <a name="architecture-review"></a>Évaluation de l’architecture

Une vue d’ensemble du diagramme du réseau industriel vous permet de définir l’emplacement approprié pour l’équipement Defender pour IoT.

1.  Consultez un diagramme du réseau global de l’environnement OT industriel. Exemple :

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagramme de l’environnement OT industriel pour le réseau global.":::

    > [!NOTE]
    > L’appliance Defender pour IoT doit être connectée à un commutateur de niveau inférieur qui voit le trafic entre les ports du commutateur.  

2. Indiquez le nombre approximatif de périphériques réseau qui seront analysés. Vous aurez besoin de ces informations lors de l’intégration de votre abonnement au portail Azure Defender pour IoT. Pendant le processus d’intégration, vous serez invité à entrer le nombre d’appareils par incréments de 1000.

3. Fournissez une liste de sous-réseaux pour les réseaux de production et une description (facultatif). 

    |  **#**  | **Nom du sous-réseau** | **Description** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Fournissez une liste de VLAN des réseaux de production.

    | **#** | **Nom du VLAN** | **Description** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Pour vérifier que les commutateurs ont la capacité de mettre en miroir les ports, indiquez les numéros de modèle des commutateurs auxquels la plateforme Defender pour IoT doit se connecter :

    | **#** | **Switch** | **Modèle** | **Prise en charge de la mise en miroir du trafic (SPAN, RSPAN ou aucune)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Un tiers gère-t-il les commutateurs ? O ou N 

    Si oui, qui ? __________________________________ 

    Quelle est sa stratégie ? __________________________________ 

    Exemple :

    - Siemens

    - Rockwell Automation : Ethernet ou IP

    - Emerson : DeltaV, Ovation
    
6. Existe-t-il des appareils qui communiquent par le biais d’une connexion série sur le réseau ? Oui ou Non 

    Si oui, spécifiez le protocole de communication série : ________________ 

    Si oui, indiquez sur le diagramme du réseau quels appareils communiquent avec les protocoles série, et où ils se trouvent : 
 
    <Add your network diagram with marked serial connection> 

7. Pour la Qualité de service, le paramètre par défaut du capteur est de 1,5 Mbits/s. Spécifiez si vous souhaitez le modifier : ________________ 

   Unité commerciale : ________________ 

### <a name="specifications-for-site-equipment"></a>Spécifications pour l’équipement du site

#### <a name="network"></a>Réseau  

L’appliance de détection est connectée au port SPAN du commutateur via une carte réseau. Elle est connectée au réseau d’entreprise du client à des fins de gestion par le biais d’une autre carte réseau dédiée.

Fournissez des informations sur l’adresse de la carte réseau du capteur qui sera connectée au réseau d’entreprise : 

|  Élément               | Appliance 1 | Appliance 2 | Appliance 3 |
| --------------- | ------------- | ------------- | ------------- |
| Adresse IP de l’appliance    |               |               |               |
| Subnet          |               |               |               |
| Passerelle par défaut |               |               |               |
| DNS             |               |               |               |
| Nom de l’hôte       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/gestion des serveurs

|       Élément          | Appliance 1 | Appliance 2 | Appliance 3 |
| --------------- | ------------- | ------------- | ------------- |
| Adresse IP de l’appliance     |               |               |               |
| Subnet          |               |               |               |
| Passerelle par défaut |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Console de gestion locale  

|       Élément          | Actif | Passif (en cas d’utilisation de la haute disponibilité) |
| --------------- | ------ | ----------------------- |
| Adresse IP             |        |                         |
| Subnet          |        |                         |
| Passerelle par défaut |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Élément              | Détails |
| --------------- | ------ |
| IP              |        |
| Adresse IP | |
| Nom d’utilisateur | |
| Mot de passe | |
| Type d'authentification | MD5 ou SHA |
| Chiffrement | DES ou AES |
| Clé secrète | |
| Chaîne de communauté SNMP v2 |

### <a name="cm-ssl-certificate"></a>Certificat SSL CM

Envisagez-vous d’utiliser un certificat SSL ? Oui ou Non

Si oui, quel service allez-vous utiliser pour le générer ? Quels attributs inclurez-vous dans le certificat (par exemple, domaine ou adresse IP) ?

### <a name="smtp-authentication"></a>Authentification SMTP

Envisagez-vous d’utiliser le protocole SMTP pour transférer des alertes à un serveur de messagerie ? Oui ou Non

Si oui, quelle est la méthode d’authentification que vous utiliserez ?  

### <a name="active-directory-or-local-users"></a>Active Directory ou utilisateurs locaux

Contactez un administrateur Active Directory pour créer un groupe d’utilisateurs du site Active Directory ou créez des utilisateurs locaux. Veillez à ce que vos utilisateurs soient prêts pour le jour du déploiement. 

### <a name="iot-device-types-in-the-network"></a>Types d’appareils IoT dans le réseau

| Type d’appareil | Nombre d’appareils dans le réseau | Bande passante moyenne |
| --------------- | ------ | ----------------------- |
| Appareil photo | |
| Appareil de radiographie | |

## <a name="see-also"></a>Voir aussi

[À propos de l’installation de Defender pour IoT](how-to-install-software.md)
