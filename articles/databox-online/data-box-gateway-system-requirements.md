---
title: Configuration système Microsoft Azure Data Box Gateway | Microsoft Docs
description: En savoir plus sur la configuration logicielle et réseau requise pour Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 81df0a776cd22490342230567deacb23097cd12e
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094187"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Configuration système Azure Data Box Gateway (préversion)

Cet article décrit la configuration système importante pour votre solution Microsoft Azure Data Box Gateway et pour les clients se connectant à Azure Data Box Gateway. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre solution Data Box Gateway, puis d’y revenir si nécessaire pendant le déploiement et les opérations suivantes.

La configuration système requise pour l’appareil virtuel Data Box Gateway inclut les aspects suivants :

- **Configuration logicielle pour les hôtes** : décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur de configuration locale, les clients SMB et des exigences supplémentaires pour les hôtes qui se connectent à l’appareil.
- **Configuration réseau pour l’appareil** : fournit des informations sur la configuration réseau nécessaire au fonctionnement de l’appareil virtuel.

> [!IMPORTANT]
> Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution. 

## <a name="specifications-for-the-virtual-device"></a>Spécifications pour l’appareil virtuel

Le système hôte sous-jacent pour le Data Box Gateway est en mesure de dédier les ressources suivantes pour approvisionner votre appareil virtuel :

| Spécifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Processeurs virtuels (cœurs)   | 4 minimum |            
| Mémoire  | 8 Go minimum|
| Disponibilité|Nœud unique|
| Disques| Disque de système d’exploitation : 250 Go <br> Disque de données : 2 To minimum, allocation dynamique et sauvegarde sur SSD|
| Interfaces réseau|1 ou plusieurs interfaces réseau virtuelles|


## <a name="supported-os-for-clients-connected-to-device"></a>Systèmes d’exploitation pris en charge pour les clients connectés à l’appareil

Voici la liste des systèmes d’exploitation pris en charge pour les clients ou hôtes connectés à Data Box Gateway.

| **Système d’exploitation/plateforme** | **Versions** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 <br> 2019 |
|  Windows |8, 10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocoles pris en charge pour l’accès des clients à l’appareil

|**Protocole** |**Versions**   |**Remarques**  |
|---------|---------|---------|
|SMB    | 2.X, 3.X      | La version SMB 1 n’est pas prise en charge.|
|NFS     | V3 et V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Plateformes de virtualisation prises en charge pour l’appareil

| **Système d’exploitation/plateforme**  |**Versions**   |**Remarques**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Les outils VMware ne sont pas pris en charge.         |


## <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

Voici une liste des comptes de stockage pris en charge pour Data Box Gateway.

| **Compte de stockage** | **Remarques** |
| --- | --- |
| Classique | standard |
| Usage général  |Standard ; V1 et V2 sont pris en charge. Les niveaux chaud et froid sont tous les deux pris en charge. |


## <a name="supported-storage-types"></a>Types de stockage pris en charge

Voici une liste des types de stockage pris en charge pour Data Box Gateway.

| **Format de fichier** | **Remarques** |
| --- | --- |
| Objet blob de blocs Azure | |
| Objet blob de pages Azure  | |
| Azure Files | |

## <a name="supported-browsers-for-local-web-ui"></a>Navigateurs pris en charge pour l’interface utilisateur web locale

Voici une liste des navigateurs pris en charge pour l’interface utilisateur web locale de l’appareil virtuel.

|Browser  |Versions  |Conditions/remarques supplémentaires  |
|---------|---------|---------|
|Google Chrome   |Version la plus récente         |         |
|Microsoft Edge    | Version la plus récente        |         |
|Internet Explorer     | Version la plus récente        |         |
|FireFox    |Version la plus récente         |         |


## <a name="networking-requirements"></a>Configuration requise du réseau

Le tableau ci-dessous répertorie les ports qui doivent être ouverts dans votre pare-feu pour autoriser le trafic SMB, cloud ou de gestion. Dans ce tableau, *entrée* ou *entrant* représente la direction à partir de laquelle les requêtes clientes entrantes accèdent à votre appareil. *Sortie* ou *sortant* représente la direction vers laquelle votre appareil Data Box Gateway envoie des données de façon externe, au-delà du déploiement : par exemple, sortant vers Internet.

| N° de port| Entrant ou sortant | Étendue de ports| Obligatoire|   Notes                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|Sortie|WAN |Non |Le port de sortie est utilisé pour accéder à Internet afin de récupérer les mises à jour. <br>Le proxy web sortant est configurable par l’utilisateur. |                          
| TCP 443 (HTTPS)|Sortie|WAN|Oui|Le port de sortie est utilisé pour accéder aux données dans le cloud.<br>Le proxy web sortant est configurable par l’utilisateur.|   
| UDP 53 (DNS)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DNS Internet.<br>Nous vous recommandons d’utiliser un serveur DNS local. |
| UDP 123 (NTP)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur NTP Internet.  |
| UDP 67 (DHCP)|Sortie|WAN|Dans certains cas<br>Voir les remarques|Ce port est requis seulement si vous utilisez un serveur DHCP.  |
| TCP 80 (HTTP)|Dans|LAN|Oui|Il s’agit du port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. <br>L’accès à l’interface utilisateur locale par le biais du protocole HTTP est automatiquement redirigé vers HTTPS.  | 
| TCP 443 (HTTPS)|Dans|LAN|Oui|Il s’agit du port d’entrée pour l’interface utilisateur locale de l’appareil pour la gestion locale. | 

## <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour règles de pare-feu

Les administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur des modèles d’URL afin de filtrer le trafic entrant et sortant. Votre appareil Data Box Gateway et le service Data Box Gateway dépendent d’autres applications Microsoft comme Azure Service Bus, Azure Active Directory Access Control, des comptes de stockage et des serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent être utilisés pour configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. L’administrateur réseau doit alors surveiller et mettre à jour les règles de pare-feu pour votre appareil Data Box Gateway si nécessaire.

Dans la plupart des cas, nous vous recommandons de définir librement les règles de pare-feu pour le trafic sortant en fonction des adresses IP fixes Data Box Gateway. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancées qui sont nécessaires à la création d’environnements sécurisés.

> [!NOTE]
> - Les adresses IP d’appareil (sources) doivent toujours être définies sur l’ensemble des interfaces réseau activées pour le cloud.
> - Les adresses IP de destination doivent être définies sur les [plages d’adresses IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     Modèle d’URL                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Composant/Fonctionnalité                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Service Azure Data Box Gateway<br>Azure Service Bus<br>Service d’authentification    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Activation d’appareil                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Révocation de certificat                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Comptes de stockage Azure et surveillance                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Serveurs Microsoft Update                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    CDN Akamai                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Package de prise en charge                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Service de télémétrie dans Windows, consultez la mise à jour de l’expérience client et de la télémétrie de diagnostic      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Bande passante Internet

La configuration suivante s’applique à la bande passante Internet minimale disponible pour vos appareils Data Box Gateway.

- Votre instance Data Box Gateway est dotée d’une bande passante Internet dédiée de 20 Mbits/s (ou plus) disponible à tout moment. La bande passante ne doit pas être partagée avec d’autres applications. 
- Votre instance Data Box Gateway a une bande passante Internet dédiée de 32 Mbits/s (ou plus) lorsque vous utilisez la limitation de bande passante.

## <a name="next-step"></a>Étape suivante

* [Déployer votre instance Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

