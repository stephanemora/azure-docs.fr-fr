---
title: Procédure pas à pas pour la solution Usine connectée - Azure | Microsoft Docs
description: Description de l’accélérateur de solution Usine connectée Azure IoT et de son architecture.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 950d248d2525f053981c8642ee2d39021b9a0494
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490357"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>Procédure pas à pas de l’accélérateur de solution Usine connectée

L’[accélérateur de solution][lnk-preconfigured-solutions] Usine connectée est une implémentation de solution industrielle de bout en bout qui :

* Se connecte à la fois aux appareils industriels simulés fonctionnant sur des serveurs OPC UA dans des lignes de production simulées et aux appareils fonctionnant sur des serveurs OPC UA réels. Pour plus d’informations sur l’UA OPC, consultez les [questions fréquentes (FAQ) relatives à Usine connectée](iot-accelerators-faq-cf.md).
* Affiche les indicateurs de performance clé (KPI) opérationnels et les OEE de ces appareils et des lignes de production.
* Montre comment une application basée sur le cloud peut être utilisée pour interagir avec les systèmes de serveur OPC UA.
* Vous permet de connecter vos propres appareils de serveur OPC UA.
* Vous permet de parcourir et de modifier les données de serveur OPC UA.
* S’intègre avec le service Azure Time Series Insights (TSI) pour fournir des vues personnalisées des données à partir de vos serveurs OPC UA.

Vous pouvez utiliser la solution comme point de départ pour votre propre implémentation et la [personnaliser] [ lnk-customize] pour répondre à vos propres exigences professionnelles.

Cet article vous présente quelques éléments clés de la solution Usine connectée pour vous permettre de comprendre son fonctionnement. Cet article décrit aussi le flux de données dans la solution. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels.
* Concevoir votre propre solution IoT utilisant des services Azure.

Pour plus d’informations, consultez les [questions fréquentes (FAQ) relatives à Usine connectée](iot-accelerators-faq-cf.md).

## <a name="logical-architecture"></a>Architecture logique

Le diagramme suivant décrit les composants logiques de l’accélérateur de solution :

![Architecture logique Usine connectée][connected-factory-logical]

## <a name="communication-patterns"></a>Modèles de communication

La solution utilise la [spécification Pub/Sub OPC UA](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) pour envoyer des données de télémétrie OPC UA à IoT Hub au format JSON. Pour ce faire, la solution utilise le module IoT Edge de [l’éditeur d’OPC](https://github.com/Azure/iot-edge-opc-publisher).

La solution dispose également d’un client OPC UA intégré à une application web qui peut établir des connexions avec des serveurs locaux OPC UA. Le client utilise un [proxy inversé](https://wikipedia.org/wiki/Reverse_proxy) et reçoit de l’aide d’IoT Hub pour établir la connexion sans demander de ports ouverts dans le pare-feu local. Ce modèle de communication est appelé « communication assistée par le service ». Pour ce faire, la solution utilise le module IoT Edge de [proxy OPC](https://github.com/Azure/iot-edge-opc-proxy/).


## <a name="simulation"></a>Simulation

Les postes et les systèmes d'exécution de la fabrication (MES) simulés constituent une chaîne de fabrication d’usine. Les appareils simulés et le module Éditeur d’OPC sont basés sur le [Standard OPC UA .NET][lnk-OPC-UA-NET-Standard] publié par la Fondation OPC.

Le Proxy OPC et le module Éditeur d’OPC sont implémentés en tant que modules basés sur [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Chaque ligne de production simulée est associée à une passerelle.

Tous les composants de simulation sont exécutés dans des conteneurs Docker hébergés dans une machine virtuelle Linux Azure. La simulation est configurée pour exécuter huit lignes de production simulées par défaut.

## <a name="simulated-production-line"></a>Ligne de production simulée

Une ligne de production fabrique des parties. Elle est composée de différentes stations : une station d’assembly, une station de test et une station de packaging.

La simulation s’exécute et met à jour les données rendues disponibles via les nœuds OPC UA. Toutes les stations de ligne de production simulées sont orchestrées par le MES via OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Simulation de système d’exécution de fabrication

Le MES analyse chaque poste dans la ligne de production via OPC UA pour détecter les modifications d’état des postes. Il appelle des méthodes OPC UA pour contrôler les stations et transmet les produits d’une station à l’autre jusqu’à la fin de l’opération.

## <a name="gateway-opc-publisher-module"></a>Module Éditeur d’OPC de la passerelle

Le module Éditeur d’OPC se connecte aux serveurs de poste OPC UA et s’abonne aux nœuds OPC à publier. Le module :

1. Convertit les données du nœud au format JSON.
1. Chiffre le code JSON.
1. Envoie le JSON à IoT Hub en tant que OPC UA pub/sub messages.

Le module Éditeur d’OPC nécessite un port sortant https (443) uniquement et fonctionne avec l’infrastructure d’entreprise existante.

## <a name="gateway-opc-proxy-module"></a>Module proxy OPC de la passerelle

Le module proxy OPC UA de la passerelle « tunnele » les messages OPC UA binaires de commande et de contrôle et nécessite seulement un port sortant https (443). Il est compatible avec l’infrastructure d’entreprise existante, y compris les proxys Web.

Il utilise des méthodes d’appareil IoT Hub pour transférer des données TCP/IP en paquets à la couche d’application, et garantit ainsi l’approbation du point de terminaison, le chiffrement des données et l’intégrité à l’aide de SSL/TLS.

Le protocole binaire OPC UA relayé via le proxy utilise le chiffrement et l’authentification UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Le module Éditeur d’OPC de la passerelle s’abonne à des nœuds de serveur OPC UA pour détecter des modifications dans les valeurs de données. Si une modification de données est détectée dans un des nœuds, ce module envoie des messages à Azure IoT Hub.

IoT Hub fournit une source d’événements à Azure STI. STI stocke les données durant 30 jours en fonction des horodatages associés messages. Ces données incluent :

* OPC UA ApplicationUri
* OPC UA NodeId
* Valeur du nœud
* Source timestamp
* OPC UA DisplayName

TSI ne permet pas aux clients de personnaliser la durée pendant laquelle ils souhaitent conserver les données.

TSI interroge les données de nœud à l’aide d’un **SearchSpan** basé sur le temps, et agrège les données en fonction du **OPC UA ApplicationUri**, du **OPC UA NodeId** ou du **OPC UA DisplayName**.

Pour récupérer les données des jauges TRG et KPI, et les graphiques de séries chronologiques, la solution agrège les données selon le nombre d’événements, **Sum**, **Avg**, **Min** et **Max**.

Les séries chronologiques sont créées à l’aide d’un processus différent. La solution calcule les valeurs TRG et KPI à partir des données de base de la station, et propage ces valeurs aux lignes de production, aux usines et à l’entreprise.

En outre, les séries chronologiques pour la topologie OEE et KPI sont calculées dans l’application, chaque fois qu’un intervalle de temps affiché est prêt. Par exemple, l’affichage quotidien est mis à jour chaque heure complète.

L’affichage de séries chronologiques des données de nœud provient directement de STI à l’aide d’une agrégation pour l’intervalle de temps.

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-IoT Hub] reçoit les données envoyées par le module Éditeur d’OPC au cloud et les rend disponibles au service Azure TSI. 

L’instance IoT Hub de la solution effectue également ce qui suit :
- Gère un registre d’identité qui stocke les ID des modules Éditeur d’OPC et de tous les modules de Proxy OPC.
- Est utilisé en tant que canal de transport pour la communication bidirectionnelle du module Proxy OPC.

## <a name="azure-storage"></a>Stockage Azure
La solution utilise le stockage d’objets blob Azure comme stockage sur disque pour la machine virtuelle et pour stocker les données de déploiement.

## <a name="web-app"></a>Application web
L’application web déployée dans le cadre de l’accélérateur de solution comprend un client OPC UA intégré, un système de traitement des alertes et un système de visualisation de télémétrie.

## <a name="telemetry-data-flow"></a>Flux de données de télémétrie

![Flux de données de télémétrie](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Étapes du flux

1. L’éditeur OPC lit les certificats X509 UA OPC et les informations d’identification de sécurité IoT Hub depuis le magasin de certificats local.
    - Si nécessaire, l’éditeur OPC crée et stocke tous les certificats ou informations d’identification manquants dans le magasin de certificats.

2. L’éditeur OPC s’inscrit avec IoT Hub.
    - Utilise le protocole configuré. Peut utiliser n’importe quel protocole pris en charge par le kit de développement logiciel (SDK) de client IoT Hub. La valeur par défaut est MQTT.
    - La communication de protocole est sécurisée par TLS.

3. L’éditeur OPC lit le fichier de configuration.

4. L’éditeur OPC crée une session OPC avec chaque serveur UA OPC configuré.
    - Utilise la connexion TCP.
    - L’éditeur OPC et le serveur UA OPC s’authentifient à l’aide des certificats X509.
    - Tout trafic UA OPC supplémentaire est chiffré par le mécanisme de chiffrement UA OPC configuré.
    - L’éditeur OPC crée un abonnement OPC dans la session OPC, pour chaque intervalle de publication configurée.
    - Crée des éléments surveillés OPC pour les nœuds OPC afin de publier dans l’abonnement OPC.

5. Si la valeur d’un nœud OPC surveillé est modifiée, le serveur UA OPC envoie les mises à jour à l’éditeur OPC.

6. L’éditeur OPC transcode la nouvelle valeur.
    - Traite plusieurs changements si le traitement est activé.
    - Crée un message IoT Hub.

7. L’éditeur OPC envoie un message à IoT Hub.
    - Utilise le protocole configuré.
    - La communication est sécurisée par TLS.

8. Time Series Insights (TSI) lit les messages depuis IoT Hub.
    - Utilise AMQP via TCP/TLS.
    - Cette étape est interne au centre de données.

9. Données au repos dans TSI.

10. Les requêtes d’application web Usine connectée dans Azure App Service nécessitent des données de TSI.
    - Utilise des communications sécurisées TCP/TLS.
    - Cette étape est interne au centre de données.

11. Le navigateur web se connecte à l’application web Usine connectée.
    - Affiche le tableau de bord Usine connectée.
    - Se connecte via le protocole HTTPS.
    - L’accès à l’application Usine connectée nécessite l’authentification de l’utilisateur par le biais d’Azure Active Directory.
    - Tous les appels WepApi dans l’application Usine connectée sont sécurisés par des jetons anti-contrefaçon.

12. Lors des mises à jour des données, l’application web Usine connectée envoie des données mises à jour au navigateur web.
    - Utilise le protocole SignalR.
    - Sécurisé par TCP/TLS.

## <a name="browsing-data-flow"></a>Flux de données de navigation

![Flux de données de navigation](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Étapes du flux

1. Le proxy OPC (composant serveur) démarre.
    - Lit les clés d’accès partagé à partir d’un magasin local.
    - Si nécessaire, stocke les clés d’accès manquantes dans le magasin.

2. Le proxy OPC (composant serveur) s’inscrit avec IoT Hub.
    - Lit tous les périphériques connus de IoT Hub.
    - Utilise MQTT via TLS over Socket ou Secure WebSocket.

3. Le navigateur web se connecte à l’application web Usine connectée et affiche le tableau de bord Usine connectée.
    - Utilise le protocole HTTPS.
    - Un utilisateur sélectionne un serveur UA OPC pour s’y connecter.

4. L’application web Usine connectée établit une session UA OPC dans le serveur UA OPC sélectionné.
    - Utilise la pile UA OPC.

5. Le transport de proxy OPC reçoit une requête de la pile UA OPC pour établir une connexion socket TCP au serveur UA OPC.
    - Il récupère la charge utile TCP et l’utilise telle quelle.
    - Cette étape est interne à l’application web Usine connectée.

6. Le proxy OPC (composant client) cherche le l’appareil proxy OPC (composant serveur) dans le registre de l’appareil IoT Hub. Il appelle ensuite une méthode d’appareil à l’appareil proxy OPC (composant serveur) dans IoT Hub.
    - Utilise le protocole HTTPS via TCP/TLS pour chercher le proxy OPC.
    - Utilise le protocole HTTPS via TCP/TLS pour établir une connexion socket TCP au serveur UA OPC.
    - Cette étape est interne au centre de données.

7. IoT Hub appelle ensuite une méthode d’appareil à l’appareil proxy OPC (composant serveur).
    - Utilise un protocole MQTT établit via des connexions Socket ou Secure Websocket pour établir une connexion socket TCP sur le serveur UA OPC.

8. Le proxy OPC (composant serveur) envoie la charge utile TCP sur le réseau d’atelier.

9. Le serveur UA OPC traite la charge utile et renvoie la réponse.

10. Le socket du proxy OPC (composant serveur) reçoit la réponse.
    - Le proxy OPC envoie les données en tant que valeur renvoyée de la méthode d’appareil à IoT Hub et au proxy OPC (composant client).
    - Ces données sont fournies à la pile UA OPC dans l’application Usine connectée.

11. L’application web Usine connectée renvoie le navigateur OPC UX enrichi avec les informations spécifiques d’UA OPC provenant du serveur UA OPC vers le navigateur web et les affiche.
    - Lorsqu’un utilisateur navigue dans l’espace d’adresse OPC et applique des fonctions aux nœuds de cet espace, le client UX du navigateur OPC utilise des appels AJAX par le biais du protocole sécurisé HTTPS avec des jetons anti-contrefaçon afin d’obtenir des données de l’application web Usine connectée.
    - Si nécessaire, le client utilise les communications détaillées de l’étape 4 à 10 pour échanger des informations avec le serveur UA OPC.

> [!NOTE]
> Le proxy OPC (composant serveur) et le proxy OPC (composant client) réalisent les étapes 4 à 10 pour tout trafic TCP relatifs aux communications UA OPC.

> [!NOTE]
> Pour le serveur UA OPC et la pile UA OPC au sein de l’application web Usine connectée, les communications du proxy OPC sont transparentes et toutes les fonctionnalités de sécurité UA OPC pour l’authentification et le chiffrement s’appliquent.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez poursuivre la prise en main des accélérateurs de solution IoT en lisant les articles suivants :

* [Autorisations sur le site azureiotsolutions.com][lnk-permissions]
* [Déployer une passerelle sur Windows ou Linux pour l’accélérateur de solution usine connectée](iot-accelerators-connected-factory-gateway-deployment.md)
* [Implémentation de référence de l’éditeur OPC](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md).

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
