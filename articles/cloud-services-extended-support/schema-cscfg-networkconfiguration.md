---
title: Schéma NetworkConfiguration d’Azure Cloud Services (support étendu) | Microsoft Docs
description: Informations relatives au schéma de configuration réseau pour Azure Cloud Services (support étendu)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ed2d48288bf97fe3ebaa1e8ffc1336d8a82d940e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719022"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Schéma NetworkConfiguration Azure Cloud Services (support étendu)

L’élément `NetworkConfiguration` du fichier de configuration de service spécifie les valeurs du réseau virtuel et DNS. Ces paramètres sont facultatifs pour Azure Cloud Services.

Vous pouvez consulter les ressources suivantes pour en savoir plus sur les réseaux virtuels et les schémas associés :

- [Schéma de configuration du service cloud (support étendu)](schema-cscfg-file.md).
- [Schéma de définition du service cloud (support étendu)](schema-csdef-file.md).
- [Créez un réseau virtuel](../virtual-network/manage-virtual-network.md).

## <a name="networkconfiguration-element"></a>Élément NetworkConfiguration
L’exemple suivant illustre l’élément `NetworkConfiguration` et ses éléments enfants.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

La table suivante décrit les éléments enfants de l’élément `NetworkConfiguration`.

| Élément       | Description |
| ------------- | ----------- |
| AccessControl | facultatif. Spécifie les règles d’accès aux points de terminaison dans un service cloud. Le nom du contrôle d’accès est défini par une chaîne pour l’attribut `name`. L’élément `AccessControl` contient un ou plusieurs éléments `Rule`. Il est possible de définir plusieurs éléments `AccessControl`.|
| Règle | facultatif. Spécifie l’action à réaliser pour une plage de sous-réseau spécifiée d’adresses IP. L’ordre de la règle est défini par une valeur de chaîne pour l’attribut `order`. Plus le numéro de règle est petit, plus la priorité est élevée. Par exemple, vous pouvez spécifier des règles avec des numéros d’ordre de 100, 200 et 300. La règle avec le numéro d’ordre 100 a priorité sur la règle avec le numéro d’ordre 200.<br /><br /> L’action de la règle est définie par une chaîne pour l’attribut `action`. Les valeurs possibles sont les suivantes :<br /><br /> -   `permit` : indique que seuls les paquets de la plage de sous-réseau spécifiée peuvent communiquer avec le point de terminaison.<br />-   `deny` : indique que l’accès est refusé aux points de terminaison dans la plage de sous-réseau spécifiée.<br /><br /> La plage de sous-réseau d’adresses IP affectées par la règle est définie par une chaîne pour l’attribut `remoteSubnet`. La description de la règle est définie par une chaîne pour l’attribut `description`.|
| EndpointAcl | facultatif. Spécifie l’affectation de règles de contrôle d’accès à un point de terminaison. Le nom du rôle qui contient le point de terminaison est défini par une chaîne pour l’attribut `role`. Le nom du point de terminaison est défini par une chaîne pour l’attribut `endpoint`. Le nom de l’ensemble de règles `AccessControl` à appliquer au point de terminaison est défini dans une chaîne pour l’attribut `accessControl`. Il est possible de définir plusieurs éléments `EndpointAcl`.|
| DnsServer | facultatif. Spécifie les paramètres d’un serveur DNS. Vous pouvez spécifier des paramètres pour des serveurs DNS sans réseau virtuel. Le nom du serveur DNS est défini par une chaîne pour l’attribut `name`. L’adresse IP du serveur DNS est définie par une chaîne pour l’attribut `IPAddress`. L'adresse IP doit être une adresse IPv4 valide.|
| VirtualNetworkSite | facultatif. Spécifie le nom du site de réseau virtuel dans lequel vous souhaitez déployer votre service cloud. Ce paramètre ne crée pas de site de réseau virtuel. Il fait référence à un site qui a été défini précédemment dans le fichier réseau pour votre réseau virtuel. Un service cloud ne peut faire partie que d’un seul réseau virtuel. Si vous ne spécifiez pas ce paramètre, le service cloud ne sera pas déployé sur un réseau virtuel. Le nom du site de réseau virtuel est défini par une chaîne pour l’attribut `name`.|
| InstanceAddress | facultatif. Spécifie l’association d’un rôle à un sous-réseau ou à un ensemble de sous-réseaux du réseau virtuel. Quand vous associez un nom de rôle à une adresse d’instance, vous pouvez spécifier les sous-réseaux auxquels vous souhaitez associer ce rôle. L’élément `InstanceAddress` contient un élément Subnets. Le nom du rôle associé aux sous-réseaux est défini par une chaîne pour l’attribut `roleName`.|
| Subnet | facultatif. Spécifie le sous-réseau qui correspond au nom du sous-réseau dans le fichier de configuration réseau. Le nom du sous-réseau est défini par une chaîne pour l’attribut `name`.|
| ReservedIP | facultatif. Spécifie l’adresse IP réservée à associer au déploiement. La méthode d’allocation d’une adresse IP réservée doit être spécifiée comme `Static` pour les déploiements de modèle et PowerShell. Chaque déploiement dans un service cloud peut être associé à une adresse IP réservée. Le nom de l’adresse IP réservée est défini par une chaîne pour l’attribut `name`.|

## <a name="see-also"></a>Voir aussi
[Schéma de configuration du service cloud (support étendu)](schema-cscfg-file.md).
