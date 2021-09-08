---
title: Configurer le redirecteur DNS pour Azure VMware Solution
description: Découvrez comment configurer le redirecteur DNS pour Azure VMware Solution à l’aide du Portail Azure.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/15/2021
ms.openlocfilehash: 4dbf13234f29eb572519f6975e152f22b7093543
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122563896"
---
# <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Configurer un redirecteur DNS dans le portail Azure

>[!IMPORTANT]
>Pour les clouds privés Azure VMware Solution créés à partir du 1er juillet 2021, vous avez maintenant la possibilité de configurer la résolution DNS privée. Pour les clouds privés créés avant le 1er juillet 2021, lesquels ont besoin d’une résolution DNS privée, ouvrez une [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support) pour demander une configuration DNS privée. 

Par défaut, les composants de gestion Azure VMware Solution, tels que vCenter, peuvent uniquement résoudre les enregistrements de nom disponibles via un DNS public. Toutefois, certains cas d’usage hybride requièrent les composants de gestion Azure VMware Solution pour résoudre les enregistrements de noms à partir du DNS hébergé en privé pour fonctionner correctement, y compris les systèmes managés par le client tels que vCenter et Active Directory.

Le DNS privé pour les composants de gestion Azure VMware Solution vous permet de définir des règles de transfert conditionnel pour le nom de domaine souhaité vers un ensemble sélectionné de serveurs DNS privés par le biais du service DNS NSX-T. 

Cette fonctionnalité utilise le service Redirecteur DNS dans NSX-T. Un service DNS et une zone DNS par défaut sont fournis dans le cadre de votre Cloud privé. Pour permettre aux composants de gestion des solutions VMware Azure de résoudre des enregistrements à partir de vos systèmes DNS privés, vous devez définir une zone de nom de domaine complet et l’appliquer au service DNS NSX-T. Le service DNS transfère de manière conditionnelle des requêtes DNS pour chaque zone en fonction des serveurs DNS externes définis dans cette zone.

>[!NOTE]
>Le service DNS est associé à un maximum de cinq zones de nom de domaine complet (FQDN). Chaque zone de nom de domaine complet est associée à trois serveurs DNS au maximum.

>[!TIP]
>Si vous le souhaitez, vous pouvez également utiliser les règles de transfert conditionnel pour les segments de charge de travail en configurant des ordinateurs virtuels sur ces segments pour utiliser l’adresse IP du service DNS NSX-T comme serveur DNS.


## <a name="architecture"></a>Architecture

Le diagramme montre que le service DNS NSX-T peut transférer des requêtes DNS vers des systèmes DNS hébergés dans des environnements Azure et locaux.

:::image type="content" source="media/networking/dns/dns-forwarder-diagram.png" alt-text="Diagramme montrant que le service DNS NSX-T peut transférer des requêtes DNS vers des systèmes DNS hébergés dans des environnements Azure et locaux." border="false":::


## <a name="configure-dns-forwarder"></a>Configurer le redirecteur DNS

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **DNS** > **Zones DNS**. Sélectionnez ensuite **Ajouter**.

   >[!NOTE]
   >Pour les clouds privés créés le 1er juillet 2021 ou après, la zone DNS par défaut est créée pour vous lors de la création du Cloud privé.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-1.png" alt-text="Capture d’écran montrant comment ajouter des zones DNS à un cloud privé Azure VMware Solution.":::

1. Sélectionnez **zone FQDN**, fournissez un nom et jusqu’à trois adresses IP de serveur DNS au format **10.0.0.53**. Sélectionnez ensuite **OK**.

   :::image type="content" source="media/networking/dns/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Capture d’écran montrant les informations nécessaires pour ajouter une zone de nom de domaine complet.":::

   >[!IMPORTANT]
   >Alors que NSX-T autorise les espaces et autres caractères non alphanumériques dans un nom de zone DNS, certaines ressources NSX, telles qu’une zone DNS, sont mappées à une ressource Azure dont les noms n’autorisent pas certains caractères. 
   >
   >Par conséquent, les noms de zones DNS qui seraient autrement valides dans NSX-T devront peut-être être modifiés pour adhérer aux [conventions d’affectation de noms des ressources Azure](../azure-resource-manager/management/resource-name-rules.md#microsoftresources).

      Cette opération prend plusieurs minutes et vous pouvez suivre la progression à partir des **Notifications**. Un message s’affiche dans les Notifications lorsque la zone DNS a été créée.

1. Ignorez le message relatif à une zone DNS par défaut, car il est créé pour vous dans le cadre de votre Cloud privé.

1. Sélectionnez l’onglet **service DNS**, puis sélectionnez **Modifier**.

   >[!TIP]
   >Pour les clouds privés créés le 1er juillet 2021 ou après, vous pouvez ignorer le message concernant une zone DNS par défaut, car elle est créée pour vous lors de la création d’un Cloud privé.


   >[!IMPORTANT]
   >Alors que certaines opérations dans votre Cloud privé peuvent être effectuées à partir du gestionnaire NSX-T, pour les clouds privés créés le 1er juillet 2021 ou après, vous _devez_ modifier le service DNS à partir de l’expérience de Mise en réseau simplifiée dans le Portail Azure pour toute modification de configuration apportée à la passerelle par défaut de niveau 1.  

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-2.png" alt-text="Capture d’écran montrant l’onglet service DNS avec le bouton Modifier sélectionné.":::   

1. Depuis le menu déroulant **zones FQDN**, sélectionnez le FQDN nouvellement crée, puis sélectionnez **OK**.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-3.png" alt-text="Capture d’écran montrant le nom de domaine complet sélectionné pour le service DNS.":::

   Cette opération prend plusieurs minutes. Une fois terminée, le message *Terminé* s’affichera à partir des **Notifications**. À ce stade, les composants de gestion de votre Cloud privé doivent être en mesure de résoudre les entrées DNS à partir de la zone de nom de domaine complet fournie au service DNS NSX-T. 

1. Répétez les étapes ci-dessus pour les autres zones de nom de domaine complet, y compris les zones de recherche inversée applicables.


## <a name="verify-name-resolution-operations"></a>Vérifier les opérations de la résolution de noms

Une fois que vous avez configuré le redirecteur DNS, vous disposez de plusieurs options pour vérifier les opérations de résolution de noms. 

### <a name="nsx-t-manager"></a>Gestionnaire NSX-T

Le gestionnaire NSX-T fournit les statistiques du Service Redirecteur DNS au niveau du service global et par zone. 

1. Dans Gestionnaire NSX-T, sélectionnez **Mise en réseau** > **DNS**, puis développer votre Service Redirecteur DNS.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services.png" alt-text="Capture d’écran montrant l’onglet services DNS dans le Gestionnaire NSX-T.":::

1. Sélectionnez **Afficher les statistiques**, puis à partir du menu déroulant **Statistiques de zone**, sélectionnez votre Zone FQDN.

   La moitié supérieure affiche les statistiques de l’ensemble du service, tandis que la moitié inférieure affiche les statistiques de la zone spécifiée. Dans cet exemple, vous pouvez voir les requêtes transférées vers les services DNS spécifiés lors de la configuration de la zone de nom de domaine complet.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services-statistics.png" alt-text="Capture d’écran montrant les statistiques du Redirecteur DNS.":::


### <a name="powercli"></a>PowerCLI

L’API de stratégie NSX-T vous permet d’exécuter des commandes nslookup à partir du Service Redirecteur DNS NSX-T. Les applets de commande font partie du module `VMware.VimAutomation.Nsxt` dans PowerCLI. L’exemple suivant illustre la sortie de la version 12.3.0 de ce module.

1. Connexion à votre Serveur NSX-T. 

   >[!TIP]
   >Vous pouvez obtenir l’adresse IP de votre Serveur NSX-T à partir du Portail Azure sous **Gérer** > **Identité**.
 
   ```powershell
   Connect-NsxtServer -Server 10.103.64.3
   ```

1. Obtenez un proxy pour le service nslookup du Redirecteur DNS.

   ```powershell
   $nslookup = Get-NsxtPolicyService -Name com.vmware.nsx_policy.infra.tier_1s.dns_forwarder.nslookup
   ```

1. Effectuez des recherches à partir du Service Redirecteur DNS.

   ```powershell
   $response = $nslookup.get('TNT86-T1', 'vc01.contoso.corp')
   ```

  Le premier paramètre de la commande est l’ID de la passerelle T1 de votre Cloud privé, que vous pouvez obtenir à partir de l’onglet service DNS dans le Portail Azure.

1. Obtenez une réponse brute à partir de la recherche à l’aide des propriétés suivantes de la réponse.

   ```powershell
   $response.dns_answer_per_enforcement_point.raw_answer; (()) DiG 9.10.3-P4-Ubuntu (()) @10.103.64.192 -b 10.103.64.192 vc01.contoso.corp +timeout=5 +tries=3 +nosearch ; (1 server found) ;; global options: +cmd ;; Got answer: ;; -))HEADER((- opcode: QUERY, status: NOERROR, id: 10684 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1  ;; OPT PSEUDOSECTION: ; EDNS: version: 0, flags:; udp: 4096 ;; QUESTION SECTION: ;vc01.contoso.corp.  IN A  ;; ANSWER SECTION: vc01.contoso.corp. 3046 IN A 172.21.90.2  ;; Query time: 0 msec ;; SERVER: 10.103.64.192:53(10.103.64.192) ;; WHEN: Thu Jul 01 23:44:36 UTC 2021 ;; MSG SIZE  rcvd: 62
   ```

   Dans cet exemple, vous pouvez voir une réponse pour la requête de vc01.contoso.corp présentant un enregistrement A avec l’adresse 172.21.90.2. En outre, cet exemple montre une réponse mise en cache du Service Redirecteur DNS. Par conséquent, votre sortie peut varier légèrement.
