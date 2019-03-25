---
title: Configuration système requise pour Microsoft Azure Data Box Edge | Microsoft Docs
description: En savoir plus sur les conditions logicielles et réseau requises pour votre solution Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 125ad28f049662ae6d91c61bb5ee79c1c1428af5
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401754"
---
# <a name="azure-data-box-edge-system-requirements"></a>Configuration requise Edge de zone de données Azure

Cet article décrit la configuration système importante pour votre solution Microsoft Azure Data Box Edge et pour les clients se connectant à Azure Data Box Edge. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre solution Data Box Edge. Reportez-vous aussi souvent que nécessaire à ces informations pendant le déploiement, et après, pour son fonctionnement.

La configuration système requise pour Data Box Edge inclut ce qui suit :

- **Configuration logicielle pour les hôtes** : décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur de configuration locale, les clients SMB et les exigences supplémentaires pour les clients qui accèdent à l’appareil.
- **Configuration réseau pour l’appareil** : fournit des informations sur la configuration réseau nécessaire au fonctionnement de l’appareil physique.

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

## <a name="networking-port-requirements"></a>Configuration requise du port réseau

### <a name="port-requirements-for-data-box-edge"></a>Configuration requise du port pour Data Box Edge

Le tableau ci-dessous répertorie les ports qui doivent être ouverts dans votre pare-feu pour autoriser le trafic SMB, cloud ou de gestion. Dans ce tableau, *entrée* ou *entrant* représente la direction à partir de laquelle les requêtes clientes entrantes accèdent à votre appareil. *Sortie* ou *sortant* représente la direction vers laquelle votre appareil Data Box Edge envoie des données de façon externe, au-delà du déploiement : par exemple, sortant vers Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Configuration requise du port pour IoT Edge

Azure IoT Edge permet les communications sortantes entre un appareil Edge local et le cloud Azure au moyen des protocoles IoT Hub pris en charge. Les communications entrantes sont uniquement requises pour des scénarios spécifiques où Azure IoT Hub a besoin d’envoyer (push) des messages à l’appareil Azure IoT Edge (par exemple, messagerie cloud vers appareil).

Utilisez le tableau suivant pour configurer les ports des serveurs hébergeant le runtime Azure IoT Edge :

| N° de port | Entrant ou sortant | Étendue de ports | Obligatoire | Assistance |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Sortie       | WAN        | Oui      | Sortie ouverte pour le déploiement de IoT Edge. Cette configuration est requise en cas d’utilisation de scripts manuels ou du service Azure IoT Device Provisioning.|

Pour plus d'informations, consultez [Règles de configuration du pare-feu et des ports pour le déploiement d’IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour règles de pare-feu

Les administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur des modèles d’URL afin de filtrer le trafic entrant et sortant. Votre appareil Data Box Edge et le service dépendent d’autres applications Microsoft comme Azure Service Bus, Azure Active Directory Access Control, des comptes de stockage et des serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent être utilisés pour configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Ces modifications impliquent que l’administrateur réseau surveille et mette à jour les règles de pare-feu pour votre appareil Data Box Edge, si nécessaire.

Dans la plupart des cas, nous vous recommandons de définir librement les règles de pare-feu pour le trafic sortant en fonction des adresses IP fixes Data Box Edge. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancées qui sont nécessaires à la création d’environnements sécurisés.

> [!NOTE]
> - Les adresses IP d’appareil (sources) doivent toujours être définies sur l’ensemble des interfaces réseau activées pour le cloud.
> - Les adresses IP de destination doivent être définies sur les [plages d’adresses IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Modèles d’URL pour la fonctionnalité de passerelle

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Modèles d’URL pour la fonctionnalité de calcul

| Modèle d’URL                      | Composant ou fonctionnalité                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Registre de conteneurs Microsoft (obligatoire)               |   |
| https://\*.azurecr.io                     | Registres de conteneurs personnels et tiers (facultatif) |   |
| https://\*.azure-devices.net              | Accès IoT Hub (obligatoire)                             |   |

## <a name="internet-bandwidth"></a>Bande passante Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Étape suivante

- [Déployer votre solution Azure Data Box Edge](data-box-Edge-deploy-prep.md)
