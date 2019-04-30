---
title: Exemple de réseau de périmètre – Build un réseau de périmètre pour protéger les applications avec un pare-feu et des groupes de sécurité réseau | Microsoft Docs
description: Créer un réseau de périmètre avec un pare-feu et des groupes de sécurité réseau (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868319"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 : Créer un réseau de périmètre pour protéger les applications avec un pare-feu et des groupes de sécurité réseau
[Revenir à la page de sécurité réseau et de services cloud Microsoft][HOME]

Cet exemple montre comment créer un réseau de périmètre (également appelé *DMZ*, *zone démilitarisée*, et *sous-réseau filtré*) avec un pare-feu, quatre ordinateurs Windows Server, et les groupes de sécurité réseau (NSG). Il inclut des détails sur chacune des commandes appropriées pour fournir une compréhension plus approfondie de chaque étape. La section « Scénarios de trafic » fournit une explication pas à pas de l’évolution du trafic à travers les couches de défense du réseau de périmètre. Enfin, la section « Références » fournit le code complet et des instructions pour la création de cet environnement pour tester et faire des essais avec différents scénarios.

![Réseau de périmètre entrant avec appliance virtuelle réseau et des groupes de sécurité réseau][1]

## <a name="environment"></a>Environnement 
Cet exemple est basé sur un scénario avec un abonnement Azure qui contient ces éléments :

* Deux services cloud : FrontEnd001 et BackEnd001.
* Un réseau virtuel nommé CorpNetwork qui a deux sous-réseaux : FrontEnd et BackEnd.
* Un seul groupe de sécurité réseau est appliqué aux deux sous-réseaux.
* Une appliance virtuelle réseau : Un pare-feu Barracuda nextgen Firewall connecté au sous-réseau FrontEnd.
* Un ordinateur Windows Server qui représente un serveur d’applications web : IIS01.
* Deux ordinateurs Windows Server qui représentent des serveurs principaux d’applications : AppVM01 et AppVM02.
* Un ordinateur Windows Server qui représente un serveur DNS : DNS01.

> [!NOTE]
> Bien que cet exemple utilise un pare-feu Barracuda nextgen Firewall, nombreuses appliances virtuelles réseau peut être utilisés.
> 
> 

Le script PowerShell dans la section « Références » de cet article s’appuie la majeure partie de l’environnement décrit ici. Les machines virtuelles et les réseaux virtuels sont générés par le script, mais ces processus ne sont pas décrits en détail dans ce document.

Pour créer l’environnement :

1. Enregistrez le fichier XML de configuration réseau inclus dans la section « Références » (mise à jour des noms, emplacements et IP qui correspondent à votre scénario).
2. Mettre à jour les variables définies par l’utilisateur dans le script PowerShell pour correspondre à l’environnement que le script sera exécuté sur (abonnements, noms de service et ainsi de suite).
3. Exécutez le script dans PowerShell.

> [!NOTE]
> La région spécifiée dans le script PowerShell doit correspondre à la région spécifiée dans le fichier XML de configuration réseau.
>
>

Une fois que le script s’exécute correctement, vous pouvez effectuer ces étapes :

1. Configurer les règles de pare-feu. Consultez la section « Règles de pare-feu » de cet article.
2. Si vous le souhaitez, vous pouvez configurer le serveur web et le serveur d’application avec une application web simple permettant le test avec la configuration de réseau de périmètre. Vous pouvez utiliser les deux applications de scripts fournis dans la section « Références ».

La section suivante explique la plupart des instructions du script qui se rapportent à des groupes de sécurité réseau.

## <a name="nsgs"></a>Groupes de sécurité réseau
Dans cet exemple, un groupe NSG est créé, puis chargé avec six règles.

> [!TIP]
> En général, vous devez d’abord créer vos règles « Autoriser » spécifiques et les règles de « Refus » plus génériques de la dernière. Les contrôles de priorité qui les règles sont évaluées en premier. Une fois que le trafic est trouvé qui s’applique à une règle spécifique, aucune autre règle n’est évaluées. Règles de groupe de sécurité réseau peuvent être appliquées dans le trafic entrant ou la direction sortante (du point de vue du sous-réseau).
> 
> 

De façon déclarative, ces règles sont créées pour le trafic entrant :

1. Le trafic DNS interne (port 53) est autorisé.
2. Le trafic RDP (port 3389) à partir d’internet à toute machine virtuelle est autorisé.
3. Le trafic HTTP (port 80) à partir d’internet vers l’appliance virtuelle réseau (pare-feu) est autorisé.
4. Tout le trafic (tous les ports) à partir de IIS01 vers AppVM01 est autorisé.
5. Tout le trafic (tous les ports) à partir d’internet vers le réseau virtuel entier (les deux sous-réseaux) est refusé.
6. Tout le trafic (tous les ports) à partir du sous-réseau frontal vers le sous-réseau principal est refusé.

Avec ces règles liées à chaque sous-réseau, si une requête HTTP ont été entrante à partir d’internet vers le serveur web, à la fois la règle 3 (autoriser) et règle 5 (refus) serait semblent s’appliquer également, mais étant donné que la règle 3 a une priorité plus élevée, seulement il s’applique. Règle 5 n’entre en jeu. Par conséquent, la requête HTTP pourront vers le pare-feu.

Si le même trafic tentait d’atteindre le serveur DNS01, la règle 5 (refus) serait la première à appliquer, donc le trafic n’est pas autorisé à passer au serveur. La règle 6 (refus) bloque la communication avec le sous-réseau principal (excepté le trafic autorisé dans les règles 1 et 4) du sous-réseau frontal. Cela protège le réseau principal au cas où un attaquant compromettrait l’application web sur le serveur frontal. Dans ce cas, l’attaquant aurait ont un accès limité au réseau principal « protégé ». (L’attaquant serait en mesure d’accéder uniquement aux ressources exposées sur le serveur AppVM01.)

Il existe une règle par défaut qui autorise le trafic sortant à internet. Dans cet exemple, nous allons autoriser le trafic sortant et ne modifiez ne pas les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, vous avez besoin d’un routage défini par l’utilisateur. Vous pouvez en savoir plus sur cette technique dans un autre exemple dans le [document de limite de sécurité principal][HOME].

Les règles du NSG décrites ici sont similaires aux règles NSG dans [exemple 1 : créer une zone DMZ simple avec groupes de sécurité réseau][Example1]. Passez en revue la description du groupe de sécurité réseau dans cet article pour une description détaillée de chaque règle NSG et ses attributs.

## <a name="firewall-rules"></a>Règles de pare-feu
Vous devez installer un client de gestion sur un ordinateur pour gérer le pare-feu et de créer les configurations nécessaires. Consultez le fournisseur de la documentation à partir de votre pare-feu (ou autre NVA) sur la gestion de l’appareil. Le reste de cette section décrit la configuration du pare-feu lui-même, via le client de gestion du fournisseur (pas le portail Azure ou PowerShell).

Consultez [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) pour obtenir des instructions pour le téléchargement du client et la connexion vers le pare-feu Barracuda utilisé dans cet exemple.

Vous devez créer des règles de transfert sur le pare-feu. Étant donné que le scénario dans cet exemple achemine uniquement le trafic internet entrant vers le pare-feu et au serveur web, vous devez uniquement une règle NAT de transfert. Sur le pare-feu Barracuda utilisé dans cet exemple, la règle serait une règle NAT de Destination (Dst NAT) pour autoriser ce trafic.

Pour créer la règle suivante (ou pour vérifier les règles par défaut existantes), procédez comme suit :
1. Tableau de bord client Admin Barracuda NG, sous l’onglet configuration, dans le **Configuration opérationnelle** section, sélectionnez **Ruleset**. 

   Une grille appelé **règles principales** montre existante active et la désactivation des règles du pare-feu.

2. Pour créer une nouvelle règle, sélectionnez le petit vert **+** situé dans l’angle supérieur droit de cette grille. (Votre pare-feu peut être verrouillé. Si vous voyez un bouton marqué **verrou** et ne peuvent pas créer ou modifier des règles, sélectionnez le bouton pour déverrouiller l’ensemble de règles et autoriser la modification.)
  
3. Pour modifier une règle existante, sélectionnez la règle, avec le bouton droit, puis cliquez **modifier la règle**.

Créer une nouvelle règle nommée quelque chose comme **WebTraffic.** 

L'icône de la règle NAT de destination se présente ainsi :  ![Icône NAT de destination][2]

La règle proprement dite se présente comme suit :

![Règle de pare-feu][3]

N’importe quelle adresse entrante qui atteint le pare-feu essayant d’atteindre HTTP (port 80 ou 443 pour HTTPS) est envoyée en dehors de l’interface du pare-feu DHCP1 Local IP et redirigée vers le serveur web avec l’adresse IP 10.0.1.5. Étant donné que le trafic entrant sur le port 80 et n’accédant au serveur web sur le port 80, aucune modification du port est nécessaire. Mais la liste des cibles aurait pu être 10.0.1.5:8080 si le serveur web écoutait sur le port 8080, ce qui traduit le port 80 entrant sur le pare-feu pour le port 8080 entrant sur le serveur web.

Vous devez également spécifier une méthode de connexion. Pour la règle de Destination à partir d’internet, SNAT dynamique est la méthode la plus appropriée.

Même si vous avez uniquement créé une règle, il est important de définir sa priorité correctement. Dans la grille de toutes les règles du pare-feu, si cette nouvelle règle figure au bas (en dessous de la règle BLOCKALL), il sera jamais entrent en jeu. Assurez-vous que la nouvelle règle pour le trafic web est au-dessus de la règle BLOCKALL.

Une fois que la règle est créée, vous devez placer dans le pare-feu et puis activez-le. Si vous ne prenez pas ces étapes, la modification de règle ne prendre effet. La section suivante décrit le processus de push et d’activation.

## <a name="rule-activation"></a>Activation de règle
Maintenant que la règle est ajoutée à l’ensemble de règles, vous devez télécharger l’ensemble de règles dans le pare-feu et activez-le.

![Activation de règle de pare-feu][4]

Dans le coin supérieur droit du client de gestion, vous verrez un groupe de boutons. Sélectionnez **envoyer les modifications** pour envoyer l’ensemble de règles modifié vers le pare-feu, puis sélectionnez **activer**.

Maintenant que vous avez activé l’ensemble de règles de pare-feu, l’environnement est terminé. Si vous le souhaitez, vous pouvez exécuter les exemples de scripts d’application dans la section « Références » pour ajouter une application à l’environnement. Si vous ajoutez une application, vous pouvez tester les scénarios de trafic décrits dans la section suivante.

> [!IMPORTANT]
> Vous devez être conscient que vous n’est pas atteint le serveur web directement. Lorsqu’un navigateur demande une page HTTP en provenance de FrontEnd001.CloudApp.Net, le point de terminaison HTTP (port 80) transmet le trafic vers le pare-feu, et non vers le serveur web. Le pare-feu puis, en raison de la règle que vous avez créé précédemment, utilise NAT pour mapper la demande au serveur web.
> 
> 

## <a name="traffic-scenarios"></a>Scénarios de trafic
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Autorisé) Web vers le serveur web via le pare-feu
1. Un utilisateur internet demande une page HTTP en provenance de FrontEnd001.CloudApp.Net (service cloud sur internet).
2. Le service de cloud transmet le trafic via un point de terminaison ouvert sur le port 80 pour l’interface du pare-feu local sur 10.0.1.4:80.
3. Le sous-réseau frontal commence le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   2. Règle NSG 2 (RDP) ne s’applique pas. Déplacer à la règle suivante.
   3. Règle NSG 3 (internet vers pare-feu) s’applique. Autoriser le trafic. Arrêter le traitement des règles.
4. Le trafic atteint l’adresse IP interne du pare-feu (10.0.1.4).
5. Un règle de transfert de pare-feu détermine ce trafic du port 80 et redirige vers le serveur web IIS01.
6. IIS01 écoute le trafic web reçoit la demande et commence à traiter la demande.
7. IIS01 demande des informations à partir de l’instance de SQL Server sur AppVM01.
8. Il n’existe aucune règle sortante sur le sous-réseau frontal, le trafic est autorisé.
9. Le sous-réseau principal démarre le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   2. Règle NSG 2 (RDP) ne s’applique pas. Déplacer à la règle suivante.
   3. Règle NSG 3 (internet vers pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   4. Règle NSG 4 (IIS01 vers AppVM01) s’applique. Autoriser le trafic. Arrêter le traitement des règles.
10. L’instance de SQL Server sur AppVM01 reçoit la demande et répond.
11. Comme il n’y a aucune règle sortante sur le sous-réseau principal, la réponse est autorisée.
12. Le sous-réseau frontal commence le traitement de la règle de trafic entrant :
    1. Il n’existe aucune règle de groupe de sécurité réseau qui s’applique à aucune des règles NSG s’appliquent donc pas le trafic à partir du sous-réseau du serveur principal pour le sous-réseau frontal, entrant.
    2. La règle de système par défaut autorisant le trafic entre sous-réseaux autorise ce trafic, le trafic est autorisé.
13. IIS01 reçoit la réponse à partir d’AppVM01, complète la réponse HTTP et il envoie au demandeur.
14. Comme il s’agit d’une session NAT provenant du pare-feu, la destination de la réponse est initialement pour le pare-feu.
15. Le pare-feu reçoit la réponse du serveur web et la transfère à l’utilisateur d’internet.
16. Comme il n’y a aucune règle sortante sur le sous-réseau frontal, la réponse est autorisée, et l’utilisateur internet reçoit la page web.

#### <a name="allowed-rdp-to-backend"></a>(Autorisé) RDP vers le serveur principal
1. Un administrateur du serveur sur internet demande une session RDP appvm01 sur BackEnd001.CloudApp.Net :*xxxxx*, où *xxxxx* est le numéro de port attribué de façon aléatoire pour RDP vers AppVM01. (Vous pouvez trouver le port attribué sur le portail Azure ou à l’aide de PowerShell).
2. Étant donné que le pare-feu est écoute uniquement sur l’adresse FrontEnd001.CloudApp.Net, il n’est pas impliqué dans ce flux de trafic.
3. Le sous-réseau principal démarre le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   2. Règle NSG 2 (RDP) s’applique. Autoriser le trafic. Arrêter le traitement des règles.
4. Comme il n’y a aucune règle sortante, les règles par défaut s’appliquent et retournent le trafic est autorisé.
5. La session RDP est activée.
6. AppVM01 demande un nom d’utilisateur et mot de passe.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) Recherche DNS de serveur Web sur le serveur DNS
1. Les besoins de serveur, IIS01, web, données de flux sur www.data.gov, mais devant résoudre l’adresse.
2. La configuration réseau pour l’affiche de réseau virtuel DNS01 (10.0.2.4 sur le sous-réseau principal) comme serveur DNS principal. IIS01 envoie la requête DNS pour DNS01.
3. Comme il n’y a aucune règle sortante sur le sous-réseau frontal, le trafic est autorisé.
4. Le sous-réseau principal démarre le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (DNS) s’applique. Autoriser le trafic. Arrêter le traitement des règles.
5. Le serveur DNS reçoit la demande.
6. Le serveur DNS n’a pas l’adresse mis en cache et interroge le serveur DNS racine sur internet.
7. Comme il n’y a aucune règle sortante sur le sous-réseau principal, le trafic est autorisé.
8. Internet DNS répond de serveur. Étant donné que la session a été initialisée en interne, la réponse est autorisée.
9. Le serveur DNS met en cache la réponse et répond à la demande à partir de IIS01.
10. Comme il n’y a aucune règle sortante sur le sous-réseau principal, le trafic est autorisé.
11. Le sous-réseau frontal commence le traitement de la règle de trafic entrant :
    1. Il n’existe aucune règle de groupe de sécurité réseau qui s’applique à aucune des règles NSG s’appliquent donc pas le trafic à partir du sous-réseau du serveur principal pour le sous-réseau frontal, entrant.
    2. La règle de système par défaut autorisant le trafic entre sous-réseaux autorise ce trafic, le trafic est autorisé.
12. IIS01 reçoit la réponse à partir de DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Autorisé) Accès aux fichiers de serveur Web sur AppVM01
1. IIS01 demande un fichier sur AppVM01.
2. Comme il n’y a aucune règle sortante sur le sous-réseau frontal, le trafic est autorisé.
3. Le sous-réseau principal démarre le traitement de la règle de trafic entrant :
   1. Règle NSG 1 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   2. Règle NSG 2 (RDP) ne s’applique pas. Déplacer à la règle suivante.
   3. Règle NSG 3 (internet vers pare-feu) ne s’applique pas. Déplacer à la règle suivante.
   4. Règle NSG 4 (IIS01 vers AppVM01) s’applique. Autoriser le trafic. Arrêter le traitement des règles.
4. AppVM01 reçoit la demande et répond avec le fichier (en supposant que l’accès est autorisé).
5. Comme il n’y a aucune règle sortante sur le sous-réseau principal, la réponse est autorisée.
6. Le sous-réseau frontal commence le traitement de la règle de trafic entrant :
   1. Il n’existe aucune règle de groupe de sécurité réseau qui s’applique à aucune des règles NSG s’appliquent donc pas le trafic à partir du sous-réseau du serveur principal pour le sous-réseau frontal, entrant.
   2. La règle de système par défaut autorisant le trafic entre sous-réseaux autorise ce trafic, le trafic est autorisé.
7. IIS01 reçoit le fichier.

#### <a name="denied-web-direct-to-web-server"></a>(Refusé) Web direct au serveur web
Étant donné que le serveur web IIS01 et le pare-feu se trouvent dans le même service cloud, ils partagent la même adresse IP publique. Par conséquent, tout le trafic HTTP est dirigé vers le pare-feu. Pendant une demande est servie avec succès, il ne peut pas accéder directement au serveur web. Il transmet, comme prévu, via le pare-feu tout d’abord. Consultez le premier scénario de cette section pour le flux de trafic.

#### <a name="denied-web-to-backend-server"></a>(Refusé) Web vers le serveur principal
1. Un utilisateur internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net.
2. Comme il n’y a aucun point de terminaison ouvert pour le partage de fichiers, cela ne passe pas le service cloud et n’atteint pas le serveur.
3. Si les points de terminaison sont ouverts pour une raison quelconque, la règle NSG 5 (internet vers le réseau virtuel) bloque le trafic.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Refusé) Recherche DNS Web sur le serveur DNS
1. Un utilisateur internet tente de rechercher un enregistrement DNS interne sur DNS01 via le service BackEnd001.CloudApp.Net.
2. Comme il n’y a aucun point de terminaison ouvert pour DNS, cela ne passe pas le service cloud et n’atteint pas le serveur.
3. Si les points de terminaison sont ouverts pour une raison quelconque, la règle NSG 5 (internet vers le réseau virtuel) bloque le trafic. (Règle 1 [DNS] ne s’applique pas pour deux raisons. Tout d’abord, l’adresse source est internet et que cette règle s’applique uniquement lorsque le réseau virtuel local est la source. Deuxièmement, cette règle est une règle d’autorisation, afin qu’il refuse jamais le trafic).

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Refusé) Accès Web vers SQL via le pare-feu
1. Un utilisateur internet demande des données SQL de FrontEnd001.CloudApp.Net (service cloud d’accessible sur internet).
2. Comme il n’y a aucun point de terminaison ouvert pour SQL, cela ne passe pas le service cloud et n’atteint pas le pare-feu.
3. Si les points de terminaison sont ouverts pour une raison quelconque, le sous-réseau frontal commence le traitement des règles de trafic entrant :
   1. Règle NSG 1 (DNS) ne s’applique pas. Déplacer à la règle suivante.
   2. Règle NSG 2 (RDP) ne s’applique pas. Déplacer à la règle suivante.
   3. Règle NSG 3 (internet vers pare-feu) s’applique. Autoriser le trafic. Arrêter le traitement des règles.
4. Le trafic atteint l’adresse IP interne du pare-feu (10.0.1.4).
5. Le pare-feu n’a aucune règle de transfert pour SQL et abandonne le trafic.

## <a name="conclusion"></a>Conclusion
Cet exemple montre un moyen relativement simple de protéger votre application avec un pare-feu et isoler le sous-réseau principal du trafic entrant.

Vous trouverez plus d’exemples et une vue d’ensemble du réseau des limites de sécurité [ici][HOME].

## <a name="references"></a>Références
### <a name="full-script-and-network-config"></a>Configuration de réseau et de script complet
Enregistrez le script complet dans un fichier de script PowerShell. Enregistrer le script de configuration réseau dans un fichier nommé NetworkConf2.xml.
Modifier les variables définies par l’utilisateur en fonction des besoins. Exécutez le script, puis suivez les instructions fournies dans la section « Règles de pare-feu » de cet article.

#### <a name="full-script"></a>Script complet
Ce script, basé sur les variables définies par l’utilisateur, allez effectuer les étapes suivantes :

1. Se connecter à un abonnement Azure.
2. Créez un compte de stockage.
3. Créer un réseau virtuel et deux sous-réseaux, tel que défini dans le fichier de configuration réseau.
4. Créez quatre machines virtuelles Windows Server.
5. Configurer le groupe de sécurité réseau. Fin de la configuration comme suit :
   * Crée un groupe de sécurité réseau.
   * Remplit le groupe de sécurité réseau avec des règles.
   * Lie le groupe de sécurité réseau aux sous-réseaux appropriés.

Vous devez exécuter ce script PowerShell localement sur un serveur ou un ordinateur connecté à internet.

> [!IMPORTANT]
> Lorsque vous exécutez ce script, les avertissements et autres messages d’information peuvent apparaître dans PowerShell. Il vous suffit de vous soucier des messages d’erreur qui s’affichent en rouge.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

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

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

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
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

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

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
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
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Fichier de configuration réseau
Enregistrez ce fichier XML avec des emplacements mis à jour, puis ajouter un lien vers ce fichier dans la variable $NetworkConfigFile dans le script précédent.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

#### <a name="sample-application-scripts"></a>Exemples de scripts d’application
Si vous souhaitez installer un exemple d’application pour cela et d’autres exemples de réseau de périmètre, consultez le [exemple de script d’application][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Zone DMZ avec groupe de sécurité réseau (NSG)"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icône NAT de destination"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Règle de pare-feu"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Activation de règle de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
