---
title: Présentation des certificats Azure IoT Edge | Microsoft Docs
description: Découvrez les certificats Azure IoT Edge et les modes d’utilisation.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 09/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ff85f2c9ef708c7cbac9be4933541f063a6c95b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568588"
---
# <a name="azure-iot-edge-certificate-usage-detail"></a>Utilisation détaillée des certificats Azure IoT Edge

Les certificats IoT Edge sont utilisés pour les modules et les appareils IoT en aval afin de vérifier l’identité et la légitimité du module d’exécution [Edge Hub](iot-edge-runtime.md#iot-edge-hub) auquel ils se connectent. Ces vérifications permettent une connexion sécurisée TLS entre le runtime, les modules et les appareils IoT. Tout comme IoT Hub, IoT Edge nécessite une connexion sécurisée et chiffrée à partir d’appareils IoT en aval (ou feuilles) et de modules IoT Edge. Pour établir une connexion TLS sécurisée, le module Edge Hub présente une chaîne d’approbation de serveur aux clients de la connexion pour que ces derniers puissent vérifier son identité.

Pour lever tout doute sur l’utilisation des certificats par IoT Edge, cet article explique comment le fonctionnement des certificats IoT Edge dans des scénarios à l’échelle, ainsi que dans des scénarios de développement et de test. Bien que les scripts diffèrent (PowerShell/bash), les concepts sont identiques pour Linux et Windows.

## <a name="iot-edge-certificates"></a>Certificats IoT Edge

Dans la plupart des cas, les fabricants sont des entités indépendantes des utilisateurs finaux d’un appareil Edge. Parfois, la seule relation entre le fabricant et l’opérateur est l’achat d’un appareil Edge disponible dans le commerce. Il peut également s’agir d’un fabricant sous contrat qui crée un appareil Edge pour le compte de l’opérateur. La conception de certificat IoT Edge tente de tenir compte de ces deux scénarios.

Le schéma suivant illustre l’utilisation des certificats par IoT Edge. Il peut y avoir un ou plusieurs certificats de signature intermédiaires (ou aucun) entre le certificat d’autorité de certification racine et le certificat d’autorité de certification de l’appareil. Voici un exemple variable selon le nombre d’entités impliquées.

![Diagramme des relations de certificat standard](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Autorité de certification

L’autorité de certification (ou « CA ») est une entité qui émet des certificats numériques. Une autorité de certification agit comme un tiers de confiance entre le propriétaire et le destinataire du certificat. Un certificat numérique certifie que le destinataire du certificat détient une clé publique. La chaîne d’approbation de confiance fonctionne en émettant initialement un certificat racine, qui sert de base pour tous les certificats émis par l’autorité. Par la suite, le propriétaire peut utiliser le certificat racine pour émettre des certificats intermédiaires supplémentaires (certificats « feuilles »).

### <a name="root-ca-certificate"></a>Certificat d’autorité de certification racine

Un certificat d’autorité de certification racine est la source de confiance de l’ensemble du processus. Dans les scénarios de production, le certificat est généralement acheté auprès d’une autorité de certification commerciale de confiance comme Baltimore, Verisign, DigiCert, etc. Si vous avez un contrôle complet sur les dispositifs qui se connectent à vos appareils Edge, il est possible d’utiliser une autorité de certification de niveau d’entreprise. Dans les deux cas, la chaîne d’approbation dans son intégralité remonte vers celle-ci à partir de Edge Hub. Les appareils IoT feuilles doivent donc approuver le certificat racine. Vous pouvez stocker le certificat d’autorité de certification racine dans le magasin d’autorité de certification racine de confiance, ou fournir les détails du certificat d’autorité de certification racine dans votre code lorsque le client IoT se connecte à IoT Edge.

### <a name="intermediate-certificates"></a>Certificats intermédiaires

Dans un processus de fabrication standard d’appareils sécurisés, les certificats d’autorité de certification racine sont rarement utilisés directement, essentiellement en raison du risque de fuite ou d’exposition. En règle générale, un ou plusieurs certificats d’autorité de certification intermédiaires sont créés et signés numériquement par le certificat d’autorité de certification racine. Il peut n’y en avoir qu’un, ou il peut y avoir une chaîne de certificats intermédiaires. Les scénarios qui nécessitent une chaîne de certificats intermédiaires sont les suivants :

* Hiérarchie de départements chez un fabricant.

* Plusieurs entreprises impliquées successivement dans la production d’un appareil.

* Client achetant une autorité de certification racine et obtenant un certificat de signature permettant au fabricant de signer les appareils qu’il fabrique pour le compte de ce client.

Dans tous les cas, le fabricant utilise un certificat d’autorité de certification intermédiaire à la fin de cette chaîne pour signer le certificat d’autorité de certification placé sur l’appareil final. En règle générale, ces certificats intermédiaires sont jalousement gardés à l’usine de fabrication. Ils sont soumis à des processus rigoureux, physiques et électroniques, pour leur utilisation.

### <a name="device-ca-certificate"></a>Certificat d’autorité de certification d’appareil

Le certificat d’autorité de certification de l’appareil est généré à partir du certificat d’autorité de certification intermédiaire final (et signé par celui-ci) dans le processus. Ce certificat est installé sur l’appareil Edge, de préférence dans un stockage sécurisé, tel qu’un module de sécurité matériel (HSM). En outre, un certificat d’autorité de certification d’appareil identifie de façon unique un appareil IoT Edge. Pour IoT Edge, le certificat d’autorité de certification d’appareil est capable de délivrer des autres certificats. Par exemple, le certificat d’autorité de certification d’appareil émet des certificats d’appareil feuille servant à authentifier les appareils pour le [service de provisionnement des appareils Azure IoT](..\iot-dps\about-iot-dps.md).

### <a name="iot-edge-workload-ca"></a>Autorité de certification pour la charge de travail IoT Edge

Ce certificat, le premier côté « opérateur » du processus, est généré par le [gestionnaire de sécurité IoT Edge](iot-edge-security-manager.md) au démarrage d’IoT Edge. Ce certificat est généré à partir du certificat d’autorité de certification d’appareil (et signé par celui-ci). Ce certificat, qui est simplement un autre certificat de signature intermédiaire, sert à générer et à signer tous les autres certificats utilisés par le runtime IoT Edge. Aujourd’hui, il s’agit principalement du certificat de serveur Edge Hub abordé dans la section suivante, mais à l’avenir, cela peut inclure les autres certificats d’authentification des composants IoT Edge.

### <a name="edge-hub-server-certificate"></a>Certificat de serveur Edge Hub

Le certificat de serveur Edge Hub est le certificat réel présenté aux modules et aux appareils feuilles pour vérifier l’identité lors de l’établissement de la connexion TLS requise par IoT Edge. Ce certificat présente la chaîne complète des certificats de signature utilisés pour le générer jusqu’au certificat d’autorité de certification racine, que l’appareil IoT feuille doit approuver. Lors de la génération par le gestionnaire de sécurité IoT Edge, le nom commun (CN), de ce certificat Edge Hub est défini sur le nom fourni par la propriété « hostname » dans le fichier config.yaml après la conversion en minuscules. Il s’agit d’une source courante de confusion avec IoT Edge.

## <a name="production-implications"></a>Implications en matière de production

On peut raisonnablement se demander pourquoi IoT Edge a besoin d’un certificat supplémentaire pour l’autorité de certification de la charge de travail. Pourquoi ne pas utiliser le certificat d’autorité de certification d’appareil pour générer directement le certificat de serveur Edge Hub ? Techniquement, c’est possible. Toutefois, l’objectif de ce certificat intermédiaire de « charge de travail » consiste à séparer les responsabilités entre le fabricant de l’appareil et l’opérateur. Imaginez un scénario où un appareil IoT Edge est vendu ou transféré d’un client à un autre. Il est sans doute préférable que le certificat d’autorité de certification de l’appareil fourni par le fabricant soit immuable. Cependant, les certificats de « charge de travail » propres au fonctionnement de l’appareil doivent être supprimés et recréés pour le nouveau déploiement.

Étant donné que les processus du fabricant et de l’opérateur sont séparés, il existe quelques facteurs à prendre en considération concernant le déploiement d’IoT Edge pour les appareils de production.

* Avec n’importe quel processus reposant sur un certificat, le certificat d’autorité de certification racine et tous les certificats d’autorité de certification intermédiaires doivent être sécurisés et surveillés pendant tout le processus de déploiement d’un appareil IoT Edge. Le fabricant de l’appareil IoT Edge doit mettre en place des processus stricts pour un stockage et une utilisation appropriés de ses certificats intermédiaires. En outre, le certificat d’autorité de certification de l’appareil doit être conservé dans un emplacement de stockage aussi sécurisé que possible sur l’appareil lui-même, de préférence un module de sécurité matériel.

* Étant donné que le certificat de serveur Edge Hub est présenté par Edge Hub aux appareils clients et les modules cibles de la connexion, le nom commun (CN) du certificat d’autorité de certification d’appareil **ne doit pas être** identique au « nom d’hôte » utilisé dans le fichier config.yaml sur l’appareil IoT Edge. Le nom utilisé par les clients pour se connecter à IoT Edge (par exemple, via le paramètre GatewayHostName de la chaîne de connexion ou la commande CONNECT de MQTT) **ne peut pas être** le nom commun utilisé dans le certificat d’autorité de certification d’appareil. La raison est que Hub Edge présente sa chaîne d’approbation dans son intégralité pour la vérification par les clients. Si le certificat de serveur Edge Hub et le certificat d’autorité de certification d’appareil ont tous deux le même nom commun (CN), vous obtenez une boucle de vérification, et le certificat est invalidé.

* Étant donné que le certificat d’autorité de certification d’appareil est utilisé par le démon de sécurité IoT Edge pour générer les certificats IoT Edge finaux, il doit lui-même être un certificat de signature, ce qui signifie qu’il a des fonctionnalités de signature de certificat. Pour en faire un certificat d’autorité de certification de signature, l’application paramètre « V3 Basic constraints CA:True » au certificat d’autorité de certification d’appareil configure automatiquement les propriétés d’utilisation de clé requises.

>[!Tip]
> Si vous avez déjà configuré IoT Edge en tant que passerelle transparente dans un scénario de développement/test à l’aide de nos « scripts de commodité » (voir la section suivante) et utilisé le même nom d’hôte lors de la création du certificat d’autorité de certification d’appareil que pour le nom d’hôte dans le fichier config.yaml, vous vous demandez peut-être pourquoi cela a fonctionné. Dans le but de simplifier l’expérience de développement, les scripts de commodité ajoutent « .ca » à la fin du nom que vous transmettez dans le script. Par conséquent, si vous avez utilisé « mygateway » comme nom de périphérique dans les scripts et comme nom d’hôte dans le fichier config.yaml, la première occurrence devient en mygateway.ca avant d’être utilisée en tant que nom commun (CN) pour le certificat d’autorité de certification d’appareil.

## <a name="devtest-implications"></a>Implications en matière de développement/test

Pour faciliter les scénarios de développement et de test, Microsoft fournit un ensemble de [scripts de commodité](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) permettant de générer des certificats hors production appropriés pour IoT Edge dans le scénario de passerelle transparente. Pour obtenir des exemples de fonctionnement des scripts, voir [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md).

Ces scripts génèrent des certificats qui suivent la structure de chaîne d’approbation expliquée dans cet article. Les commandes suivantes génèrent le « certificat d’autorité de certification racine » et un seul « certificat d’autorité de certification intermédiaire ».

```bash
./certGen.sh create_root_and_intermediate 
```

```Powershell
New-CACertsCertChain rsa 
```

De même, ces commandes génèrent le « certificat d’autorité de certification d’appareil ».

```bash
./certGen.sh create_edge_device_certificate "<gateway device name>"
```

```Powershell
New-CACertsEdgeDevice "<gateway device name>"
```

* Le **\<nom d’appareil de passerelle\>** transmis dans ces scripts **ne doit pas** être le même que le paramètre « hostname » du fichier config.yaml. Comme mentionné, les scripts vous aident à éviter les problèmes en ajoutant une chaîne « .ca » au **\<nom d’appareil de passerelle\>** afin d’éviter le conflit de noms dans le cas où un utilisateur a configuré Edge à l’aide du même nom aux deux emplacements. Toutefois, il est conseillé d’éviter d’utiliser le même nom.

>[!Tip]
> Pour connecter vos appareils IoT feuille et applications utilisant notre Kit de développement logiciel (SDK) d’appareil IoT via IoT Edge, vous devez ajouter le paramètre GatewayHostName facultatif à la fin de la chaîne de connexion de l’appareil. Lorsque le certificat de serveur Edge Hub est généré, il est basé sur une version en minuscules du nom d’hôte indiqué dans le fichier config.yaml. Par conséquent, pour que les noms correspondent et que la vérification du certificat TLS réussisse, vous devez saisir le paramètre GatewayHostName en minuscules.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Exemple de hiérarchie de certificats IoT Edge

Pour illustrer ce chemin d’accès de certificat, la capture d’écran suivante montre un appareil IoT Edge configuré en tant que passerelle transparente. OpenSSL est utilisé pour se connecter à Edge Hub, puis valider et vider les certificats.

![Capture d’écran de la hiérarchie de certificats à chaque niveau](./media/iot-edge-certs/iotedge-cert-chain.png)

Observez la hiérarchie des certificats représentée dans la capture d’écran :

| Certificat d’autorité de certification racine         | Certification d’autorité de certification Azure IoT Hub - Test uniquement                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Certificat d’autorité de certification intermédiaire | Certification intermédiaire Azure IoT Hub - Test uniquement                                                                 |
| Certificat d’autorité de certification d’appareil       | iotgateway.ca (« iotgateway » a été transmis comme  < nom d’hôte de passerelle > aux scripts de commodité)      |
| Certificat d’autorité de certification de la charge de travail     | Autorité de certification pour la charge de travail IoT Edge                                                                                       |
| Certificat de serveur Edge Hub | iotedgegw.local (correspond au nom d’hôte du fichier config.yaml)                                                |

## <a name="next-steps"></a>Étapes suivantes

[Présentation des modules Azure IoT Edge](iot-edge-modules.md)

[Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md)