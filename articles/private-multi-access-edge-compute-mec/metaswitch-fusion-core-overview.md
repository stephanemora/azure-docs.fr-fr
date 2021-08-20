---
title: Solution Fusion Core dans Azure
description: Présentation de Fusion Core, une implémentation native Cloud de la 5G Next Generation Core (5G NGC ou 5GC) dont les spécifications sont définies par le 3GPP. Cette implémentation permet aux opérateurs de réseau mobile 5G d’agréger le trafic des données de tous les appareils via plusieurs technologies d’accès sans fil et d’accès fixe.
ms.service: private-multi-access-edge-compute-mec
author: djrmetaswitch
ms.author: drichards
ms.topic: overview
ms.date: 06/16/2021
ms.openlocfilehash: dd813b08301da960fb13e6047e51046ac2d95aed
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464554"
---
# <a name="what-is-fusion-core"></a>Présentation de Fusion Core

Fusion Core est une implémentation native Cloud de la 5G Next Generation Core (5G NGC ou 5GC) dont les spécifications sont définies par le 3GPP. Cette implémentation permet aux opérateurs de réseau mobile 5G d’agréger le trafic des données de tous les appareils via plusieurs technologies d’accès sans fil et d’accès fixe. Elle comprend une fonction de plan utilisateur (UPF) 5G hautes performances et hautement programmable, des fonctions de plan de contrôle principales, un portefeuille d’éléments d’architecture basés sur les services et des composants de gestion pour la supervision du réseau.

Fusion Core peut être déployé dans des scénarios de 5G pure ou associée à des réseaux 4G.

Fusion Core implémente les fonctions réseau 5G ci-dessous.

|Fonction réseau  |Description  |
|---------|---------|
|**AMF**<br><br>Fonction de gestion de la mobilité et des accès     |Celle-ci prend en charge ce qui suit :<br><ul> <li>Arrêt de la signalisation NAS à partir du gNB</li><li>Chiffrement NAS et protection de l’intégrité</li><li>Gestion des inscriptions</li><li>Gestion des connexions</li><li>Gestion de la mobilité</li><li>Authentification et autorisation des accès</li><li>Gestion du contexte de sécurité</li></ul>         |
|**SMF**<br><br>Fonction de gestion des sessions     |La fonction SMF prend en charge la configuration, la modification et la publication des sessions.<br><br>Dans ce contexte, elle fournit l’allocation et la gestion des adresses IP des UE, y compris DHCP, ainsi que l’arrêt de la signalisation NAS liée à la gestion des sessions.<br><br>Elle prend également en charge les appareils de radiomessagerie sur le trafic de données des liaisons descendantes, et permet la configuration de l’orientation du trafic dans le cadre de la gestion des sessions.         |
|**UPF**<br><br>Fonction de plan utilisateur     |La fonction UPF est responsable de la gestion du trafic des paquets de données. Cela comprend la gestion du routage, du transfert, de l’inspection et de la qualité de service du trafic des paquets de données.<br><br>Elle joue le rôle de point d’ancrage pour le trafic vers le réseau de données et pour le transfert entre différents réseaux radio.         |
|**PCF**<br><br>Fonction de contrôle de police     |La fonction PCF fournit un framework de gestion centralisée des stratégies de trafic, qui définit des règles de stratégie pour la fonction de plan de contrôle et qui sert de source pour les informations d’abonnement.         |
|**AUSF**<br><br>Fonction de serveur d’authentification     |La fonction AUSF est le serveur d’authentification des abonnés 5G.         |
|**UDM**<br><br>Gestion des données unifiée     |La fonction UDM prend en charge la génération des informations d’identification AKA, l’identification de l’utilisateur, l’autorisation d’accès et la gestion des abonnés.         |
|**UDR**<br><br>Référentiel de données unifié     |La fonction UDR est un référentiel qui comprend toutes les informations d’abonné.         |
|**NRF**<br><br>Fonction de référentiel réseau     |La fonction NRF permet la découverte des services pour les fonctions réseau.         |

Elle implémente également les fonctions réseau suivantes lors d’un interfonctionnement avec un réseau 4G.

|Fonction réseau  |Description  |
|---------|---------|
|**MME**<br><br>Entité de gestion de la mobilité     |La fonction MME est le nœud de contrôle principal du réseau d’accès LTE. Elle est chargée de contrôler tous les aspects de l’accès des UE au réseau.         |
|**S11-IWF**<br><br>Fonction d’interfonctionnement S11 |La fonction S11-IWF expose une interface S11 aux entités MME 4G et fournit des traductions de protocole pour les fonctions de prise en charge.         |
|**UDR**<br><br>Référentiel de données unifié     |La fonction UDR est un référentiel qui comprend toutes les informations d’abonné. La fonction UDR de Fusion Core joue un rôle équivalent à celui du HSS dans un scénario 4G. Elle interagit avec la fonction MME à l’aide de l’interface S6a.         |

Le diagramme suivant illustre chacune de ces fonctions réseau et les interfaces qu’elles utilisent pour interagir avec les composants tiers.

:::image type="content" source="./media/metaswitch-overview/fusion-core-architecture.png" alt-text="Architecture de Fusion Core":::

Fusion Core est distribué sur une machine virtuelle appelée « machine virtuelle de base Fusion Core ». La machine virtuelle de base Fusion Core est conçue pour être déployée comme une application gérée par Azure dans une instance Azure Stack Edge (ASE). Les fonctions réseau et les composants d’infrastructure nécessaires à la distribution de Fusion Core sont déployés sous la forme de conteneurs sur la machine virtuelle de base Fusion Core, et sont orchestrés par Kubernetes.

:::image type="content" source="./media/metaswitch-overview/fusion-core-base-vm-azure-stack-edge-with-networking.png" alt-text="Machine virtuelle de base Fusion Core sur Azure Stack Edge":::

## <a name="why-use-fusion-core"></a>Pourquoi utiliser Fusion Core ?

### <a name="deployment-in-private-networks"></a>Déploiement dans des réseaux privés

Fusion Core utilise les fonctionnalités du calcul de périphérie multi-accès privé pour fournir aux entreprises une solution 5G qui combine les performances et la faible latence des ressources de computing en périphérie nécessaires à la prise en charge des cas d’usage Industrie 4.0, avec une gestion cohérente et centralisée effectuée via Azure. Pour plus d’informations sur le calcul de périphérie multi-accès privé, consultez [Qu’est-ce que le calcul de périphérie multi-accès privé ?](overview.md).

Le fait de déployer Fusion Core à la périphérie lui permet d’être aussi proche que possible des appareils auxquels elle distribue des données, et donc de réduire le temps de latence ainsi que le nombre de liaisons terrestres grâce au traitement des données locales lorsque celui-ci est associé à une logique d’application à un même endroit. Cela offre aux entreprises un certain nombre d’avantages, notamment :

- **Automatisation** - Les messages de contrôle et de commande des systèmes automatisés (tels que les robots) peuvent être traités en temps réel pour empêcher les blocages et garantir une plus grande productivité.
- **Télémétrie** - Les données de télémétrie relatives à l’évaluation de l’intégrité et au fonctionnement des systèmes automatisés peuvent être traitées en temps réel afin d’éviter les accidents et de garantir la sécurité sur site.
- **Analytique** - Des quantités très importantes de données relatives au fonctionnement et au diagnostic peuvent être transportées à moindre coût, avec la garantie que les actions vitales ne seront pas retardées.

:::image type="content" source="./media/metaswitch-overview/enterprise-edge-latency.png" alt-text="Fusion Core à la périphérie d’un réseau Private 5G":::

Fusion Core peut tirer parti de cette faible latence, ainsi que de la sécurité et de la bande passante élevée qu’offrent les réseaux Private 5G, ce qui la rend idéale pour prendre en charge les cas d’usage Industrie 4.0 comme ceux qui suivent.

- **Fabrication** - Données d’analytique concernant la chaîne de production et l’automatisation de l’entrepôt à l’aide de robots.
- **Sécurité publique** - Mobilité et connectivité pour les services d’urgence et les opérateurs de récupération d’urgence.
- **Eau, gaz et électricité** - Liaisons terrestres pour les compteurs intelligents et le découpage/contrôle des réseaux.
- **Défense** - Postes de commandement et champ de bataille connectés à l’aide d’une analytique en temps réel.
- **Fermes intelligentes** - Équipement connecté pour les exploitations.

### <a name="pure-5g-and-4g-interworking-support"></a>Prise en charge de la 5G pure et de l’interfonctionnement avec la 4G

Fusion Core peut être déployée dans des scénarios de 5G pure. Dans ce cas, Fusion Core s’exécute en **mode autonome 5G**.

Fusion Core prend également en charge l’interfonctionnement avec la 4G, en fournissant un service aux UE 4G sur un noyau natif Cloud. C’est ce que l’on appelle le **mode 4G**. Pour les opérateurs de réseau mobile greenfield et historiques, le mode 4G fournit un chemin direct vers la 5G, sans poursuivre la maintenance d’un noyau 4G.

### <a name="service-assurance-and-kpi-metrics"></a>Métriques relatives à l’assurance de service et aux indicateurs de performance clés

Fusion Core est intégré à **Service Assurance Server de Metaswitch**, qui fournit une analyse proactive et en temps réel de tout le trafic des messages, y compris les messages NGAP/NAS, et les requêtes et réponses HTTP.

Service Assurance Server offre une interface utilisateur graphique web qui permet de collecter des traces détaillées concernant les flux de signalisation impliquant Fusion Core. Celles-ci peuvent être utilisées pour diagnostiquer de nombreux problèmes courants de configuration, de réseau et d’interopérabilité affectant le service utilisateur.

:::image type="content" source="./media/metaswitch-overview/service-assurance-server-detailed-timeline.png" alt-text="Vue détaillée de la chronologie dans l’interface utilisateur graphique web de Service Assurance Server":::

Fusion Core est également intégré à **Metaswitch ServiceIQ Monitoring** qui fournit des outils de supervision natifs Cloud standard, tels que Prometheus et Grafana. Ainsi, vous pouvez effectuer une analyse en temps réel des performances du système, détecter les défaillances et résoudre les problèmes.

ServiceIQ Monitoring s’exécute sur la machine virtuelle Fusion Core et vous permet d’accéder à un certain nombre de tableaux de bord Fusion Core. Ceux-ci permettent de superviser de manière souple les principales métriques relatives à votre déploiement de Fusion Core. Ils vous permettent également d’afficher des informations sur le déclenchement des alertes, pour que vous puissiez réagir rapidement aux problèmes émergents.


[![Tableau de bord de présentation de Fusion Core](media/metaswitch-overview/fusion-core-overview-dashboard.png)](media/metaswitch-overview/fusion-core-overview-dashboard.png#lightbox)

### <a name="other-features"></a>Autres fonctionnalités

|Fonctionnalité  |Description  |
|---------|---------|
|**Découpage réseau**     |Les déploiements Fusion Core peuvent être configurés pour fournir plusieurs tronçons réseau à des réseaux logiques indépendants multiplexés. Toutes les fonctions réseau qui se trouvent au sein d’un déploiement Fusion Core distribuent des données vers l’ensemble des tronçons configurés.<br><br>Vous pouvez choisir de provisionner les abonnés avec leurs tronçons autorisés et par défaut et de définir une stratégie propres aux tronçons (QoS), en fournissant un mécanisme simple et omniprésent qui permet la séparation administrative des groupes d’abonnés.         |
|**Procédures 5G prises en charge**     |Fusion Core est conforme à la norme 3GPP TS 23.502 en ce qui concerne les procédures suivantes, lorsqu’elle fonctionne dans le cadre d’une solution plus étendue de type Private 5G.<ul><li>Inscription/désinscription des UE</li><li>Mise à jour des inscriptions de mobilité/Mise à jour des inscriptions périodiques</li><li>Demande de service effectuée par un UE (signalisation/données)</li><li>Publication du contexte des UE lancée par le réseau ou le réseau d’accès</li><li>Établissement des sessions PDU</li><li>Publication des sessions PDU</li><li>Transfert basé sur le nœud N2 inter NG-RAN</li><li>Transfert inter NG-RAN basé sur Xn</li><li>Notification ou émission de données de liaisons descendantes lancées par le réseau</li>        |
|**Authentification des UE**     |<ul><li>Prise en charge SEAF pour fournir une fonctionnalité d’authentification dans le réseau de distribution.</li><li>Authentification à l’aide d’identificateurs SUPI, d’identificateurs SUCI et d’identités 5G-GUTI.</li><li>Affectation ou réallocation d’une identité 5G-GUTI à un UE.</li><li>EAP-AKA et 5G-AKA pour l’authentification mutuelle entre les UE et le réseau.</li><li>EPS-AKA pour les déploiements en mode 4G.</li>         |
|**Gestion du contexte de sécurité des UE**     |La fonction AMF de Fusion Core assure le chiffrement et la protection de l’intégrité du NAS 5G. Pendant l’inscription d’un UE, l’UE comprend ses fonctionnalités de sécurité pour le NAS 5G avec des clés 128 bits.<br><br>Les algorithmes pris en charge par Fusion Core pour la protection du chiffrement et de l’intégrité sont les suivants.<ul><li>Algorithme de chiffrement null 5GS</li><li>Snow3G 128 bits</li><li>AES 128 bits</li><li>ZUC 128 bits</li>        |
|**Configuration de la MTU des UE**     |La fonction SMF de Fusion Core signale la MTU entre un réseau de données et les UE lors d’une requête dans le cadre des procédures d’établissement des sessions PDU, afin d’éviter la fragmentation.         |
|**Contrôle de stratégie**     |Fusion Core applique les décisions de contrôle des stratégies de bout en bout et les met en application pour chaque session PDU et chaque flux. Cela offre la flexibilité nécessaire pour appliquer différents niveaux de QoS à certains flux ou UE. Cela vous permet également d’exposer de manière sélective des services ou des réseaux de données à des groupes d’UE et de contrôler les types de trafic circulant sur le réseau.         |
|**Listes ACL UPF**     |Les listes de contrôle d’accès peuvent être utilisées pour autoriser ou bloquer le trafic provenant de plages d’adresses IPv4 ou se dirigeant vers de telles plages. Vous pouvez configurer des listes de contrôle d’accès dans les interfaces d’accès (N3) et de base (N6) de Fusion Core.         |
|**Index RAT/RFSP**     |La fonction AMF de Fusion Core peut fournir à un réseau RAN un index RFSP, que le RAN peut faire correspondre avec sa configuration locale pour appliquer des stratégies de gestion des ressources radio, telles que la resélection cellulaire ou la redirection de la couche de fréquence.         |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer Fusion Core](deploy-metaswitch-fusion-core-solution.md).

