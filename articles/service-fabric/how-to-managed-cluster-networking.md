---
title: Configurer les paramètres réseau pour les clusters Service Fabric managés
description: Découvrez comment configurer votre cluster Service Fabric géré pour les règles de groupe de sécurité réseau, l’accès aux ports RDP, les règles d’équilibrage de charge, etc.
ms.topic: how-to
ms.date: 5/10/2021
ms.openlocfilehash: 5164a7e3aeb1e82700bd5c5bc4d44e55de64421b
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111895594"
---
# <a name="configure-network-settings-for-service-fabric-managed-clusters"></a>Configurer les paramètres réseau pour les clusters Service Fabric managés

Les clusters Service Fabric managés sont créés avec une configuration réseau par défaut. Cette configuration se compose de règles obligatoires pour les fonctionnalités essentielles des clusters et de quelques règles facultatives, comme l’autorisation de tout le trafic sortant par défaut, destinées à faciliter la configuration client.

Au-delà de la configuration de mise en réseau par défaut, vous pouvez modifier les règles de mise en réseau pour répondre aux besoins de votre scénario.

## <a name="nsg-rules-guidance"></a>Instructions relatives aux règles de groupe de sécurité réseau

Tenez compte de ces considérations lors de la création de règles de groupe de sécurité réseau (NSG, Network Security Group) pour votre cluster géré.

* Les clusters Service Fabric gérés réservent la plage de priorités de 0 à 999 aux fonctionnalités essentielles des règles NSG. Il n’est pas possible de créer de règles NSG personnalisées avec une valeur de priorité inférieure à 1 000.
* Les clusters Service Fabric gérés réservent la plage de priorités de 3 001 à 4 000 à la création de règles NSG facultatives. Ces règles sont ajoutées automatiquement pour faciliter et accélérer la configuration. Vous pouvez les remplacer en ajoutant des règles NSG personnalisées dans la plage de priorités comprise entre 1 000 et 3 000.
* Les règles NSG personnalisées doivent avoir une priorité comprise entre 1 000 et 3 000.

## <a name="apply-nsg-rules"></a>Application de règles NSG

Avec des clusters Service Fabric classiques (non gérés), vous devez déclarer et gérer une ressource *Microsoft.Network/networkSecurityGroups* distincte pour [appliquer des règles NSG à votre cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.servicefabric/service-fabric-secure-nsg-cluster-65-node-3-nodetype). Les clusters Service Fabric gérés vous permettent d’affecter des règles NSG directement dans la ressource de cluster de votre modèle de déploiement.

Utilisez la propriété [networkSecurityRules](/azure/templates/microsoft.servicefabric/managedclusters#managedclusterproperties-object) de votre ressource *Microsoft.ServiceFabric/managedclusters* (version `2021-05-01` ou ultérieure) pour affecter des règles NSG. Par exemple :

```json
            "apiVersion": "2021-05-01",
            "type": "Microsoft.ServiceFabric/managedclusters",
            ...
            "properties": {
                ...
                "networkSecurityRules" : [
                    {
                        "name": "AllowCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33000-33499",
                        "access": "Allow",
                        "priority": 2001,
                        "direction": "Inbound"
                    },
                    {
                        "name": "AllowARM",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "AzureResourceManager",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33500-33699",
                        "access": "Allow",
                        "priority": 2002,
                        "direction": "Inbound"
                    },
                    {
                        "name": "DenyCustomers",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "Internet",
                        "destinationAddressPrefix": "*",
                        "destinationPortRange": "33700-33799",
                        "access": "Deny",
                        "priority": 2003,
                        "direction": "Outbound"
                    },
                    {
                        "name": "DenyRDP",
                        "protocol": "*",
                        "sourcePortRange": "*",
                        "sourceAddressPrefix": "*",
                        "destinationAddressPrefix": "VirtualNetwork",
                        "destinationPortRange": "3389",
                        "access": "Deny",
                        "priority": 2004,
                        "direction": "Inbound",
                        "description": "Override for optional SFMC_AllowRdpPort rule. This is required in tests to avoid Sev2 incident for security policy violation."
                    }
                ],
                "fabricSettings": [
                ...
                ]
            }
```

## <a name="rdp-ports"></a>Ports RDP

Par défaut, les clusters Service Fabric gérés ne permettent pas l’accès aux ports RDP. Vous pouvez ouvrir des ports RDP sur Internet en définissant la propriété suivante sur une ressource de cluster Service Fabric géré.

```json
"allowRDPAccess": true
```

Lorsque la propriété allowRDPAccess est définie sur true, la règle NSG suivante est ajoutée au déploiement de cluster.

```json
{
    "name": "SFMC_AllowRdpPort",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open RDP ports.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3002,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRange": "3389"
    }
}
```

Les clusters Service Fabric gérés créent automatiquement des règles NAT de trafic entrant pour chaque instance dans un type de nœud. Pour rechercher les mappages de port permettant d’atteindre des instances spécifiques (nœuds de cluster), procédez comme suit :

À l’aide du portail Azure, recherchez les règles NAT de trafic entrant créées par le cluster géré pour le protocole RDP (Remote Desktop Protocol).

1. Accédez au groupe de ressources du cluster géré dans votre abonnement, nommé au format suivant : SFC_{cluster-id}.

2. Sélectionnez l’équilibreur de charge pour le cluster avec le format suivant : LB-{cluster-name}.

3. Sur la page de votre équilibreur de charge, sélectionnez Règles NAT de trafic entrant. Examinez les règles NAT de trafic entrant pour confirmer le mappage du port frontend du trafic entrant au port cible pour un nœud. 

   La capture d’écran suivante montre les règles NAT de trafic entrant pour trois différents types de nœuds :

   ![Règles NAT entrantes][Inbound-NAT-Rules]

   Par défaut, pour les clusters Windows, le port frontend se trouve dans la plage 50000 et plus et le port cible est le port 3389, qui correspond au service RDP sur le nœud cible.

4. Connectez-vous à distance au nœud (instance de groupe identique) spécifique. Vous pouvez utiliser le nom d’utilisateur et le mot de passe que vous avez définis lors de la création du cluster ou de toutes autres informations d’identification que vous avez configurées.

La capture d’écran suivante illustre l’utilisation de Remote Desktop Connection pour se connecter au nœud d’applications (Instance 0) dans un cluster Windows :

![Connexion Bureau à distance][sfmc-rdp-connect]

## <a name="clientconnection-and-httpgatewayconnection-ports"></a>Ports ClientConnection et HttpGatewayConnection

### <a name="nsg-rule-sfmc_allowservicefabricgatewaytosfrp"></a>Règle NSG : SFMC_AllowServiceFabricGatewayToSFRP

Une règle NSG par défaut est ajoutée pour permettre au fournisseur de ressources Service Fabric d’accéder aux ports clientConnectionPort et httpGatewayConnectionPort du cluster. Elle autorise l’accès aux ports par l’intermédiaire de l’étiquette de service « ServiceFabric ».

>[!NOTE]
>Cette règle est toujours ajoutée et ne peut pas être remplacée.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayToSFRP",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "This is required rule to allow SFRP to connect to the cluster. This rule cannot be overridden.",
        "protocol": "TCP",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "ServiceFabric",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 500,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

### <a name="nsg-rule-sfmc_allowservicefabricgatewayports"></a>Règle NSG : SFMC_AllowServiceFabricGatewayPorts

Cette règle facultative permet aux clients d’accéder à SFX, de se connecter au cluster à l’aide de PowerShell et d’utiliser les points de terminaison de l’API du cluster Service Fabric à partir d’Internet en ouvrant les ports d’équilibrage de charge pour clientConnectionPort et httpGatewayPort.

>[!NOTE]
>Cette règle n’est pas ajoutée s’il existe une règle personnalisée avec les mêmes valeurs d’accès, de direction et de protocole pour le même port. Vous pouvez remplacer cette règle par des règles NSG personnalisées.

```json
{
    "name": "SFMC_AllowServiceFabricGatewayPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open SF cluster gateway ports. To override add a custom NSG rule for gateway ports in priority range 1000-3000.",
        "protocol": "tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3001,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
            "19000",
            "19080"
        ]
    }
}
```

## <a name="load-balancer-ports"></a>Ports d’équilibreur de charge

Les clusters Service Fabric gérés créent une règle NSG dans la plage de priorités par défaut pour tous les ports d’équilibrage de charge configurés dans la section « loadBalancingRules » sous les propriétés *ManagedCluster*. Cette règle ouvre les ports d’équilibrage de charge pour le trafic entrant à partir d’Internet.  

>[!NOTE]
>Cette règle est ajoutée dans la plage de priorités facultative et peut être remplacée en ajoutant des règles NSG personnalisées.

```json
{
    "name": "SFMC_AllowLoadBalancedPorts",
    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
    "properties": {
        "description": "Optional rule to open LB ports",
        "protocol": "*",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Allow",
        "priority": 3003,
        "direction": "Inbound",
        "sourcePortRanges": [],
        "destinationPortRanges": [
        "80", "8080", "4343"
        ]
    }
}
```

## <a name="load-balancer-probes"></a>Sondes d’équilibreur de charge

Les clusters Service Fabric gérés créent automatiquement des sondes d’intégrité d’équilibreur de charge pour les ports de passerelle de la structure, ainsi que tous les ports configurés dans la section « loadBalancingRules » des propriétés du cluster géré.

```json
{
  "value": [
    {
        "name": "FabricTcpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19000,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "FabricHttpGateway",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 19080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
                {
                    "id": "<>"
                }
            ]
        },
        "type": "Microsoft.Network/loadBalancers/probes"
    },
    {
        "name": "probe1_tcp_8080",
        "properties": {
            "provisioningState": "Succeeded",
            "protocol": "Tcp",
            "port": 8080,
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "loadBalancingRules": [
            {
                "id": "<>"
            }
        ]
      },
      "type": "Microsoft.Network/loadBalancers/probes"
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

[Options de configuration du cluster Service Fabric géré](how-to-managed-cluster-configuration.md)

[Vue d’ensemble des clusters Service Fabric gérés](overview-managed-cluster.md)

<!--Image references-->
[Inbound-NAT-Rules]: ./media/how-to-managed-cluster-networking/inbound-nat-rules.png
[sfmc-rdp-connect]: ./media/how-to-managed-cluster-networking/sfmc-rdp-connect.png

