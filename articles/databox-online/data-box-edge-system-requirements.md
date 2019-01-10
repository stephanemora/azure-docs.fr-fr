---
title: Configuration système requise pour Microsoft Azure Data Box Edge | Microsoft Docs
description: En savoir plus sur les conditions logicielles et réseau requises pour votre solution Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 4d6060c1774b2ad09c3b813bc107b73309dae801
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020215"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Configuration système Azure Data Box Edge (Préversion)

Cet article décrit la configuration système importante pour votre solution Microsoft Azure Data Box Edge et pour les clients se connectant à Azure Data Box Edge. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre solution Data Box Edge. Reportez-vous aussi souvent que nécessaire à ces informations pendant le déploiement, et après, pour son fonctionnement.

La configuration système requise pour Data Box Edge inclut ce qui suit :

- **Configuration logicielle pour les hôtes** : décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur de configuration locale, les clients SMB et les exigences supplémentaires pour les clients qui accèdent à l’appareil.
- **Configuration réseau pour l’appareil** : fournit des informations sur la configuration réseau nécessaire au fonctionnement de l’appareil physique.

> [!IMPORTANT]
> Data Box Edge est en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution.

## <a name="supported-os-for-clients-connected-to-device"></a>Systèmes d’exploitation pris en charge pour les clients connectés à l’appareil

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocoles pris en charge pour l’accès des clients à l’appareil

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Types de stockage pris en charge

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navigateurs pris en charge pour l’interface utilisateur web locale

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="port-configuration-for-data-box-edge"></a>Configuration du port pour Data Box Edge

Le tableau ci-dessous répertorie les ports qui doivent être ouverts dans votre pare-feu pour autoriser le trafic SMB, cloud ou de gestion. Dans ce tableau, *entrée* ou *entrant* représente la direction à partir de laquelle les requêtes clientes entrantes accèdent à votre appareil. *Sortie* ou *sortant* représente la direction vers laquelle votre appareil Data Box Edge envoie des données de façon externe, au-delà du déploiement : par exemple, sortant vers Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>Configuration du port pour IoT Edge

Azure IoT Edge permet les communications sortantes entre un appareil Edge local et le cloud Azure au moyen des protocoles IoT Hub pris en charge. Les communications entrantes sont uniquement requises pour des scénarios spécifiques où Azure IoT Hub a besoin d’envoyer (push) des messages à l’appareil Azure IoT Edge (par exemple, messagerie cloud vers appareil).

Utilisez le tableau suivant pour configurer les ports des serveurs hébergeant le runtime Azure IoT Edge :

| N° de port | Entrant ou sortant | Étendue de ports | Obligatoire | Assistance |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| Sortie       | WAN        | Oui      | Protocole de communication par défaut pour IoT Edge. Doit être ouvert si Azure IoT Edge n’est pas configuré pour les autres protocoles pris en charge ou si AMQP est le protocole de communication souhaité. <br>5672 pour AMQP n’est pas pris en charge par IoT Edge. <br>Bloquez ce port quand Azure IoT Edge utilise un autre protocole IoT Hub pris en charge. |
| TCP 443 (HTTPS)| Sortie       | WAN        | Oui      | Sortie ouverte pour le déploiement de IoT Edge. Si vous disposez d’une passerelle transparente avec des appareils de nœud terminal pouvant envoyer des requêtes de méthode. Dans ce cas, le port 443 n’a pas besoin d’être ouvert aux réseaux externes pour se connecter à IoTHub ou fournir des services IoTHub via Azure IoT Edge. La règle entrante peut donc être limitée uniquement à l’ouverture du trafic entrant en provenance du réseau interne. |
| TCP 5671 (AMQP) | Dans        |            | Non        | Les connexions entrantes doivent être bloquées.|
| TCP 443 (HTTPS) | Dans        |            | Dans certains cas, consultez les commentaires. | Les connexions entrantes ne peuvent être ouvertes que dans certaines situations uniquement. Si les protocoles non HTTP tels que AMQP, MQTT ne peuvent pas être configurés, les messages peuvent être envoyés sur WebSockets via le port 443. |

Pour plus d'informations, consultez [Règles de configuration du pare-feu et des ports pour le déploiement d’IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour règles de pare-feu

Les administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur des modèles d’URL afin de filtrer le trafic entrant et sortant. Votre appareil Data Box Edge et le service dépendent d’autres applications Microsoft comme Azure Service Bus, Azure Active Directory Access Control, des comptes de stockage et des serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent être utilisés pour configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Ces modifications impliquent que l’administrateur réseau surveille et mette à jour les règles de pare-feu pour votre appareil Data Box Edge, si nécessaire.

Dans la plupart des cas, nous vous recommandons de définir librement les règles de pare-feu pour le trafic sortant en fonction des adresses IP fixes Data Box Edge. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancées qui sont nécessaires à la création d’environnements sécurisés.

> [!NOTE]
> - Les adresses IP d’appareil (sources) doivent toujours être définies sur l’ensemble des interfaces réseau activées pour le cloud.
> - Les adresses IP de destination doivent être définies sur les [plages d’adresses IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|    Modèle d’URL                                                                                                                                                                                                                                                                                                                                                                                                                                                      |    Composant ou fonctionnalité                                                                           |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                                                                           |    Service Azure Data Box Edge<br>Azure Service Bus<br>Service d’authentification                           |
|    http://\*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                                |    Activation d’appareil                                                                                    |
|    http://crl.microsoft.com/pki/\*<br>http://www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    Révocation de certificat                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                            |    Comptes de stockage Azure et surveillance                                                                |
|    http://windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Serveurs Microsoft Update                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    CDN Akamai                                                                                           |
|    https://\*.partners.extranet.microsoft.com/\*                                                                                                                                                                                                                                                                                                                                                                                                                    |    Package de prise en charge                                                                                      |
|    http://\*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                                     |    Service de télémétrie dans Windows, consultez la mise à jour de l’expérience client et de la télémétrie de diagnostic      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                         |

## <a name="internet-bandwidth"></a>Bande passante Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Étape suivante

* [Déployer votre solution Azure Data Box Edge](data-box-Edge-deploy-prep.md)
