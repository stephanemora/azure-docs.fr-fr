---
title: 'Tutoriel : Effectuer une capture de paquets sur des connexions site à site Azure Virtual WAN'
description: Dans ce tutoriel, découvrez comment effectuer une capture de paquets sur la passerelle VPN site à site Virtual WAN.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: 765285a8b7c2434c64d1513e510f1cf06b513291
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598059"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Effectuer une capture de paquets sur la passerelle VPN site à site Azure Virtual WAN 

Les problèmes de connectivité et de performance sont souvent complexes. Le simple fait de cerner la cause du problème peut demander beaucoup de temps et d’énergie. La capture de paquets peut vous aider à restreindre l’étendue d’un problème à certaines parties du réseau. Elle peut vous aider à déterminer si le problème se situe sur le réseau côté client, côté Azure ou entre les deux. Une fois que vous avez réussi à cerner le problème avec précision, vous pouvez déboguer et prendre des mesures correctives de manière plus efficace.

L’article suivant décrit comment démarrer et arrêter une capture de paquets sur la passerelle VPN site à site Azure Virtual WAN. Actuellement, cette fonctionnalité est disponible uniquement dans PowerShell.

## <a name="prerequisites"></a>Prérequis

Pour utiliser la procédure décrite dans cet article, la configuration suivante doit déjà être définie dans votre environnement :

* Un WAN virtuel, un hub virtuel et une passerelle VPN site à site déployée dans le hub virtuel. Vous pouvez également avoir des connexions entre des sites VPN et votre passerelle VPN site à site.


### <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Configurer l’environnement

Veillez à utiliser le bon contexte d’abonnement en exécutant le script suivant dans PowerShell. Cela garantit que vous êtes connecté avec un utilisateur autorisé à effectuer la capture de paquets sur la passerelle VPN site à site.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Créer un compte de stockage

Vous devez créer un compte de stockage sous votre abonnement Azure pour stocker les résultats de votre capture de paquets. Pour obtenir des instructions sur la création d’un compte de stockage, consultez ce [document](.././storage/common/storage-account-create.md).

Une fois votre compte créé, exécutez les commandes suivantes pour générer une URL de signature d’accès partagé (SAS). Les résultats de votre capture de paquets sont stockés via cette URL.
   ```azurepowershell-interactive
  $rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Démarrer la capture de paquets

Pour démarrer la capture de paquets, vous avez deux options : capturer les paquets sur une seule connexion VPN ou sur l’ensemble de la passerelle VPN site à site. Notez que si vous choisissez d’effectuer les captures sur des connexions VPN, vous pouvez le faire sur seulement 6 connexions en même temps.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Capture de paquets sur une passerelle VPN site à site (toutes les connexions)

Exécutez ensuite les commande suivantes. Pour rechercher le nom de la passerelle VPN site à site, accédez à votre hub virtuel et cliquez sur VPN (site à site) sous Connectivité.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Image du nom de la passerelle Virtual WAN." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Capture de paquets sur des connexions VPN site à site spécifiques

>[!NOTE]
> Notez que vous pouvez exécuter une capture de paquets seulement sur 5 connexions VPN simultanément.


Exécutez ensuite les commande suivantes. Pour rechercher le nom des connexions VPN site à site, accédez à votre hub virtuel et cliquez sur VPN (site à site) sous Connectivité. Ensuite, accédez au site VPN sur lequel effectuer la capture de paquets et cliquez sur les trois points à droite. Cliquez sur **Modifier la connexion VPN** dans le menu qui s’affiche.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Image montrant comment rechercher les noms de connexion VPN." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Pour rechercher le nom des liens connectés à un site VPN spécifique, cliquez sur le site VPN dans la section précédente et consultez la table **Liens**. 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Image montrant comment rechercher le nom du lien VPN." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">”
   ```

## <a name="optional-specifying-filters"></a>Facultatif : spécification de filtres

Il existe plusieurs outils de capture de paquets communément disponibles. L’obtention de captures de paquets pertinentes avec ces outils peut être fastidieuse, surtout dans des scénarios de trafic à volume élevé. Pour simplifier vos captures de paquets, vous pouvez spécifier des filtres pour cibler des comportements spécifiques. Voici les paramètres disponibles :

>[!NOTE]
> Pour TracingFlags et TCPFlags, vous pouvez spécifier plusieurs protocoles en additionnant les valeurs numériques des protocoles à capturer (identique à l’opérateur logique OR). Par exemple, si vous voulez capturer uniquement des paquets ESP et OPVN, vous devez spécifier la valeur TracingFlag : 8+1 = 9.  

| Paramètre | Description | Valeurs par défaut | Valeurs disponibles|
|--- |--- | --- | ---|
| TracingFlags | Entier qui détermine les types de paquets à capturer | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Entier qui détermine les types de paquets TCP à capturer | 0 (aucun) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Taille maximale, en octets, d’un paquet capturé. Les paquets sont tronqués s’ils dépassent la valeur fournie. |120|Quelconque|
| MaxFileSize |Taille maximale du fichier de capture en Mo. Les captures sont stockées dans une mémoire tampon circulaire de sorte que le dépassement de capacité est géré d’une manière de type FIFO (les anciens paquets sont supprimés en premier)|100|Quelconque|
|SourceSubnets|Les paquets des plages CIDR spécifiées sont capturés. Spécifié sous forme de tableau.|[] (toutes les adresses IPv4)|Tableau de sous-réseaux IPV4 séparés par des virgules|
| DestinationSubnets |Les paquets destinés aux plages CIDR spécifiées sont capturés. Spécifié sous forme de tableau. |[] (toutes les adresses IPv4)|Tableau de sous-réseaux IPV4 séparés par des virgules|
|SourcePort |Les paquets dont la source est dans les plages spécifiées sont capturés. Spécifié sous forme de tableau.|[] (tous les ports)|Tableau de ports séparés par des virgules|
|DestinationPort |Les paquets dont la destination est dans les plages spécifiées sont capturés. Spécifié sous forme de tableau.|[] (tous les ports)|Tableau de ports séparés par des virgules|
| CaptureSingleDirectionTrafficOnly |Si la valeur est true, une seule direction d’un fluide bidirectionnel s’affiche dans la capture de paquets. Cela permet de capturer toutes les combinaisons possibles d’IP et de ports.|Vrai|True, False|
|Protocole|Tableau d’entiers qui correspondent aux protocoles IANA. |[] (tous les protocoles)| Tous les protocoles trouvés [ici](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Voici un exemple de capture de paquets utilisant une chaîne de filtrage. Vous pouvez changer les paramètres pour les adapter à vos besoins en fonction du tableau précédent.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Arrêt de la capture de paquets
Nous vous recommandons de laisser la capture de paquets s’exécuter pendant au moins 600 secondes. En raison de problèmes de synchronisation entre plusieurs composants sur le chemin, les captures de paquets d’une durée plus courte risquent de fournir des données incomplètes. Une fois que vous êtes prêt à arrêter la capture de paquets, exécutez la commande suivante.

Les paramètres sont similaires à ceux de la section Démarrage d’une capture de paquets. L’URL SAS a été générée dans la section [Créer un compte de stockage](#createstorage). Si le paramètre `SASurl` n’est pas configuré correctement, la trace peut échouer avec des erreurs de stockage.

### <a name="gateway-level-packet-capture"></a>Capture de paquets au niveau de la passerelle

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
   ```

### <a name="connection-level-packet-captures"></a>Captures de paquets au niveau de la connexion

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sasurl
   ```

## <a name="viewing-your-packet-capture"></a>Consultation de votre capture de paquets

Dans le portail Azure, accédez au compte de stockage créé dans « Créer un compte de stockage ». Cliquez sur votre conteneur et téléchargez le fichier. Les fichiers de données de capture de paquets sont générés au format PCAP. Vous pouvez utiliser Wireshark ou une autre application couramment disponible pour ouvrir les fichiers PCAP.

## <a name="next-steps"></a>Étapes suivantes

Ensuite, pour en savoir plus sur Virtual WAN, consultez :

> [!div class="nextstepaction"]
> * [FAQ sur Virtual WAN](virtual-wan-faq.md)
