---
title: 'Exemple de réseau de périmètre : protéger les réseaux avec un réseau de périmètre consistant en un pare-feu, UDR et groupes de sécurité réseau | Microsoft Docs'
description: Créer un réseau de périmètre (également appelé DMZ) avec un pare-feu, de routage défini par l’utilisateur (UDR) et de groupes de sécurité réseau (NSG).
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 668862714b416bd89d3b5f82caf8b0305fccae54
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426526"
---
# <a name="example-3-build-a-perimeter-network-to-protect-networks-with-a-firewall-udr-and-nsgs"></a>Exemple 3 : Créer un réseau de périmètre pour protéger les réseaux avec un pare-feu, UDR et groupes de sécurité réseau

[Revenir à la page Meilleures pratiques relatives aux frontières de sécurité][HOME]

Dans cet exemple, vous créez un réseau de périmètre (également connu comme une zone DMZ, zone démilitarisée et sous-réseau filtré). L’exemple implémente un pare-feu, quatre serveurs Windows, un routage défini par l’utilisateur (UDR), le transfert IP et les groupes de sécurité réseau (NSG). Cet article vous guide à travers chacune des commandes appropriées pour fournir une compréhension plus approfondie de chaque étape. La section de scénario de trafic explique également en détail l’évolution du trafic à travers les couches de défense du réseau de périmètre. Enfin, la section Références contient tout le code et les instructions pour créer cet environnement afin de pouvoir tester et expérimenter différents scénarios.

![Réseau de périmètre bidirectionnel avec appliance virtuelle réseau, groupe de sécurité réseau et routage défini par l’utilisateur][1]

## <a name="environment-setup"></a>Configuration de l’environnement

Cet exemple utilise un abonnement qui contient les composants suivants :

* Trois services cloud : SecSvc001, FrontEnd001 et BackEnd001
* Un réseau virtuel (CorpNetwork) avec trois sous-réseaux : SecNet, FrontEnd et BackEnd
* Une appliance virtuelle réseau : un pare-feu est connecté au sous-réseau SecNet
* Un serveur Windows qui représente un serveur d’applications web : IIS01
* Deux serveurs Windows qui représentent des serveurs principaux d’applications : AppVM01, AppVM02
* Un serveur Windows qui représente un serveur DNS : DNS01

Le [section Références](#references) contient un script PowerShell qui génère une grande partie l’environnement décrit ici. Cet article ne donne pas sinon des instructions détaillées pour la création de machines virtuelles (VM) et réseaux virtuels.

Pour créer l’environnement :

1. Enregistrez le fichier XML de configuration réseau inclus dans le [section de référence](#references). Vous devez mettre à jour des noms, emplacement et les adresses IP pour faire correspondre le scénario donné.
1. Mettre à jour les variables utilisateur dans le script complet pour correspondre à votre environnement spécifique (par exemple, les abonnements, les noms de service et ainsi de suite).
1. Exécutez le script dans PowerShell.

> [!NOTE]
> La région spécifiée dans le script PowerShell doit correspondre à la région spécifiée dans le fichier XML de configuration réseau.

Une fois que le script s’exécute correctement, procédez comme suit :

1. Configurer les règles de pare-feu. Consultez le [règles de pare-feu](#firewall-rules) section.
1. Si vous le souhaitez, utilisez les deux scripts dans la section Références pour configurer une application web sur le serveur web et le serveur d’applications permettant le test de cette configuration de réseau de périmètre.

## <a name="user-defined-routing"></a>Routage défini par l’utilisateur

Par défaut, les itinéraires système suivants sont définis en tant que :

    Effective routes :
     Address Prefix    Next hop type    Next hop IP address Status   Source
     --------------    -------------    ------------------- ------   ------
     {10.0.0.0/16}     VNETLocal                            Active   Default
     {0.0.0.0/0}       internet                             Active   Default
     {10.0.0.0/8}      Null                                 Active   Default
     {100.64.0.0/10}   Null                                 Active   Default
     {172.16.0.0/12}   Null                                 Active   Default
     {192.168.0.0/16}  Null                                 Active   Default

VNETLocal est toujours l’ou les préfixes d’adresse définis pour ce réseau virtuel spécifique. Par exemple, elle deviendra à partir du réseau virtuel à réseau virtuel en fonction de la définition de chaque réseau virtuel spécifique. Les itinéraires système restants sont statiques et par défaut comme indiqué.

Priorité, les itinéraires sont traités via la méthode le plus long préfixe (LPM). Par conséquent, l’itinéraire plus spécifique dans le tableau s’applique à une adresse de destination donné.

Par conséquent, le trafic destiné à un serveur tel que DNS01 (10.0.2.4) sur l’ordinateur local (10.0.0.0/16) de réseau est routé via le réseau virtuel en raison de l’itinéraire 10.0.0.0/16.  Pour 10.0.2.4, l’itinéraire 10.0.0.0/16 est l’itinéraire plus spécifique. Cette règle s’applique même si 10.0.0.0/8 et 0.0.0.0/0 peuvent également s’appliquer. Ils sont moins spécifiques, toutefois, afin qu’ils n’affectent pas ce trafic. Le trafic vers 10.0.2.4 a le réseau virtuel local en tant que son tronçon suivant, il est routé vers la destination.

Par exemple, l’itinéraire 10.0.0.0/16 ne s’applique pas au trafic qui est conçu pour 10.1.1.1. L’itinéraire du système 10.0.0.0/8 est le plus spécifique afin que le trafic est abandonné ou « à un de trou noir », car le tronçon suivant est Null.

Si la destination ne s’applique à aucun des préfixes Null ou les préfixes VNETLocal, le trafic suit l’itinéraire moins spécifique (0.0.0.0/0). Il est acheminé en internet en tant que tronçon suivant et en dehors du périmètre d’internet d’Azure.

S’il existe deux préfixes identiques dans la table de routage, l’ordre de préférence est basée sur l’attribut de source de l’itinéraire :

1. VirtualAppliance: Un itinéraire défini par l’utilisateur ajouté manuellement à la table.
1. Passerelle VPN : Un itinéraire dynamique (BGP lorsqu’il est utilisé avec des réseaux hybrides) ajouté par un protocole réseau dynamique. Ces itinéraires peuvent changer au fil du temps, comme le protocole dynamique reflétant automatiquement les modifications dans un réseau homologué.
1. Valeur par défaut : Les itinéraires système, le réseau virtuel local et les entrées statiques comme indiqué dans la table d’itinéraires.

> [!NOTE]
> Vous pouvez maintenant utiliser le routage défini par l’utilisateur (UDR) avec ExpressRoute et les passerelles VPN pour forcer le trafic entrant et sortant entre différents locaux pour être acheminé vers une appliance virtuelle de réseau (NVA).

### <a name="create-local-routes"></a>Créer des itinéraires locaux

Cet exemple utilise deux tables de routage, un pour les sous-réseaux frontaux et principaux. Chaque table est chargée d’itinéraires statiques appropriés au sous-réseau donné. Dans cet exemple, chaque table possède trois routes :

1. Trafic du sous-réseau local sans tronçon suivant défini. Cet itinéraire autorise le trafic de sous-réseau local à contourner le pare-feu.
2. Trafic de réseau virtuel avec un tronçon suivant défini comme pare-feu. Cet itinéraire remplace la règle par défaut qui permet le trafic de réseau virtuel acheminer directement.
3. Tous les du trafic restant (0/0) avec un tronçon suivant défini comme pare-feu.

Une fois que les tables de routage sont créées, elles sont liées à leurs sous-réseaux. La table de routage du sous-réseau frontal doit ressembler à :

    Effective routes :
     Address Prefix    Next hop type       Next hop IP address  Status   Source
     --------------    ------------------  -------------------  ------   ------
     {10.0.1.0/24}     VNETLocal                                Active
     {10.0.0.0/16}     VirtualAppliance    10.0.0.4             Active
     {0.0.0.0/0}       VirtualAppliance    10.0.0.4             Active

Cet exemple utilise les commandes suivantes pour créer la table d’itinéraires, ajouter un itinéraire défini par l’utilisateur et puis lier la table de routage à un sous-réseau. Les éléments qui commencent par `$`, tel que `$BESubnet`, sont des variables définies par l’utilisateur à partir du script dans la section de référence.

1. Commencez par créer la table de routage de base. L’extrait de code suivant crée la table pour le sous-réseau principal. Le script complet crée également une table correspondante pour le sous-réseau frontal.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

1. Une fois que vous avez créé la table de routage, vous pouvez ajouter des itinéraires définis par l’utilisateur spécifiques. L’extrait de code suivant spécifie que tout le trafic (0.0.0.0/0) est acheminé via l’appliance virtuelle. Une variable `$VMIP[0]` est utilisé pour transmettre l’adresse IP affectée lors de l’appliance virtuelle a été créée précédemment dans le script. Le script complet crée également une règle correspondante dans la table frontale.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. L’entrée d’itinéraire précédente remplace l’itinéraire de « 0.0.0.0/0 » par défaut, mais la règle de 10.0.0.0/16 par défaut permet toujours le trafic au sein du réseau virtuel pour acheminer directement vers la destination et non vers l’appliance virtuelle réseau. Pour corriger ce comportement, vous devez ajouter la règle suivante :

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

1. À ce stade, vous devez faire un choix. Les deux règles précédentes acheminer tout le trafic vers le pare-feu pour l’évaluation, y compris le trafic au sein d’un seul sous-réseau. Vous souhaiterez peut-être ce comportement. Toutefois, si vous ne le faites, vous pouvez autoriser le trafic au sein d’un sous-réseau pour acheminer localement sans intervention du pare-feu. Ajoutez une troisième, la règle spécifique qui achemine directement n’importe quelle adresse destiné au sous-réseau local (NextHopType = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

1. Enfin, une fois que la table de routage est créée et remplie avec les itinéraires définis par l’utilisateur, vous devez lier la table à un sous-réseau. L’extrait de code suivant lie la table pour le sous-réseau principal. Le script complet est également lié à la table de routage frontal vers le sous-réseau frontal.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Transfert IP

Le transfert IP est une fonctionnalité associée au UDR. Ce paramètre sur une appliance virtuelle autorise à recevoir le trafic non adressé à l’appliance et puis de transférer ce trafic vers sa destination finale.

Par exemple, si le trafic à partir d’AppVM01 fait une demande au serveur DNS01, UDR achemine le trafic vers le pare-feu. Lorsque le transfert d’IP activé, le trafic avec la destination de DNS01 (10.0.2.4) est accepté par l’appliance de pare-feu (10.0.0.4), puis transféré vers sa destination finale (10.0.2.4). Sans le transfert IP est activé sur le pare-feu, le trafic n’est pas accepté par l’appliance même si la table de routage est le pare-feu le tronçon suivant.

> [!IMPORTANT]
> Pensez à activer le transfert IP en conjonction avec un routage défini par l’utilisateur.

Transfert IP peut être activé avec une seule commande au moment de la création de machine virtuelle. Vous appelez l’instance de machine virtuelle est de votre appliance virtuelle de pare-feu et activer le transfert IP. N’oubliez pas que les éléments en rouge qui commencent par `$`, comme `$VMName[0]`, sont des variables définies par l’utilisateur à partir du script dans la section Références de ce document. Le zéro entre crochets, `[0]`, représente la première machine virtuelle dans le tableau des machines virtuelles. Pour l’exemple de script de fonctionner sans modification, la première machine virtuelle (VM 0) doit être le pare-feu. Dans le script complet, l’extrait de code pertinentes est regroupée avec les commandes UDR vers la fin.

```powershell
Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
    Set-AzureIPForwarding -Enable
```

## <a name="network-security-groups"></a>Groupes de sécurité réseau

Dans cet exemple, vous créez un groupe de sécurité réseau (NSG) et puis le charger avec une seule règle. L’exemple lie ensuite le groupe de sécurité réseau uniquement aux sous-réseaux frontaux et principaux (et pas au SecNet). La règle que vous chargez dans le groupe de sécurité réseau est comme suit :

* Tout trafic (tous les ports) à partir d’internet vers le réseau virtuel entier (tous les sous-réseaux) est refusé

Bien que les groupes de sécurité réseau sont utilisés dans cet exemple, leur objectif principal est comme une couche secondaire de défense contre les erreurs de configuration manuelle. Vous souhaitez bloquer tout le trafic entrant à partir d’internet vers les sous-réseaux frontal ou principal. Le trafic doit circuler uniquement via le sous-réseau SecNet au pare-feu, après laquelle le trafic uniquement approprié doit obtenir routé une session sur les sous-réseaux frontaux ou principaux. En outre, les règles UDR acheminent tout le trafic qui atteint les sous-réseaux frontaux ou principaux vers le pare-feu. Le pare-feu le considère comme un flux asymétrique et supprime le trafic sortant.

Trois couches de sécurité protéger les sous-réseaux frontaux et principaux :

1. Aucun point de terminaison ouvert sur les services cloud FrontEnd001 et BackEnd001
1. Groupes de sécurité réseau refusent le trafic provenant d’internet
1. Le pare-feu interrompt le trafic asymétrique

Un point intéressant concernant le groupe de sécurité réseau dans cet exemple est qu’il ne contient qu’une seule règle, illustrée ci-dessous. Cette règle refuse le trafic internet vers le réseau virtuel entier, y compris le sous-réseau de sécurité.

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Étant donné que le NSG est associé uniquement aux sous-réseaux frontaux et principaux, la règle n’est pas appliquée pour le trafic entrant du sous-réseau de sécurité. Par conséquent, le trafic circule vers le sous-réseau de sécurité.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Règles de pare-feu

Vous devez créer des règles de transfert sur le pare-feu. Étant donné que le pare-feu bloque ou transfère le trafic entrant, sortant et intra-virtual-network, vous avez besoin de nombreuses règles de pare-feu. En outre, le pare-feu doit traiter tout le trafic entrant à l’adresse IP publique de Service de sécurité (sur différents ports). Pour éviter les modifications ultérieurement, suivez la meilleure pratique en représentant graphiquement les flux logiques avant de configurer les sous-réseaux et les règles de pare-feu. La figure qui suit est une vue logique des règles de pare-feu de cet exemple :

![Affichage logique des règles de pare-feu][2]

> [!NOTE]
> Ports de gestion varient selon l’appliance virtuelle réseau. Cet exemple montre un pare-feu Barracuda nextgen Firewall, qui utilise les ports 22, 801 et 807. Veuillez consulter la documentation du fournisseur de matériel pour rechercher les ports exacts pour gérer l’appareil.

### <a name="logical-rule-description"></a>Description de la logique de règle

Le diagramme logique ci-dessus n’affiche pas le sous-réseau de sécurité, car le pare-feu est la seule ressource de ce sous-réseau. Ce diagramme montre les règles de pare-feu, comment elles autorisent ou refusent les flux de trafic, mais pas le véritable acheminé de chemin d’accès. En outre, les ports externes sélectionnés pour le trafic de protocole Bureau à distance (RDP) sont les ports supérieurs par plage (8014 – 8026) choisis pour faciliter la lecture pour s’aligner avec les deux derniers octets des adresses IP locales. Par exemple, l’adresse du serveur local 10.0.1.4 est associée à un port externe 8014. Vous pouvez, toutefois, utiliser tous les ports supérieurs non conflictuels.

Vous devez les types de règles suivants pour cet exemple :

* Règles externes pour le trafic entrant :
  1. Règle de pare-feu de gestion : autorise le trafic à traverser les ports de gestion de l’appliance virtuelle réseau.
  2. Règles de RDP pour chaque serveur windows : permet la gestion des serveurs individuels via RDP.  Selon les capacités de votre appliance virtuelle réseau, vous pourrez regrouper les règles en une seule.
  3. Règles de trafic d’application : un pour le trafic web frontal et un pour le trafic de serveur principal (par exemple, serveur web vers couche de données). La configuration de ces règles dépend d’architecture réseau et le trafic de flux.

     * La première règle autorise le trafic d’application réel atteindre le serveur d’applications. Contrairement aux règles de sécurité, gestion et ainsi de suite, les règles d’application permettent aux utilisateurs externes ou des services accéder aux applications. Cet exemple a un seul serveur web sur le port 80, ce qui permet une règle d’application unique rediriger le trafic destiné à une adresse IP externe à la place acheminer vers les adresse IP interne du serveur web. Session de trafic redirigée est remappée par NAT vers le serveur interne.
     * La deuxième règle de trafic d’application est la règle de serveur principal pour permettre au serveur web à utiliser n’importe quel port pour acheminer le trafic vers le serveur AppVM01, mais pas sur le serveur de AppVM02.

* Règles internes pour le trafic intra-virtual-network :
  1. Règle sortant vers internet : autorise le trafic à partir de n’importe quel réseau vers les réseaux sélectionnés. Cette règle est généralement une valeur par défaut sur le pare-feu, mais dans un état désactivé. Activez cette règle pour cet exemple.
  2. Règle DNS : autorise uniquement le trafic DNS (port 53) vers le serveur DNS. Pour cet environnement, la majeure partie du Front end vers le principal est bloquée. Cette règle autorise spécifiquement le DNS à partir de n'importe quel sous-réseau local.
  3. Règle sous-réseau à sous-réseau : permet à n’importe quel serveur sur le sous-réseau du serveur principal pour vous connecter à n’importe quel serveur sur le sous-réseau frontal, mais pas l’inverse.

* Règle de prévention de défaillance pour le trafic répondant à aucun des critères ci-dessus :
  1. Règle Refuser tout le trafic : toujours la dernière règle en termes de priorité. Si le flux de trafic ne correspond à aucune des règles ci-dessus, cette règle est bloquée. Il est une règle par défaut. Car il est généralement activé, aucune modification n’est nécessaire.

> [!TIP]
> Dans la deuxième règle de trafic de l’application, n’importe quel port est autorisé pour simplifier cet exemple. Dans un scénario réel, vous devez utiliser des ports et plages d’adresses afin de réduire la surface d’attaque de cette règle.


> [!IMPORTANT]
> Une fois que vous avez créé les règles, il est important que vous passez en revue la priorité de chaque règle pour s’assurer que le trafic est autorisé ou refusé comme vous le souhaitez. Pour cet exemple, les règles sont classées par ordre de priorité. Il est facile d’obtenir bloqué hors du pare-feu si les règles sont mal ordonnés. Veillez à définir la règle de pare-feu de gestion comme la plus haute priorité absolue.

### <a name="rule-prerequisites"></a>Conditions préalables de règle

Points de terminaison publics sont nécessaires pour la machine virtuelle exécutant le pare-feu. Ces points de terminaison publics doivent être ouverts pour que le pare-feu peut traiter le trafic. Il existe trois types de trafic dans cet exemple :

1. Trafic de gestion pour contrôler le pare-feu et les règles de pare-feu
1. Trafic RDP pour contrôler les serveurs windows
1. Trafic des applications

Les types de trafic s’affichent dans la partie supérieure du pare-feu règles logiques diagramme ci-dessus.

> [!IMPORTANT]
> N’oubliez pas que *tous les* le trafic est fourni via le pare-feu. Bureau à distance au serveur IIS01, vous devez vous connecter au pare-feu sur le port 8014 et ensuite autoriser le pare-feu à acheminer la requête RDP en interne pour le port RDP de IIS01. Le portail Azure **Connect** bouton ne fonctionne pas car il n’est pas directe RDP vers IIS01 aussi du portail. Toutes les connexions à partir d’internet sont pour le service de sécurité et un port (par exemple, secscv001.cloudapp.NET : xxxx). Le diagramme ci-dessus pour le mappage de port externe et interne IP et port de référence.

Vous pouvez ouvrir un point de terminaison au moment de la création de machine virtuelle ou une fois la build. L’exemple de script et de l’extrait de code suivant ouvrent un point de terminaison après la création de la machine virtuelle.

N’oubliez pas que les éléments qui commencent par `$`, tel que `$VMName[$i]`, sont des variables définies par l’utilisateur à partir du script dans la section de référence. La `[$i]` représente le nombre de tableau d’une machine virtuelle spécifique dans un tableau de machines virtuelles.

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Bien qu’il n’est pas clairement indiqué ici en raison de variables, vous devez ouvrir uniquement les points de terminaison sur le service de cloud de sécurité. Cette précaution vous permet de vous assurer que le pare-feu gère tout le trafic entrant, s’il a acheminé, remappé par NAT ou supprimé.

Installez un client de gestion sur un PC pour gérer le pare-feu et de créer les configurations nécessaires. Pour plus d’informations sur la gestion de votre pare-feu ou autres appliances virtuelles réseau, consultez la documentation du fournisseur. Le reste de cette section, ainsi que les **la création de règles de pare-feu** section décrivent la configuration du pare-feu. Utilisez le client de gestion du fournisseur, pas le portail Azure ou PowerShell.

Accédez à [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) pour télécharger le client de gestion et de savoir comment se connecter au pare-feu Barracuda.

Une fois que vous êtes connecté au pare-feu, définir des objets de réseau et le service avant de créer les règles de pare-feu. Ces deux classes d’objet requis peuvent rendre plus facile la création de règles.

Pour cet exemple, définir trois objets réseaux nommés pour chacun :

* Sous-réseau frontal
* Sous-réseau principal
* Adresse IP du serveur DNS

Pour créer un réseau nommé à partir du tableau de bord client Admin Barracuda NG :

1. Accédez à la **onglet configuration**.
1. Sélectionnez **Ruleset** dans le **Configuration opérationnelle** section
1. Sélectionnez **réseaux** sous le **objets de pare-feu** menu.
1. Sélectionnez **New** dans le **modifier des réseaux** menu.
1. Créez l’objet de réseau en ajoutant le nom et le préfixe :

   ![Créer un objet réseau de front-ed][3]

Les étapes précédentes créent un réseau nommé pour le sous-réseau frontal. Créez un objet similaire pour le sous-réseau du serveur principal. Désormais les sous-réseaux peuvent être référencés plus facilement par nom dans les règles de pare-feu.

Pour l’objet de serveur DNS :

![Créez un objet de serveur DNS][4]

Cette référence d’adresse IP unique est utilisée dans une règle DNS plus loin dans le document.

L’autre classe d’objet inclut les objets de services, qui représentent les ports de connexion RDP pour chaque serveur. L’objet de service RDP existant est lié au port RDP par défaut, 3389. Par conséquent, vous pouvez créer de nouveaux services pour autoriser le trafic à partir des ports externes (8014-8026). Vous pouvez également ajouter les nouveaux ports pour le service RDP existant. Toutefois, pour faciliter la démonstration, vous pouvez rendre une règle individuelle pour chaque serveur. Pour créer une nouvelle règle RDP pour un serveur à partir du tableau de bord client Admin Barracuda NG :

1. Accédez à la **onglet configuration**.
1. Sélectionnez **Ruleset** dans le **Configuration opérationnelle** section.
1. Sélectionnez **Services** sous le **objets de pare-feu** menu.
1. Défiler la liste des services et sélectionnez **RDP**.
1. Avec le bouton droit et sélectionnez Copier, puis avec le bouton droit et sélectionnez Coller.
1. Il existe désormais un objet de service RDP-Copie1 qui peut être modifié. Avec le bouton droit **RDP-Copie1** et sélectionnez **modifier**.
1. Le **modifier un objet de Service** fenêtre s’affiche comme indiqué ici :

   ![Copie de la règle RDP par défaut][5]

1. Modifiez les valeurs pour représenter le service RDP pour un serveur spécifique. Pour AppVM01, la règle RDP par défaut doit être modifiée pour refléter un nouveau service **nom**, **Description**et le Port RDP externe utilisé dans le diagramme de la Figure 8. N’oubliez pas que les ports sont modifiées à partir de la valeur par défaut RDP 3389 au port externe pour ce serveur spécifique. Par exemple, le port externe pour AppVM01 est 8025. La règle de service modifié est illustrée ici :

   ![Règle AppVM01][6]

Répétez ce processus pour créer des services RDP pour les serveurs restants : AppVM02, DNS01 et IIS01. Ces services simplifier les règles dans la section suivante créer et plus évidents.

> [!NOTE]
> Un service RDP pour le pare-feu n’est pas nécessaire, car le pare-feu de machine virtuelle étant une image basée sur Linux SSH est utilisé sur le port 22 pour la gestion de machine virtuelle au lieu de RDP. En outre, le port 22 et deux autres ports sont autorisés pour la connectivité de gestion. Consultez le **règle de pare-feu de gestion** dans la section suivante.

### <a name="firewall-rules-creation"></a>Création de règles de pare-feu

Il existe trois types de règles de pare-feu utilisées dans cet exemple, toutes avec des icônes distinctes :

La règle de redirection d’application : ![Icône de redirection d’application][7]

La règle NAT de destination : ![Icône NAT de destination][8]

La règle pass : ![Icône de réussite][9]

Vous trouverez plus d’informations sur ces règles sur le site Web de Barracuda.

Pour créer les règles suivantes ou vérifiez les règles par défaut existantes :

1. Dans le tableau de bord client Admin Barracuda NG, accédez à la **configuration** onglet.
1. Dans le **Configuration opérationnelle** section, sélectionnez **Ruleset**.
1. Le **règles principales** grille montre existante active et désactivation des règles sur ce pare-feu. Sélectionnez le vert **+** dans le coin supérieur droit pour créer une nouvelle règle. Si votre pare-feu est verrouillé pour les modifications, vous voyez un bouton marqué **verrou** et ne peut pas créer ou modifier les règles. Sélectionnez le **verrou** bouton pour déverrouiller l’ensemble de règles et autoriser la modification. Cliquez sur une règle que vous souhaitez modifier, puis sélectionnez **modifier la règle**.

Une fois que vous créez ou modifiez des règles, les transmettre au pare-feu et les activer. Sinon, les modifications de règle ne prennent effet. Le processus de push et d’activation est décrit dans [d’activation de règle](#rule-activation).

Voici les spécificités de chaque règle nécessaire pour terminer cet exemple :

* **Règle de pare-feu de gestion**: Cette règle de redirection d’application autorise le trafic à traverser les ports de gestion de l’appliance virtuelle réseau, dans cet exemple, un pare-feu Barracuda nextgen Firewall. Les ports de gestion sont 801, 807 et éventuellement 22. Les ports internes et externes sont les mêmes, aucune traduction de port. Cette règle est appelée le programme d’installation-MGMT-ACCESS. Il existe une règle par défaut et activé par défaut dans le pare-feu Barracuda nextgen Firewall, version 6.1.
  
    ![Règle de gestion de pare-feu][10]

  > [!TIP]
  > L’espace d’adresse source dans cette règle est **n’importe quel**. Si les plages d’adresses IP gestion sont connues, réduction de la portée réduit également la surface d’attaque pour les ports de gestion.

* **Règles RDP**:  Ces règles NAT de destination permettent la gestion des serveurs individuels via RDP. Les champs critiques pour ces règles sont :
  * Source. Pour autoriser RDP à partir de n’importe quel emplacement, utilisez la référence **tout** dans le champ Source.
  * Service. Utilisez l’objet de service RDP que vous avez créé précédemment : **AppVM01 RDP**. Les ports externes redirigent vers l’adresse du serveur local IP et au port RDP par défaut 3386. Cette règle spécifique sert à l’accès RDP à AppVM01.
  * Destination. Utilisez le port local sur le pare-feu : **DCHP IP Local 1** ou **eth0** si vous utilisez des adresses IP statiques. Le nombre ordinal (eth0, eth1, etc.) peut être différent si votre équipement réseau dispose de plusieurs interfaces locales. Le pare-feu utilise ce port d’envoi, et il peut être le même que le port de réception. La destination réellement acheminée est dans le **liste cible** champ.
  * Redirection. Configurer pour indiquer à l’appliance virtuelle vers lequel rediriger finalement ce trafic. La redirection la plus simple consiste à placer l’adresse IP dans le champ de liste cible. Vous pouvez également spécifier le port et NAT réachemine le port et l’adresse IP. Si vous ne spécifiez pas un port, l’appliance virtuelle utilise le port de destination de la demande entrante.

    ![Règle RDP de pare-feu][11]

    Créez quatre règles RDP :

    | Nom de la règle | Serveur | de diffusion en continu | Liste des cibles |
    | --- | --- | --- | --- |
    | RDP-vers-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
    | RDP-vers-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
    | RDP 0 AppVM01 |AppVM01 |RDP AppVM01 |10.0.2.5:3389 |
    | RDP-vers-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

  > [!TIP]
  > Réduire la portée des champs Source et de Service permet de réduire la surface d’attaque. Utilisez l’étendue plus limitée qui permet des fonctionnalités.

* **Règles de trafic d’application**: Il existe deux règles de trafic d’application. L’un concerne le trafic web frontal. L’autre traite le trafic principal comme serveur web vers couche de données. Ces règles varient selon l’architecture réseau et flux de trafic.
  
  * La règle frontale pour le trafic web :
  
    ![Règle web de pare-feu ][12]
  
    Cette règle NAT de Destination autorise le trafic de réelle application d’atteindre le serveur d’applications. Contrairement aux règles de sécurité, gestion et et autres éléments riches, règles d’application permettent aux utilisateurs externes ou des services accéder aux applications. Cet exemple a un seul serveur web sur le port 80, ce qui permet une règle d’application unique rediriger le trafic destiné à une adresse IP externe à la place acheminer vers les adresse IP interne du serveur web. Session de trafic redirigée est remappée par NAT vers le serveur interne.

    > [!NOTE]
    > Il n’existe aucun port assigné dans le **liste cible** champ. Par conséquent, le port entrant 80 (ou 443 pour le Service sélectionné) est utilisé pour la redirection du serveur web. Si le serveur web écoute sur un port différent comme 8080, vous pouvez mettre à jour le champ de la liste des cibles à 10.0.1.4:8080 pour permettre ainsi la redirection de port.
  
  * La règle de serveur principal autorise le serveur web communiquer avec le serveur AppVM01, et non AppVM02, via **n’importe quel** service :
  
    ![Règle AppVM01 de pare-feu ][13]
  
    Cette règle passe permet à n’importe quel serveur IIS sur le sous-réseau frontal d’atteindre AppVM01 (10.0.2.5) sur n’importe quel port à l’aide de n’importe quel protocole afin que les données sont accessibles par l’application web.
  
    Dans cette capture d’écran, `<explicit-dest>` est utilisé dans le **Destination** champ pour désigner 10.0.2.5 comme destination. Vous pouvez spécifier l’adresse IP explicitement comme indiqué dans la capture d’écran. Vous pouvez également utiliser un objet réseau nommé comme dans les conditions préalables pour le serveur DNS. L’administrateur de pare-feu peut choisir la méthode à utiliser. Pour ajouter 10.0.2.5 comme destination explicite, double-cliquez sur la première ligne vide sous `<explicit-dest>` et entrez l’adresse dans la boîte de dialogue qui s’ouvre.
  
    Avec cette règle pass, aucune traduction n’est nécessaire, car il gère le trafic interne. Vous pouvez définir le **méthode de connexion** à `No SNAT`.
  
    > [!NOTE]
    > Le réseau Source dans cette règle est n’importe quelle ressource sur le sous-réseau frontal s’il en existe qu’un seul. Si votre architecture spécifie un nombre connu de serveurs web, vous pouvez créer une ressource d’objet réseau pour être plus précis à ces adresses IP exactes au lieu de l’ensemble du sous-réseau frontal.

    > [!TIP]
    > Cette règle utilise le service **n’importe quel** pour faciliter l’installation et utilisez l’exemple d’application. Il permet de ICMPv4 (ping) dans une seule règle. Toutefois, pour réduire l’attaque surface au-delà de cette limite, nous recommandons de limiter les ports et protocoles de Services à la valeur minimale possible qui permettent le fonctionnement de l’application.

    > [!TIP]
    > Bien que cet exemple de règle utilise `<explicit-dest>` référence, vous devez utiliser une approche cohérente tout au long de la configuration du pare-feu. Il est recommandé à l’aide d’un objet réseau nommé pour faciliter la lisibilité et la prise en charge. Le `<explicit-dest>` affichée ici uniquement pour montrer une méthode de référence alternative. Nous ne généralement recommandé, en particulier pour des configurations complexes.

* **Règle sortant vers internet**: Cette règle pass autorise le trafic à partir de n’importe quel réseau Source vers les réseaux de Destination sélectionnés. Le pare-feu Barracuda NextGen a généralement cette règle « on » par défaut, mais dans un état désactivé. Avec le bouton droit sur cette règle pour accéder à la **activer la règle** commande. Modifier la règle indiquée dans la capture d’écran pour ajouter les objets de réseau pour les sous-réseaux principal et frontal à l’attribut Source de cette règle. Vous avez créé ces objets réseau dans la section Configuration requise de cet article.
  
    ![Règle de trafic sortant de pare-feu][14]

* **Règle DNS**: Cette règle pass autorise uniquement le trafic DNS (port 53) vers le serveur DNS. Pour cet environnement, la plupart du trafic du composant frontal au serveur principal est bloqué pour cette règle autorise spécifiquement le trafic DNS.
  
    ![Règle DNS de pare-feu ][15]
  
    > [!NOTE]
    > Le **méthode de connexion** est défini sur `No SNAT` , car cette règle est pour l’adresse IP interne pour le trafic des adresses IP interne et aucune redirection via NAT n’est requise.

* **Règle sous-réseau à sous-réseau**: Cette règle de test par défaut a été activée et modifiée pour autoriser n’importe quel serveur sur le sous-réseau du serveur principal pour vous connecter à n’importe quel serveur sur le sous-réseau frontal. Cette règle coves uniquement le trafic interne afin que la **méthode de connexion** peut être définie sur `No SNAT`.

    ![Règle Intra-réseau virtuel de pare-feu][16]
  
    > [!NOTE]
    > Le **bidirectionnel** case à cocher n’est pas sélectionné ici pour cette règle s’applique uniquement dans un sens. Une connexion peut être lancée à partir du sous-réseau principal pour le réseau frontal, mais pas l’inverse. Si cette case à cocher a été sélectionnée, cette règle permet le trafic bidirectionnel, ce qui nous avons spécifié comme indésirable dans notre diagramme logique.

* **Règle Refuser tout trafic**: Cette règle doit toujours être la dernière règle en termes de priorité. Si le flux de trafic ne correspond pas à des règles ci-dessus, cette règle fait à supprimer. La règle est généralement activée par défaut, donc aucune modification n’est nécessaire.
  
    ![Règle de refus de pare-feu][17]

> [!IMPORTANT]
> Une fois que toutes les règles précédentes sont créées, passez en revue la priorité de chaque règle pour garantir le trafic est autorisé ou refusé comme vous le souhaitez. Pour cet exemple, les règles sont répertoriées dans l’ordre, qu'ils doivent apparaître dans la grille de principale du client de gestion de Barracuda de règles de transfert.

## <a name="rule-activation"></a>Activation de règle

Une fois que vous avez modifié la règle définie pour répondre aux spécifications du schéma logique, vous devez télécharger l’ensemble de règles dans le pare-feu et activez-le.

![Activation de règle de pare-feu][18]

Rechercher dans le coin supérieur droit de la fenêtre client de gestion et sélectionnez **envoyer les modifications** pour charger les règles modifiées au pare-feu. Sélectionnez **Activer**.

Lorsque vous activez l’ensemble de règles de pare-feu, cet exemple d’environnement est terminée.

## <a name="traffic-scenarios"></a>Scénarios de trafic

> [!IMPORTANT]
> N’oubliez pas que *tous les* le trafic est fourni via le pare-feu. Bureau à distance au serveur IIS01, vous devez vous connecter au pare-feu sur le port 8014 et ensuite autoriser le pare-feu à acheminer la requête RDP en interne pour le port RDP de IIS01. Le portail Azure **Connect** bouton ne fonctionne pas car il n’est pas directe RDP vers IIS01 aussi du portail. Toutes les connexions à partir d’internet sont pour le service de sécurité et un port (par exemple, secscv001.cloudapp.NET : xxxx). Le diagramme ci-dessus pour le mappage de port externe et interne IP et port de référence.

Pour ces scénarios, les règles de pare-feu suivantes doivent être en place :

1. Gestion du pare-feu (gestion de pare-feu)
2. RDP vers IIS01
3. RDP vers DNS01
4. RDP vers AppVM01
5. RDP vers AppVM02
6. Trafic d’application vers le web
7. Trafic d’application vers AppVM01
8. Sortie vers internet
9. Serveur frontal vers DNS01
10. Trafic intra-sous-réseau (principal vers frontal uniquement)
11. Refuser tout

Votre ensemble de règles de pare-feu plus susceptibles de nécessiter davantage de règles que celles dans cet exemple. Ils sont susceptibles d’avoir également les numéros de priorité différents. Reportez-vous à cette liste et les numéros associés pour leur priorité relative entre eux. Par exemple, la règle « RDP vers IIS01 » peut être numéro 5 sur le pare-feu, mais tant que ses ci-dessous la « gestion du pare-feu » règle et au-dessus de la « RDP DNS01 » de règle, le jeu s’aligne avec l’intention de cette liste. Cette liste vous permet également de simplifier les instructions pour les scénarios qui suivent. Par exemple, « règle de pare-feu 9 (DNS). » N’oubliez pas que les quatre règles RDP sont collectivement appelés « règles RDP » lorsque le scénario de trafic n’est pas lié à RDP.

Rappelez-vous également que les groupes de sécurité réseau (NSG) sont en place pour le trafic internet entrant sur les sous-réseaux frontaux et principaux.

### <a name="allowed-internet-to-web-server"></a>(Autorisé) Internet au serveur web

1. Un utilisateur internet demande une page HTTP en provenance de SecSvc001.CloudApp.Net (service cloud d’accessible sur internet).
1. Le service de cloud transmet le trafic via un point de terminaison ouvert sur le port 80 à l’interface de pare-feu sur 10.0.0.4 : 80.
1. Aucun groupe de sécurité réseau n’est affecté au sous-réseau de sécurité pour les règles du NSG système autorisent le trafic vers le pare-feu.
1. Le trafic atteint une adresse IP interne du pare-feu (10.0.1.4).
1. Le pare-feu effectue le traitement des règles :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 6 (application : Web) s’applique. Le trafic est autorisé. Pare-feu redirige le trafic via NAT sur 10.0.1.4 (IIS01).
1. Le sous-réseau frontal effectue le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (blocage internet) ne s’applique pas. Le pare-feu redirigé ce trafic via NAT pour l’adresse source se trouve désormais le pare-feu. Étant donné que le pare-feu est sur le sous-réseau de sécurité et apparaît en tant que le trafic local vers le groupe de sécurité réseau du sous-réseau frontal, le trafic est autorisé. Déplacer à la règle suivante.
   1. Règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau de sorte que ce trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. IIS01 écoute le trafic web. Il reçoit cette demande et commence à traiter la demande.
1. IIS01 tente de lancer une session FTP vers AppVM01 sur le sous-réseau principal.
1. L’itinéraire UDR sur le sous-réseau frontal fait du pare-feu le tronçon suivant.
1. Il n’existe aucune règle sortante sur le sous-réseau frontal pour le trafic est autorisé.
1. Le pare-feu commence le traitement de la règle :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 6 (application : Web) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 7 (application : back-end) s’applique. Le trafic est autorisé. Le pare-feu transfère le trafic vers 10.0.2.5 (AppVM01).
1. Le sous-réseau principal effectue le traitement de la règle de trafic entrant :
    1. Règle NSG 1 (blocage internet) ne s’applique pas. Déplacer à la règle suivante.
    1. Règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau. Le trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. AppVM01 reçoit la demande, lance la session et répond.
1. L’itinéraire UDR sur le sous-réseau du serveur principal fait du pare-feu le tronçon suivant.
1. Il n’existe aucune règle NSG sortante sur le sous-réseau du serveur principal afin de la réponse est autorisée.
1. Car il est un trafic de retour sur une session établie, le pare-feu retransfère la réponse sur le serveur web (IIS01).
1. Sous-réseau frontal effectue le traitement de la règle de trafic entrant :
    1. Règle NSG 1 (blocage internet) ne s’applique pas. Déplacer à la règle suivante.
    1. Les règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau, donc ce trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. Le serveur IIS reçoit la réponse et termine la transaction avec AppVM01. Le serveur termine la réalisation de la réponse HTTP, puis il envoie au demandeur.
1. Il n’existe aucune règle NSG sortante sur le sous-réseau frontal pour la réponse est autorisée.
1. La réponse HTTP atteint le pare-feu. S’agissant d’une réponse à une session NAT établie, le pare-feu l’accepte.
1. Le pare-feu redirige la réponse à l’utilisateur d’internet.
1. Il n’existe aucune règle NSG sortante ou tronçon UDR sur le sous-réseau frontal de la réponse est autorisé. L’utilisateur internet reçoit la page web demandée.

### <a name="allowed-internet-rdp-to-back-end"></a>(Autorisé) RDP Internet au serveur principal

1. Un administrateur du serveur sur internet demande une session RDP appvm01 via secsvc001.cloudapp.NET : 8025. 8025 étant le numéro de port affecté par l’utilisateur pour la règle de pare-feu 4 (AppVM01 RDP).
1. Le service de cloud transmet le trafic via le point de terminaison ouvert sur le port 8025 vers l’interface de pare-feu sur 10.0.0.4:8025.
1. Aucun groupe de sécurité réseau n’est affecté au sous-réseau de sécurité pour le système permet le trafic vers le pare-feu.
1. Le pare-feu effectue le traitement des règles :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 2 (RDP IIS) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 3 (RDP DNS01) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 4 (AppVM01 RDP) s’applique donc le trafic est autorisé. Le pare-feu il réachemine via NAT à 10.0.2.5:3386 (port RDP sur AppVM01).
1. Le sous-réseau principal effectue le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (blocage internet) ne s’applique pas. Le pare-feu redirigé ce trafic via NAT pour l’adresse source se trouve désormais le pare-feu qui se trouve sur le sous-réseau de sécurité. Il est vu par le sous-réseau principal groupe de sécurité réseau pour le réseau local et est autorisée. Déplacer à la règle suivante.
   1. Règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau de sorte que ce trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. AppVM01 est à l’écoute pour le trafic RDP et répond.
1. Il n’existe aucune règle NSG sortante afin d’appliquent des règles par défaut. Retour de trafic est autorisé.
1. UDR achemine le trafic sortant vers le pare-feu le tronçon suivant.
1. Car il est un trafic de retour sur une session établie, le pare-feu retransfère la réponse à l’utilisateur d’internet.
1. Session Bureau à distance est activée.
1. AppVM01 demande de mot de passe utilisateur.

### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) Recherche DNS de serveur Web sur le serveur DNS

1. Serveur Web IIS01 demande un flux HTTP de données\:\/\/www.data.gov, mais doit résoudre l’adresse.
1. La configuration réseau pour les listes de réseau virtuel DNS01 (10.0.2.4 sur le sous-réseau principal) comme serveur DNS principal. IIS01 envoie la requête DNS pour DNS01.
1. UDR achemine le trafic sortant vers le pare-feu le tronçon suivant.
1. Aucune règle NSG sortante n’est liés au sous-réseau frontal. Le trafic est autorisé.
1. Le pare-feu effectue le traitement des règles :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 6 et 7 (règles d’application) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 8 (internet) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 9 (DNS) s’applique. Le trafic est autorisé. Pare-feu transfère le trafic vers 10.0.2.4 (DNS01).
1. Le sous-réseau principal effectue le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (blocage internet) ne s’applique pas. Déplacer à la règle suivante.
   1. Règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau. Le trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. Le serveur DNS reçoit la demande.
1. Le serveur DNS n’a pas l’adresse mis en cache et demande à un serveur DNS racine sur internet.
1. UDR achemine le trafic sortant vers le pare-feu le tronçon suivant.
1. Il y a aucune règle NSG sortante sur le sous-réseau du serveur principal pour le trafic n’est autorisé.
1. Le pare-feu effectue le traitement des règles :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 6 et 7 (règles d’application) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 8 (internet) s’applique. Le trafic est autorisé. La session est redirigée via SNAT au serveur DNS racine sur internet.
1. Internet DNS répond de serveur. Cette session a été démarrée à partir du pare-feu afin de la réponse est acceptée par le pare-feu.
1. Cette session est déjà établie pour le pare-feu transmet la réponse au serveur initiateur, DNS01.
1. Le sous-réseau principal effectue le traitement de la règle de trafic entrant :
    1. Règle NSG 1 (blocage internet) ne s’applique pas. Déplacer à la règle suivante.
    1. Règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau à ce trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. Le serveur DNS reçoit et met en cache la réponse et répond ensuite à la demande initiale à IIS01.
1. L’itinéraire UDR sur le sous-réseau du serveur principal fait du pare-feu le tronçon suivant.
1. Aucune règle NSG sortante n’existe sur le sous-réseau du serveur principal pour le trafic est autorisé.
1. Cette session est déjà établie sur le pare-feu pour le pare-feu redirige la réponse sur le serveur IIS.
1. Le sous-réseau frontal effectue le traitement de la règle de trafic entrant :
    1. Il n’existe aucune règle de groupe de sécurité réseau pour le trafic entrant à partir du sous-réseau du serveur principal vers le sous-réseau frontal aucune des règles NSG s’appliquent donc pas.
    1. La règle de système par défaut autorise le trafic entre sous-réseaux. Le trafic est autorisé.
1. IIS01 reçoit la réponse à partir de DNS01.

### <a name="allowed-back-end-server-to-front-end-server"></a>(Autorisé) Serveur principal au serveur frontal

1. Un administrateur connecté à AppVM02 via RDP demande un fichier directement depuis le serveur IIS01 via l’Explorateur de fichiers windows.
1. L’itinéraire UDR sur le sous-réseau du serveur principal fait du pare-feu le tronçon suivant.
1. Il n’existe aucune règle NSG sortante sur le sous-réseau du serveur principal afin de la réponse est autorisée.
1. Le pare-feu effectue le traitement des règles :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 6 et 7 (règles d’application) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 8 (internet) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 9 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 10 (Intra-sous-réseau) s’applique. Le trafic est autorisé. Le pare-feu transmet le trafic à 10.0.1.4 (IIS01).
1. Le sous-réseau frontal commence le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (blocage internet) ne s’appliquent pas, passer à la règle suivante
   1. Les règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau de sorte que le trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. En supposant que l’autorisation et une authentification correcte, IIS01 accepte la demande et répond.
1. L’itinéraire UDR sur le sous-réseau frontal fait du pare-feu le tronçon suivant.
1. Il n’existe aucune règle NSG sortante sur le sous-réseau frontal pour la réponse est autorisée.
1. Cette session existe déjà sur le pare-feu pour cette réponse est autorisée. Le pare-feu renvoie la réponse à AppVM02.
1. Le sous-réseau principal effectue le traitement de la règle de trafic entrant :
    1. Règle NSG 1 (blocage internet) ne s’applique pas. Déplacer à la règle suivante.
    2. Les règles de groupe de sécurité réseau par défaut autorisent le trafic de sous-réseau à sous-réseau de sorte que le trafic est autorisé. Arrêter le traitement des règles de groupe de sécurité réseau.
1. AppVM02 reçoit la réponse.

### <a name="denied-internet-direct-to-web-server"></a>(Refusé) Internet directe au serveur web

1. Un utilisateur internet tente d’accéder au serveur de web IIS01 via le service FrontEnd001.CloudApp.Net.
1. Aucun point de terminaison ne sont ouverts pour le trafic HTTP afin de ce trafic ne passe pas par le service cloud. Le trafic n’atteint pas le serveur.
1. Si les points de terminaison sont ouverts pour une raison quelconque, le NSG (bloquer internet) sur le sous-réseau frontal bloque ce trafic.
1. Pour finir, l’itinéraire UDR de sous-réseau frontal envoie tout le trafic sortant à partir de IIS01 vers le pare-feu le tronçon suivant. Le pare-feu le considère comme le trafic asymétrique et supprime la réponse sortante.

>Par conséquent, il existe au moins trois couches indépendantes de défense entre internet et IIS01. Le service cloud empêche l’accès non autorisé ou inapproprié.

### <a name="denied-internet-to-back-end-server"></a>(Refusé) Internet vers le serveur principal

1. Un utilisateur internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net.
2. Il n’y a aucun point de terminaison ouvert pour le partage de fichiers pour cette demande ne remplit pas le service cloud. Le trafic n’atteint pas le serveur.
3. Si les points de terminaison sont ouverts pour une raison quelconque, le NSG (bloquer internet) bloque ce trafic.
4. Pour finir, l’itinéraire UDR envoie tout le trafic sortant à partir de AppVM01 vers le pare-feu le tronçon suivant. Le pare-feu le considère comme le trafic asymétrique et supprime la réponse sortante.

> Par conséquent, il existe au moins trois couches indépendantes de défense entre internet et AppVM01. Le service cloud empêche l’accès non autorisé ou inapproprié.

### <a name="denied-front-end-server-to-back-end-server"></a>(Refusé) Serveur frontal vers serveur principal

1. IIS01 est compromis et un code malveillant essaie d’analyser les serveurs du sous-réseau du serveur principal est en cours d’exécution.
1. L’itinéraire UDR de sous-réseau frontal envoie tout le trafic sortant à partir de IIS01 vers le pare-feu le tronçon suivant. La machine virtuelle compromise Impossible de modifier ce routage.
1. Le pare-feu traite le trafic. Si la demande est vers AppVM01 ou vers le serveur DNS pour les recherches DNS, le pare-feu peut potentiellement autoriser le trafic en raison des règles de pare-feu 7 et 9. Tout autre trafic est bloqué par la règle de pare-feu 11 (refuser tout).
1. Si vous activez la détection avancée des menaces sur le pare-feu, le trafic qui contient les signatures connus ou des modèles signalant une règle avancée contre les menaces peut être évité. Cette mesure peut fonctionner même si le trafic est autorisé en fonction des règles de transfert de base qui sont abordées dans cet article. Détection avancée des menaces n’est pas abordée dans ce document. Consultez la documentation du fournisseur de votre appliance de réseau spécifique fonctionnalités avancées.

### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Refusé) Recherche Internet DNS sur le serveur DNS.

1. Un utilisateur internet tente de rechercher un enregistrement DNS interne sur DNS01 par le biais du service BackEnd001.CloudApp.Net.
1. Dans la mesure où il n’y a aucun point de terminaison ouvert pour le trafic DNS, ce trafic ne passe pas par le service cloud. Il n’atteint pas le serveur.
1. Si les points de terminaison sont ouverts pour une raison quelconque, la règle NSG (bloquer internet) sur le sous-réseau frontal bloque ce trafic.
1. Pour finir, l’itinéraire UDR de sous-réseau principal envoie tout le trafic sortant à partir de DNS01 au pare-feu le tronçon suivant. Le pare-feu considère comme un trafic asymétrique et supprime la réponse sortante.

> Par conséquent, il existe au moins trois couches indépendantes de défense entre internet et DNS01. Le service cloud empêche l’accès non autorisé ou inapproprié.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Refusé) Internet pour accès à SQL via le pare-feu

1. Un utilisateur internet demande des données SQL à partir du service de cloud de côté internet SecSvc001.CloudApp.Net.
1. Il n’existe aucun point de terminaison ouvert pour SQL donc ce trafic ne passe pas le service cloud. Il n’atteint pas le pare-feu.
1. Si les points de terminaison SQL sont ouverts pour une raison quelconque, le pare-feu effectue le traitement des règles :
   1. Règle de pare-feu 1 (gestion de pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 2 à 5 (règles RDP) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règles de pare-feu 6 et 7 (règles d’Application) ne s’appliquent pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 8 (internet) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 9 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 10 (Intra-sous-réseau) ne s’applique pas. Déplacer à la règle suivante.
   1. Règle de pare-feu 11 (refuser tout) s’applique. Le trafic est bloqué. Arrêter le traitement des règles.

## <a name="references"></a>Références

Cette section contient les éléments suivants :

* Script complet. Enregistrez-le dans un fichier de script PowerShell.
* Configuration du réseau. Enregistrez-le dans un fichier nommé NetworkConf2.xml.

Modifiez les variables définies par l’utilisateur dans les fichiers en fonction des besoins. Exécutez le script, puis suivez les instructions de configuration de règle de pare-feu répertoriées plus haut dans cet article.

### <a name="full-script"></a>Script complet

Après avoir défini les variables définies par l’utilisateur, exécutez ce script pour :

1. Connexion à un abonnement Azure
1. Création d’un nouveau compte de stockage
1. Créer un nouveau réseau virtuel et trois sous-réseaux, comme indiqué dans le fichier de configuration de réseau
1. Créer cinq machines virtuelles : un pare-feu et quatre machines virtuelles Windows Server
1. Configurer UDR :
   1. Créez deux nouvelles tables de routage
   1. Ajouter des itinéraires aux tables
   1. Lier des tables aux sous-réseaux appropriés
1. Activer le transfert IP sur le NVA
1. Configurer le groupe de sécurité réseau :
   1. Créer un groupe de sécurité réseau
   1. Ajouter une règle
   1. Lier le groupe de sécurité réseau aux sous-réseaux appropriés

Exécutez cette commande PowerShell script localement sur un internet connecté PC ou un serveur.

> [!IMPORTANT]
> Lorsque vous exécutez ce script, avertissements ou autres messages d’information peuvent s’afficher dans PowerShell. Seuls les messages d’erreur rouge sont source de préoccupation.

```powershell
    <#
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) {
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the FrontEnd and BackEnd subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install BackEnd resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

### <a name="network-config-file"></a>Fichier de configuration réseau

Enregistrez ce fichier XML avec l’emplacement mis à jour. Modifier le `$NetworkConfigFile` variable dans le script complet ci-dessus pour créer un lien vers le fichier de configuration réseau enregistré.

```xml
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez installer un exemple d’application à l’aide de cet exemple de réseau de périmètre.

> [!div class="nextstepaction"]
> [Exemple de script application](./virtual-networks-sample-app.md)

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Zone DMZ bidirectionnelle avec NVA, NSG et UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Affichage logique des règles de pare-feu"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Créer un objet réseau FrontEnd"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Créer un objet de serveur DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Copie de la règle RDP par défaut"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Règle AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Icône de redirection d’application"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Icône NAT de destination"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Icône de réussite"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Règle de gestion de pare-feu"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Règle RDP de pare-feu"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Règle web de pare-feu "
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Règle AppVM01 de pare-feu "
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Règle de trafic sortant de pare-feu"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Règle DNS de pare-feu"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Règle Intra-réseau virtuel de pare-feu"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Règle de refus de pare-feu"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Activation de règle de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
