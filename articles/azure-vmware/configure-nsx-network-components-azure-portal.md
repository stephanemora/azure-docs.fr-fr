---
title: Configurer des composants réseau NSX dans Azure VMware Solution
description: Découvrez comment utiliser la console Azure VMware Solution pour configurer des segments de réseau NSX-T.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a4702efe8ea26234d074f421d2e6eab871eba5f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733095"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Configurer des composants réseau NSX dans Azure VMware Solution

Un cloud privé Azure VMware Solution est fourni avec NSX-T en tant que réseau à définition logicielle (SDDC) par défaut. Il est préprovisionné avec une passerelle NSX-T de niveau 0 en mode **Actif/Actif** et une passerelle NSX-T de niveau 1 par défaut en mode Actif/En attente.  Ces passerelles vous permettent de connecter les segments (commutateurs logiques) et de fournir une connectivité Est-Ouest et Nord-Sud. 

Une fois le cloud privé Azure VMware Solution déployé, vous pouvez configurer les objets NSX-T nécessaires à partir de la console Azure VMware Solution sous **Réseau des charges de travail**.  La console présente l’affichage simplifié des opérations NSX-T dont un administrateur VMware a besoin quotidiennement et s’adresse aux utilisateurs qui ne sont pas familiarisés avec NSX-T.  

Quatre options s’offrent à vous pour configurer les composants NSX-T dans la console Azure VMware Solution :
- **Segments** : créez des segments qui s’affichent dans NSX-T Manager et vCenter.
- **DHCP** : créez un serveur DHCP ou un relais DHCP si vous envisagez d’utiliser DHCP.
- **Mise en miroir des ports** : créez une mise en miroir des ports pour aider à résoudre les problèmes réseau.
- **DNS** : créez un redirecteur DNS pour envoyer les requêtes DNS à un serveur DNS désigné à des fins de résolution.  

>[!NOTE]
>Vous avez toujours accès à la console NSX-T Manager, où vous pouvez utiliser les paramètres avancés mentionnés et d’autres fonctionnalités NSX-T. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="Capture d’écran montrant quatre options dans la console Azure VMware Solution pour configurer NSX-T.":::

## <a name="prerequisites"></a>Prérequis
Les machines virtuelles créées ou migrées vers le cloud privé Azure VMware Solution doivent être attachées à un segment. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Créer un segment NSX-T dans le portail Azure
Vous pouvez créer et configurer un segment NSX-T à partir de la console Azure VMware Solution dans le portail Azure.  Ces segments sont connectés à la passerelle de niveau 1 par défaut et les charges de travail sur ces segments obtiennent une connectivité Est-Ouest et Nord-Sud. Une fois que vous avez créé le segment, il s’affiche dans NSX-T Manager et vCenter.

>[!NOTE]
>Si vous envisagez d’utiliser le protocole DHCP, vous devez [configurer un serveur DHCP ou un relais DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) avant de pouvoir créer et configurer un segment NSX-T.

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **segments** > **Ajouter**. Fournissez les détails du nouveau segment logique, puis sélectionnez **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Capture d’écran montrant comment ajouter un nouveau segment":::
   
   - **Nom du segment** : nom du commutateur logique qui est visible dans vCenter.
   - **Passerelle de sous-réseau** : adresse IP de la passerelle pour le sous-réseau du commutateur logique avec un masque de sous-réseau. Les machines virtuelles sont attachées à un commutateur logique, et toutes les machines virtuelles qui se connectent à ce commutateur appartiennent au même sous-réseau.  De plus, toutes les machines virtuelles attachées à ce segment logique doivent contenir une adresse IP du même segment.
   - **DHCP** (facultatif) : plages DHCP pour un segment logique. Un [serveur DHCP ou relais DHCP](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) doit être configuré pour consommer DHCP sur des segments.  
   - **Passerelle connectée** - *Sélectionnée par défaut et en lecture seule.*  Passerelle de niveau 1 et type d’informations de segment. 
      - **T1** : nom de la passerelle de niveau 1 dans NSX-T Manager. Un cloud privé Azure VMware Solution est fourni avec une passerelle NSX-T de niveau 0 en mode Actif/Actif et une passerelle NSX-T de niveau 1 par défaut en mode Actif/En attente.  Les segments créés par le biais de la console Azure VMware Solution se connectent uniquement à la passerelle de niveau 1 par défaut, et les charges de travail de ces segments obtiennent une connectivité Est-Ouest et Nord-Sud. Vous pouvez uniquement créer plus de passerelles de niveau 1 par le biais de NSX-T Manager. Les passerelles de niveau 1 créées à partir de la console NSX-T Manager ne sont pas visibles dans la console Azure VMware Solution. 
      - **Type** : segment de superposition pris en charge par Azure VMware Solution.

   Le segment est désormais visible dans la console Azure VMware Solution , NSX-T Manager et vCenter.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Créer un serveur DHCP ou un relais DHCP dans le portail Azure
Vous pouvez créer un serveur ou relais DHCP directement à partir de la console Azure VMware Solution dans le portail Azure. Le serveur ou relais DHCP se connecte à la passerelle de niveau 1, qui est créée quand vous déployez Azure VMware Solution. Tous les segments pour lesquels vous avez donné des plages DHCP feront partie de ce dispositif DHCP.  Une fois que vous avez créé un serveur DHCP ou un relais DHCP, vous devez définir un sous-réseau ou une plage au niveau du segment pour le consommer.

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **DHCP** > **Ajouter**.

2. Sélectionnez **Serveur DHCP** ou **relais DHCP**, puis indiquez un nom pour le serveur ou le relais et trois adresses IP. 

   >[!NOTE]
   >Pour le relais DHCP, une seule adresse IP est nécessaire pour une configuration réussie.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Capture d’écran montrant comment ajouter un serveur DHCP ou un relais DHCP dans Azure VMware Solution":::

4. Terminez la configuration DHCP en [fournissant des plages DHCP sur les segments logiques](#create-an-nsx-t-segment-in-the-azure-portal), puis sélectionnez **OK**.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Configurer la mise en miroir des ports dans le portail Azure
Vous pouvez configurer la mise en miroir des ports pour superviser le trafic réseau qui implique le transfert d’une copie de chaque paquet d’un port de commutateur réseau à un autre. Cette option place un analyseur de protocole sur le port qui reçoit les données mises en miroir. Il analyse le trafic provenant d’une source, d’une machine virtuelle ou d’un groupe de machines virtuelles, puis envoyé à une destination définie. 

Pour configurer la mise en miroir des ports dans la console Azure VMware Solution, procédez comme suit :

* [Étape 1. Créer des machines virtuelles ou des groupes de machines virtuelles source et de destination](#step-1-create-source-and-destination-vms-or-vm-groups) : le groupe source a une machine virtuelle unique ou plusieurs machines virtuelles sur lesquelles le trafic est mis en miroir.

* [Étape 2. Créer un profil de mise en miroir des ports](#step-2-create-a-port-mirroring-profile) : vous définissez la direction du trafic pour les groupes de machines virtuelles source et de destination.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Étape 1. Créer des machines virtuelles ou des groupes de machines virtuelles source et de destination

Au cours de cette étape, vous allez créer un groupe de machines virtuelles source et un groupe de machines virtuelles de destination.

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **Mise en miroir de ports** > **Groupes de machines virtuelles** > **Ajouter**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Capture d’écran montrant comment créer un groupe de machines virtuelles pour la mise en miroir des ports":::

1. Fournissez un nom pour le nouveau groupe de machines virtuelles, sélectionnez les machines virtuelles de votre choix dans la liste, puis sélectionnez **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="Capture d’écran montrant la liste des machines virtuelles à ajouter au groupe de machines virtuelles":::

1. Répétez ces étapes pour créer le groupe de machines virtuelles de destination.

### <a name="step-2-create-a-port-mirroring-profile"></a>Étape 2. Créer un profil de mise en miroir des ports

Au cours de cette étape, vous allez définir un profil pour la direction du trafic des groupes de machines virtuelles source et de destination.

>[!NOTE]
>Assurez-vous que vous avez créé les groupes de machines virtuelles source et de destination.

1. Sélectionnez **Mise en miroir de ports** > **Ajouter**, puis indiquez les informations suivantes :

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Capture d’écran montrant les informations nécessaires pour le profil de la mise en miroir des ports":::

   - **Nom de la mise en miroir des ports** : nom descriptif du profil.
   - **Direction** : sélectionnez Entrée, Sortie ou Bidirectionnel.
   - **Source** : sélectionnez le groupe de machines virtuelles source.
   - **Destination** : sélectionnez le groupe de machines virtuelles de destination.
   - **Description** : entrez la description de la mise en miroir des ports.

1. Sélectionnez **OK** pour terminer le profil. 

   Le profil et les groupes de machines virtuelles sont visibles dans la console Azure VMware Solution.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Configurer un redirecteur DNS dans le portail Azure
Vous allez configurer un redirecteur DNS où des requêtes DNS spécifiques sont transmises à un serveur DNS désigné à des fins de résolution.  Un redirecteur DNS est associé à une **zone DNS par défaut** et à jusqu’à trois **zones FQDN**.

>[!TIP]
>Vous pouvez également utiliser la [console NSX-T Manager pour configurer un redirecteur DNS](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html).

Pour configurer un redirecteur DNS dans la console Azure VMware Solution, procédez comme suit :

* [Étape 1. Configurer une zone DNS par défaut et une zone FQDN](#step-1-configure-a-default-dns-zone-and-fqdn-zone) : quand une requête DNS est reçue, un redirecteur DNS compare le nom de domaine aux noms de domaine dans la zones DNS FQDN. 

* [Étape 2. Configurer le service DNS](#step-2-configure-dns-service) : vous allez configurer le service du redirecteur DNS.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Étape 1. Configurer une zone DNS par défaut et une zone FQDN
Vous allez configurer une zone DNS par défaut et une zone FQDN pour envoyer les requêtes DNS au serveur en amont.  Lors de la réception d’une requête DNS, le redirecteur DNS compare le nom de domaine dans la requête aux noms de domaine des zones DNS FQDN. Si une correspondance est trouvée, la requête est transférée aux serveurs DNS spécifiés dans la zone DNS FQDN. Si aucune correspondance n’est trouvée, la requête est transférée aux serveurs DNS spécifiés dans la zone DNS par défaut.

>[!NOTE]
>Vous devez définir une zone DNS par défaut avant de configurer une zone FQDN. 

1. Dans votre cloud privé Azure VMware Solution, sous **Réseau des charges de travail**, sélectionnez **DNS** > **Zones DNS** > **Ajouter**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="Capture d’écran montrant comment ajouter des zones DNS et un service DNS":::

1. Sélectionnez **Zone DNS par défaut** et indiquez les informations suivantes :

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Capture d’écran montrant comment ajouter une zone DNS par défaut":::

   1. Un nom pour la zone DNS.

   1. Jusqu’à trois adresses IP de serveur DNS au format **8.8.8.8**.

1. Sélectionnez **Zone FQDN** et indiquez les informations suivantes :

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Capture d’écran montrant comment ajouter une zone FQDN":::

   1. Un nom pour la zone DNS.

   1. Le domaine FQDN.

   1. Jusqu’à trois adresses IP de serveur DNS au format **8.8.8.8**.

1. Sélectionnez **OK** pour terminer l’ajout du service DNS et de la zone DNS par défaut.

### <a name="step-2-configure-dns-service"></a>Étape 2. Configurer le service DNS

1. Sélectionnez l’onglet **Service DNS**, puis **Ajouter**. Entrez les détails, puis sélectionnez **OK**.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="Capture d’écran montrant les informations nécessaires pour le service DNS":::

   >[!TIP]
   >L’option **Passerelle de niveau 1** est sélectionnée par défaut et reflète la passerelle créée lors du déploiement d’Azure VMware Solution.

   Le service DNS a été ajouté avec succès.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="Capture d’écran montrant le service DNS ajouté avec succès":::

