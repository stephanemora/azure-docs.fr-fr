---
title: Prise en charge d’Azure IoT Hub pour les réseaux virtuels
description: Guide pratique pour utiliser le modèle de connectivité des réseaux virtuels avec IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: rezas
ms.openlocfilehash: 34f66c13b0e7eb7092332a48744f9abfd8f0db80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501437"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Prise en charge d’IoT Hub pour les réseaux virtuels

Cet article présente le modèle de connectivité VNET et explique comment mettre en place une expérience de connectivité privée vers un hub IoT via un réseau virtuel Azure appartenant au client.

> [!NOTE]
> Les fonctionnalités IoT Hub décrites dans cet article sont actuellement disponibles pour les hubs IoT [créés avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity) dans les régions suivantes : USA Est, USA Centre Sud, USA Ouest 2.


## <a name="introduction"></a>Introduction

Par défaut, les noms d'hôtes IoT Hub sont mappés à un point de terminaison public avec une adresse IP routable publiquement via Internet. Comme le montre l'illustration ci-dessous, ce point de terminaison public IoT Hub est partagé entre des hubs appartenant à différents clients, et les appareils IoT peuvent y accéder aussi bien sur des réseaux étendus que sur des réseaux locaux.

Plusieurs fonctionnalités IoT Hub, notamment le [routage de messages](./iot-hub-devguide-messages-d2c.md), le [chargement de fichiers](./iot-hub-devguide-file-upload.md) et l'[importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md) nécessitent également une connectivité IoT Hub vers une ressource Azure appartenant au client via son point de terminaison public. Comme illustré ci-dessous, ces chemins de connectivité constituent collectivement le trafic de sortie entre IoT Hub et les ressources du client.
![Point de terminaison public IoT Hub](./media/virtual-network-support/public-endpoint.png)


Pour plusieurs raisons, les clients peuvent restreindre la connectivité à leurs ressources Azure (y compris IoT Hub) via un réseau virtuel qu'ils possèdent et exploitent. Les raisons sont les suivantes :

* Ajouter des couches de sécurité supplémentaires via l'isolation au niveau du réseau pour votre hub IoT en empêchant l'exposition de la connectivité à votre hub via l’Internet public.

* Permettre une expérience de connectivité privée à partir de vos ressources réseau sur site en garantissant que vos données et votre trafic sont transmis directement au réseau principal Azure.

* Empêcher les attaques par exfiltration à partir de réseaux locaux sensibles. 

* Suivre des modèles de connectivité établis à l'échelle d’Azure en utilisant des [points de terminaison privés](../private-link/private-endpoint-overview.md).


Cet article explique comment atteindre ces objectifs en utilisant des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour la connectivité d'entrée vers le hub IoT, par exemple en utilisant l'exception des services internes Azure approuvés pour la connectivité de sortie du hub IoT vers d'autres ressources Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Connectivité en entrée à IoT Hub à l'aide de points de terminaison privés

Un point de terminaison privé est une adresse IP privée attribuée à l'intérieur d'un réseau virtuel appartenant au client, via lequel une ressource Azure est accessible. En disposant d'un point de terminaison privé pour votre hub IoT, vous permettrez aux services opérant à l'intérieur de votre réseau virtuel d'atteindre le hub IoT sans avoir besoin d'envoyer du trafic vers le point de terminaison public du hub IoT. De même, les appareils qui fonctionnent dans votre installation locale peuvent utiliser le [réseau privé virtuel (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou le peering privé [ExpressRoute](https://azure.microsoft.com/services/expressroute/) pour obtenir une connectivité à votre réseau virtuel dans Azure puis à votre hub IoT (via son point de terminaison privé). Ainsi, les clients qui souhaitent restreindre la connectivité aux points de terminaison publics de leur hub IoT (ou éventuellement la bloquer complètement) peuvent atteindre cet objectif en utilisant les [règles de pare-feu IoT Hub](./iot-hub-ip-filtering.md) tout en conservant la connectivité à leur hub à l'aide du point de terminaison privé.

> [!NOTE]
> Cette configuration s’adresse principalement aux appareils situés à l'intérieur d'un réseau local. Cette configuration n'est pas conseillée pour les appareils déployés dans un réseau étendu.

![Point de terminaison public IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Avant de commencer, assurez-vous que les conditions préalables suivantes sont remplies :

* Votre hub IoT doit être doté d'une [identité de service gérée](#create-an-iot-hub-with-managed-service-identity).

* Votre hub IoT doit être provisionné dans l'une des [régions prises en charge](#regional-availability-private-endpoints).

* Vous provisionné réseau virtuel Azure avec un sous-réseau dans lequel sera créé le point de terminaison privé. Voir [Créer un réseau virtuel à l’aide de l’interface de ligne de commande Azure](../virtual-network/quick-create-cli.md) pour plus de détails.

* Pour les appareils qui fonctionnent à l'intérieur de réseaux locaux, configurez un [réseau privé virtuel (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou un peering privé [ExpressRoute](https://azure.microsoft.com/services/expressroute/) dans votre réseau virtuel Azure.


### <a name="regional-availability-private-endpoints"></a>Disponibilité régionale (points de terminaison privés)

Points de terminaison privés pris en charge dans les hubs IoT créés dans les régions suivantes :

* USA Est

* États-Unis - partie centrale méridionale

* USA Ouest 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Configurer un point de terminaison privé pour l'entrée IoT Hub

Pour configurer un point de terminaison privé, procédez comme suit :

1. Exécutez la commande suivante dans l’interface de ligne de commande Azure pour réenregistrer le fournisseur Azure IoT Hub avec votre abonnement :

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Accédez à l'onglet **Connexions des points de terminaison privés** dans votre portail IoT Hub (cet onglet n'est disponible que dans les hubs IoT des [régions prises en charge](#regional-availability-private-endpoints)), puis cliquez sur le signe **+** pour ajouter un nouveau point de terminaison privé.

3. Fournissez l'abonnement, le groupe de ressources, le nom et la région dans lesquels le nouveau point de terminaison privé sera créé (idéalement, le point de terminaison privé devrait être créé dans la même région que votre hub ; voir la [section sur la disponibilité régionale](#regional-availability-private-endpoints) pour plus de détails).

4. Cliquez sur **Suivant : Ressource**, fournissez l’abonnement de votre ressource IoT Hub, puis sélectionnez **"Microsoft.Devices/IotHubs"** comme type de ressource, le nom de votre hub IoT comme **ressource**, et **iotHub** comme valeur sub-resource cible.

5. Cliquez sur **Suivant : Configuration** et indiquez le réseau virtuel et le sous-réseau où le point de terminaison privé sera créé. Sélectionnez l'option d'intégration dans zone DNS privée Azure, si vous le souhaitez.

6. Cliquez sur **Suivant : Balises**, et spécifiez si nécessaire les balises de votre ressource.

7. Cliquez sur **Vérifier + créer** pour créer votre ressource de point de terminaison privé.


### <a name="pricing-private-endpoints"></a>Tarification (points de terminaison privés)

Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Connectivité entrante d’IoT Hub vers d'autres ressources Azure

IoT Hub nécessite un accès à votre stockage d’objets blob Azure, aux hubs d'événements, aux ressources Service Bus pour le [routage des messages](./iot-hub-devguide-messages-d2c.md), le [chargement des fichiers](./iot-hub-devguide-file-upload.md) et l'[importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md), qui s'effectue généralement sur le point d'extrémité public des ressources. Dans le cas où vous liez votre compte de stockage, vos hubs d'événements ou votre ressource Service Bus à un réseau virtuel, la configuration conseillée bloquera par défaut la connectivité à la ressource. Par conséquent, cela entravera la fonctionnalité d’IoT Hub qui nécessite l'accès à ces ressources.

Pour remédier à cette situation, vous devez activer la connectivité entre votre ressource IoT Hub et votre compte de stockage, vos hubs d'événements ou vos ressources Service Bus via l'option des **services internes Azure approuvés**.

Les conditions préalables sont les suivantes :

* Votre hub IoT doit être provisionné dans l'une des [régions prises en charge](#regional-availability-trusted-microsoft-first-party-services).

* Une identité de service gérée doit être attribuée à votre hub IoT au moment de son provisionnement. Suivez les instructions sur la façon de [créer un hub avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity).


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Disponibilité régionale (services internes Microsoft approuvés)

L'exception des services internes Azure approuvés pour contourner les restrictions du pare-feu vers le stockage Azure, les hubs d'événements et les ressources Service Bus n'est prise en charge que pour les hubs IoT dans les régions suivantes :

* USA Est

* États-Unis - partie centrale méridionale

* USA Ouest 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Tarification (services internes Microsoft approuvés)

La fonction d'exception des services internes Microsoft approuvés est gratuite dans les hubs IoT des [régions prises en charge](#regional-availability-trusted-microsoft-first-party-services). Les frais pour les comptes de stockage provisionnés, les hubs d'événements ou les ressources Service Bus s'appliquent séparément.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Créer un hub IoT avec une identité de service gérée

Une identité de service gérée peut être attribuée à votre hub au moment du provisionnement de la ressource (cette fonctionnalité n'est actuellement pas prise en charge pour les hubs existants), ce qui nécessite que le hub IoT utilise TLS 1.2 comme version minimale. À cette fin, vous devez utiliser le modèle de ressource ARM ci-dessous :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Après avoir substitué les valeurs de votre ressource `name`, `location`, `SKU.name` et `SKU.tier`, vous pouvez utiliser l’interface de ligne de commande Azure pour déployer la ressource dans un groupe de ressources existant en utilisant :

```azurecli-interactive
az group deployment create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Une fois la ressource créée, vous pouvez récupérer l'identité de service géré attribuée à votre hub en utilisant l’interface de ligne de commande Azure :

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Une fois IoT Hub provisionné avec une identité de service gérée, suivez la section correspondante pour configurer le routage des points de terminaison vers les [comptes de stockage](#egress-connectivity-to-storage-account-endpoints-for-routing), les [hubs d'événements](#egress-connectivity-to-event-hubs-endpoints-for-routing) et les ressources [Service Bus](#egress-connectivity-to-service-bus-endpoints-for-routing), ou pour configurer le [chargement des fichiers](#egress-connectivity-to-storage-accounts-for-file-upload) et l'[importation/exportation d’appareils en bloc](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport).


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Connectivité sortante vers les points de terminaison de stockage pour le routage

IoT Hub peut être configuré pour router les messages vers un compte de stockage appartenant au client. Pour permettre à la fonctionnalité de routage d'accéder à un compte de stockage lorsque des restrictions de pare-feu sont en place, votre hub IoT doit disposer d’une identité de service gérée (voir comment [créer un hub avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity)). Une fois qu'une identité de service gérée est fournie, suivez les étapes ci-dessous pour donner à l'identité de ressource de votre hub l’autorisation RBAC d'accéder à votre compte de stockage.

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de votre compte de stockage, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.

2. Sélectionnez **Contributeur aux données Blob du stockage** comme **rôle**, **Utilisateur, groupe ou principal du service Azure AD** comme option **Attribution de l’accès aux applications**, puis sélectionnez le nom de la ressource de votre hub IoT dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .

3. Accédez à l'onglet **Pare-feux et réseaux virtuels** de votre compte de stockage, puis activez l'option **Autoriser l'accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**. Cliquez sur le bouton **Enregistrer** .

4. Sur la page des ressources de votre hub IoT, accédez à l'onglet **Routage de messages**.

5. Accédez à la section **Points de terminaison personnalisés** et cliquez sur **Ajouter**. Sélectionnez **Stockage** comme type de point de terminaison.

6. Sur la page qui s'affiche, nommez votre point de terminaison, sélectionnez le conteneur que vous souhaitez utiliser dans votre stockage d’objets blob, indiquez l'encodage et le format du nom de fichier. Sélectionnez **Attribué par le système** comme **type d'authentification** de votre point de terminaison de stockage. Cliquez sur le bouton **Créer**.

Votre point de terminaison de stockage personnalisé est maintenant configuré pour utiliser l'identité attribuée par le système à votre hub, et il est autorisé à accéder à votre ressource de stockage malgré les restrictions de son pare-feu. Vous pouvez maintenant utiliser ce point de terminaison pour définir une règle de routage.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connectivité sortante vers les points de terminaison de hubs d’événements pour le routage

IoT Hub peut être configuré pour router les messages vers un espace de noms de hubs d’événements appartenant au client. Pour permettre à la fonctionnalité de routage d'accéder à une ressource de hubs d’événements lorsque des restrictions de pare-feu sont en place, votre hub IoT doit disposer d’une identité de service gérée (voir comment [créer un hub avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity)). Une fois qu'une identité de service gérée est fournie, suivez les étapes ci-dessous pour donner à l'identité de ressource de votre hub l’autorisation RBAC d'accéder à vos hubs d’événements.

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de vos hubs d’événements, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.

2. Sélectionnez **Expéditeur de données Azure Event Hubs** comme **rôle**, **Utilisateur, groupe ou principal du service Azure AD** comme option **Attribution de l’accès aux applications**, puis sélectionnez le nom de la ressource de votre hub IoT dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .

3. Accédez à l'onglet **Pare-feux et réseaux virtuels** de vos hubs d’événements, puis activez l'option **Autoriser l'accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder aux hubs d’événements**. Cliquez sur le bouton **Enregistrer** .

4. Sur la page des ressources de votre hub IoT, accédez à l'onglet **Routage de messages**.

5. Accédez à la section **Points de terminaison personnalisés** et cliquez sur **Ajouter**. Sélectionnez **Hubs d’événements** comme type de point de terminaison.

6. Sur la page qui s'affiche, nommez votre point de terminaison, sélectionnez l'espace de noms et l'instance de vos hubs d'événements, puis cliquez sur le bouton **Créer**.

Votre point de terminaison de hubs d’événements personnalisé est maintenant configuré pour utiliser l'identité attribuée par le système à votre hub, et il est autorisé à accéder à votre ressource de hubs d’événements malgré les restrictions de son pare-feu. Vous pouvez maintenant utiliser ce point de terminaison pour définir une règle de routage.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Connectivité sortante vers les points de terminaison Service Bus pour le routage

IoT Hub peut être configuré pour router les messages vers un espace de noms Service Bus appartenant au client. Pour permettre à la fonctionnalité de routage d'accéder à une ressource Service Bus lorsque des restrictions de pare-feu sont en place, votre hub IoT doit disposer d’une identité de service gérée (voir comment [créer un hub avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity)). Une fois qu'une identité de service gérée est fournie, suivez les étapes ci-dessous pour donner à l'identité de ressource de votre hub l’autorisation RBAC d'accéder à Service Bus.

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de Service Bus, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.

2. Sélectionnez **Expéditeur de données Azure Service Bus** comme **rôle**, **Utilisateur, groupe ou principal du service Azure AD** comme option **Attribution de l’accès aux applications**, puis sélectionnez le nom de la ressource de votre hub IoT dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .

3. Accédez à l'onglet **Pare-feux et réseaux virtuels** de Service Bus, puis activez l'option **Autoriser l'accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder à Service Bus**. Cliquez sur le bouton **Enregistrer** .

4. Sur la page des ressources de votre hub IoT, accédez à l'onglet **Routage de messages**.

5. Accédez à la section **Points de terminaison personnalisés** et cliquez sur **Ajouter**. Sélectionnez **File d'attente Service Bus** ou **Rubrique Service Bus** (selon le cas) comme type de point de terminaison.

6. Sur la page qui s'affiche, nommez votre point de terminaison, sélectionnez l'espace de noms ainsi que la file d'attente ou la rubrique Service Bus (selon le cas). Cliquez sur le bouton **Créer**.

Votre point de terminaison Service Bus personnalisé est maintenant configuré pour utiliser l'identité attribuée par le système à votre hub, et il est autorisé à accéder à votre ressource Service Bus malgré les restrictions de son pare-feu. Vous pouvez maintenant utiliser ce point de terminaison pour définir une règle de routage.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Connectivité sortante vers les comptes de stockage pour le chargement de fichiers

La fonction de chargement de fichiers d’IoT Hub permet aux appareils de charger des fichiers sur un compte de stockage appartenant au client. Pour que le chargement de fichiers puisse fonctionner, les deux appareils et IoT Hub doivent être connectés au compte de stockage. Si des restrictions de pare-feu sont en place sur le compte de stockage, vos appareils doivent utiliser l'un des mécanismes du compte de stockage pris en charge (notamment les [points de terminaison privés](../private-link/create-private-endpoint-storage-portal.md), les [points de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) ou la [configuration directe du pare-feu](../storage/common/storage-network-security.md)) pour obtenir une connectivité. De même, si des restrictions de pare-feu sont en place sur le compte de stockage, IoT Hub doit être configuré pour accéder à la ressource de stockage via l'exception des services Microsoft approuvés. À cette fin, votre hub IoT doit avoir une identité de service gérée (voir comment [créer un hub avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity)). Une fois qu'une identité de service gérée est fournie, suivez les étapes ci-dessous pour donner à l'identité de ressource de votre hub l’autorisation RBAC d'accéder à votre compte de stockage.

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de votre compte de stockage, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.

2. Sélectionnez **Contributeur aux données Blob du stockage** comme **rôle**, **Utilisateur, groupe ou principal du service Azure AD** comme option **Attribution de l’accès aux applications**, puis sélectionnez le nom de la ressource de votre hub IoT dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .

3. Accédez à l'onglet **Pare-feux et réseaux virtuels** de votre compte de stockage, puis activez l'option **Autoriser l'accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**. Cliquez sur le bouton **Enregistrer** .

4. Sur la page des ressources de votre hub IoT, accédez à l'onglet **Chargement de fichiers**.

5. Sur la page qui s'affiche, sélectionnez le conteneur que vous comptez utiliser dans votre stockage d’objets blob, puis configurez les options **Paramètres de notification de fichier**, **Durée de vie de SAS**, **Durée de vie par défaut** et **Nombre maximal de diffusions** comme vous le souhaitez. Sélectionnez **Attribué par le système** comme **type d'authentification** de votre point de terminaison de stockage. Cliquez sur le bouton **Créer**.

Votre point de terminaison de stockage pour chargement de fichiers est maintenant configuré pour utiliser l'identité attribuée par le système à votre hub, et il est autorisé à accéder à votre ressource de stockage malgré les restrictions de son pare-feu.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Connectivité sortante vers les comptes de stockage pour l’importation/exportation d’appareils en bloc

IoT Hub prend en charge la fonctionnalité permettant d'[importer/exporter](./iot-hub-bulk-identity-mgmt.md) en bloc les informations des appareils depuis/vers un stockage d’objets blob fourni par le client. Pour que la fonctionnalité d’importation/exportation en bloc puisse fonctionner, les deux appareils et IoT Hub doivent être connectés au compte de stockage.

Cette fonctionnalité nécessite une connectivité entre IoT Hub et le compte de stockage. Pour accéder à une ressource Service Bus lorsque des restrictions de pare-feu sont en place, votre hub IoT doit disposer d’une identité de service gérée (voir comment [créer un hub avec une identité de service gérée](#create-an-iot-hub-with-managed-service-identity)). Une fois qu'une identité de service gérée est fournie, suivez les étapes ci-dessous pour donner à l'identité de ressource de votre hub l’autorisation RBAC d'accéder à Service Bus.

1. Dans le portail Azure, accédez à l'onglet **Contrôle d'accès (IAM)** de votre compte de stockage, puis cliquez sur **Ajouter** dans la section **Ajouter une attribution de rôle**.

2. Sélectionnez **Contributeur aux données Blob du stockage** comme **rôle**, **Utilisateur, groupe ou principal du service Azure AD** comme option **Attribution de l’accès aux applications**, puis sélectionnez le nom de la ressource de votre hub IoT dans la liste déroulante. Cliquez sur le bouton **Enregistrer** .

3. Accédez à l'onglet **Pare-feux et réseaux virtuels** de votre compte de stockage, puis activez l'option **Autoriser l'accès à partir des réseaux sélectionnés**. Sous la liste **Exceptions**, cochez la case **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage**. Cliquez sur le bouton **Enregistrer** .

Vous pouvez désormais utiliser l’API REST Azure IoT pour [créer des tâches d'importation/exportation](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) et obtenir des informations sur la manière d'utiliser la fonctionnalité d'importation/exportation en bloc. Notez que vous devrez fournir l’élément `storageAuthenticationType="identityBased"` dans le corps de votre demande et utiliser `inputBlobContainerUri="https://..."` et `outputBlobContainerUri="https://..."` comme URL d'entrée et de sortie de votre compte de stockage, respectivement.


Les SDK IoT Hub Azure prennent également en charge cette fonctionnalité dans le Gestionnaire du registre du client du service. L’extrait de code suivant indique comment créer une tâche d’importation ou d’exportation en utilisant le SDK C#.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Pour utiliser cette version limitée à une région des SDK Azure IoT avec prise en charge des réseaux virtuels pour C#, Java et Node.js :

1. Créez une variable d'environnement nommée `EnableStorageIdentity` et définissez sa valeur sur `1`.

2. Téléchargez le SDK :  [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Pour Python, téléchargez notre version limitée à partir de GitHub.

1. Accédez à la [page de mise en production GitHub](https://aka.ms/vnetpythonsdk).

2. Téléchargez le fichier suivant, que vous trouverez au bas de la page de mise en production sous l'en-tête **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Ouvrez un terminal et accédez au dossier contenant le fichier téléchargé.

4. Exécutez la commande suivante pour installer le SDK du service Python avec prise en charge des réseaux virtuels :
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Étapes suivantes

Utilisez les liens ci-dessous pour en savoir plus sur les fonctionnalités IoT Hub :

* [Routage de messages](./iot-hub-devguide-messages-d2c.md)
* [Chargement de fichiers](./iot-hub-devguide-file-upload.md)
* [Importation/exportation d’appareils en bloc](./iot-hub-bulk-identity-mgmt.md) 
