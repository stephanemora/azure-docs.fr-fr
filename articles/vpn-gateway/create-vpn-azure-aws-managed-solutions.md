---
title: Créer un réseau privé virtuel (VPN) entre Azure et AWS à l’aide de solutions managées
description: Comment créer une connexion VPN entre Azure et AWS à l’aide de solutions managées, plutôt que des machines virtuelles ou des appliances.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/03/2021
ms.author: ricmart
ms.openlocfilehash: 82161ac92566f6589ebfb64dddc221e296ec0992
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539126"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Créer une connexion VPN entre Azure et AWS à l’aide de solutions managées

Vous pouvez établir une connexion entre Azure et AWS à l’aide de solutions managées. Auparavant, vous deviez utiliser une appliance ou une machine virtuelle servant de répondeur. Vous pouvez désormais connecter la passerelle privée virtuelle AWS à la passerelle VPN Azure directement, sans avoir à vous soucier de la gestion des ressources IaaS telles que les machines virtuelles. Cet article vous aide à créer une connexion VPN entre Azure et AWS en utilisant uniquement des solutions managées.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Diagramme de l’architecture":::

## <a name="configure-azure"></a>Configuration d’Azure

### <a name="configure-a-virtual-network"></a>Configurer un réseau virtuel

Configurez un réseau virtuel. Pour obtenir des instructions, consultez [Démarrage rapide d’un réseau virtuel](../virtual-network/quick-create-portal.md).

Les exemples de valeurs suivants sont utilisés dans cet article :

* **Groupe de ressources :** rg-azure-aws
* **Région :** USA Est
* **Nom du réseau virtuel :** vnet-azure
* **Espace d’adressage IPv4 :** 172.10.0.0/16
* **Nom du sous-réseau :** subnet-01
* **Plage d'adresses du sous-réseau :** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN

Créez une passerelle VPN pour votre réseau virtuel. Pour obtenir des instructions, consultez [Tutoriel : Créer et gérer une passerelle VPN](tutorial-create-gateway-portal.md).

Les exemples de valeurs et de paramètres suivants sont utilisés dans cet article :

* **Nom de la passerelle :** vpn-azure-aws
* **Région :** USA Est
* **Type de passerelle :** VPN
* **Type de VPN :** basé sur la route
* **SKU :** VpnGw1
* **Génération :** Génération 1
* **Réseau virtuel :** Le réseau virtuel pour lequel vous souhaitez créer la passerelle.
* **Plage d’adresses du sous-réseau de passerelle :** 172.10.0.0/27
* **Adresse IP publique :** Création
* **Nom de l’adresse IP publique :** pip-vpn-azure-aws
* **Activer le mode actif/actif :** Disable
* **Configurer BGP :** Disable

Exemple :

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Résumé de la passerelle de réseau virtuel":::

## <a name="configure-aws"></a>Configurer AWS

1. Créez le cloud privé virtuel (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Créer des informations VPC":::

1. Créez un sous-réseau à l’intérieur du VPC (réseau virtuel).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Créer le sous-réseau":::

1. Créez une passerelle cliente qui pointe vers l’adresse IP publique de la passerelle VPN Azure. La **passerelle cliente** est une ressource AWS qui contient des informations pour AWS sur l’appareil de passerelle client, qui est dans ce cas-ci la passerelle VPN Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Créer une passerelle cliente":::

1. Créez la passerelle de réseau virtuel.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Créer une passerelle privée virtuelle":::

1. Joignez la passerelle privée virtuelle au VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Joindre la passerelle VPG au VPC":::

1. Sélectionnez le VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Attacher":::

1. Créez une connexion VPN de site à site.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Créer une connexion VPN":::

1. Définissez l’option d’itinéraire sur **Statique** et pointez sur le préfixe subnet-01 Azure **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Définition d’un itinéraire statique":::

1. Une fois les options remplies, **créez** la connexion.

1. Téléchargez le fichier de configuration. Pour télécharger la configuration appropriée, définissez le fournisseur, la plateforme et le logiciel sur **Générique**, car Azure n’est pas une option valide.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Télécharger la configuration":::

1. Le fichier de configuration contient la clé prépartagée et l’adresse IP publique pour chacun des deux tunnels IPsec créés par AWS.

   **Tunnel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Tunnel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Configuration du tunnel 1":::

   **Tunnel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Tunnel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Configuration du tunnel 2":::

1. Une fois les tunnels créés, vous verrez un résultat similaire à cet exemple.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Détails de la connexion VPN AWS":::

## <a name="create-local-network-gateway"></a>Créer une passerelle de réseau local

Dans Azure, la passerelle réseau de local est une ressource Azure qui représente généralement un emplacement local. Elle est renseignée avec les informations de connexion à l’appareil VPN local. Toutefois, dans cette configuration, la passerelle de réseau local est créée et renseignée avec les informations de connexion de la passerelle privée virtuelle AWS. Pour plus d’informations sur les passerelles de réseau local Azure, consultez [Paramètres de la passerelle VPN Azure](vpn-gateway-about-vpn-gateway-settings.md#lng).

Créez une passerelle de réseau local dans Azure. Pour connaître les étapes à suivre, consultez [Créer une passerelle de réseau local](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Spécifiez les valeurs suivantes :

* **Nom :** Dans cet exemple, nous utilisons Ing-azure-aws.
* **Point de terminaison :** Adresse IP
* **Adresse IP :** Adresse IP publique de la passerelle privée virtuelle AWS et préfixe CIDR VPC. Vous trouverez l’adresse IP publique dans le fichier de configuration que vous avez téléchargé précédemment.

AWS crée deux tunnels IPsec à des fins de haute disponibilité. L’exemple suivant montre l’adresse IP publique du tunnel IPsec n°1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Passerelle de réseau local":::

## <a name="create-vpn-connection"></a>Créer la connexion VPN

Dans cette section, vous créez la connexion VPN entre la passerelle de réseau virtuel Azure et la passerelle AWS.

1. Créez la connexion Azure. Pour obtenir des instructions sur la création d’une connexion, consultez [Créer une connexion VPN](tutorial-site-to-site-portal.md#CreateConnection).

   Dans l’exemple suivant, la clé partagée a été obtenue à partir du fichier de configuration que vous avez téléchargé précédemment. Dans cet exemple, nous utilisons les valeurs du tunnel IPsec n°1 créé par AWS qui sont décrites dans le fichier de configuration.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Objet de connexion Azure":::

1. Affichez la connexion. Après quelques minutes, la connexion est établie.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Connexion en cours":::

1. Vérifiez que le tunnel IPsec n°1 AWS est **opérationnel**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Vérifier que le tunnel AWS est opérationnel":::

1. Modifiez la table de routage associée au VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Modifier l’itinéraire":::

1. Ajoutez l’itinéraire au sous-réseau Azure. Cet itinéraire transite par la passerelle VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Enregistrer la configuration de l’itinéraire":::

## <a name="configure-second-connection"></a>Configurer la deuxième connexion

Dans cette section, vous créez une deuxième connexion pour garantir une haute disponibilité.

1. Créez une autre passerelle de réseau local qui pointe vers l’adresse IP publique du tunnel IPsec n°2 sur AWS. Il s’agit de la passerelle de secours.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Créer la passerelle de réseau local":::

1. Créez la deuxième connexion VPN entre Azure et AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Créer la connexion de passerelle de réseau local de secours":::

1. Affichez les connexions de la passerelle VPN Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="État de la connexion Azure":::

1. Affichez les connexions AWS. Dans cet exemple, vous voyez que les connexions sont désormais établies.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="État de la connexion AWS":::

## <a name="to-test-connections"></a>Pour tester les connexions

1. Ajoutez une **passerelle Internet** au VPC d’AWS. La passerelle Internet est une connexion logique entre un réseau VPN Amazon et Internet. Cette ressource vous permet de vous connecter via la machine virtuelle de test à partir de l’adresse IP publique d’AWS via Internet. Cette ressource n’est pas requise pour la connexion VPN. Nous l’utilisons uniquement pour effectuer des tests.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Créer la passerelle Internet":::

1. Sélectionnez **Joindre au VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Joindre la passerelle Internet au VPC":::

1. Sélectionnez un VPC et **Joindre la passerelle Internet**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Joindre la passerelle":::

1. Créez un itinéraire pour autoriser les connexions à **0.0.0.0/0** (Internet) via la passerelle Internet.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Configurer l’itinéraire via la passerelle":::

1. Dans Azure, l’itinéraire est créé automatiquement. Vous pouvez vérifier l’itinéraire à partir de la machine virtuelle Azure en sélectionnant **VM > Mise en réseau > Interface réseau > Itinéraires effectifs**. Vous voyez 2 itinéraires, 1 itinéraire par connexion.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Vérifier les itinéraires effectifs":::

1. Vous pouvez effectuer le test à partir d’une machine virtuelle Linux sur Azure. Le résultat est similaire à l’exemple suivant.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Vue d’ensemble d’Azure à partir de la machine virtuelle Linux":::

1. Vous pouvez également effectuer le test à partir d’une machine virtuelle Linux sur AWS. Le résultat est similaire à l’exemple suivant.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Vue d’ensemble d’AWS à partir de la machine virtuelle Linux":::

1. Testez la connectivité à partir de la machine virtuelle Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Test Ping à partir d’Azure":::

1. Testez la connectivité à partir de la machine virtuelle AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Test Ping à partir d’AWS":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la prise en charge d’AWS pour IKEv2, consultez [l’article AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).

* Pour plus d’informations sur la création d’un VPN multicloud à grande échelle, consultez la vidéo [Créer un VPN multicloud optimal à grande échelle](https://www.youtube.com/watch?v=p7h-frLDFE0).
