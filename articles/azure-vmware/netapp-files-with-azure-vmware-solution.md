---
title: Azure NetApp Files avec Azure VMware Solution
description: Utilisez Azure NetApp Files avec des machines virtuelles Azure VMware Solution pour migrer et synchroniser des données entre des serveurs locaux, des machines virtuelles Azure VMware Solution et des infrastructures cloud.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 69d4e3a99de28d55b2fd95b1fc05c04c2ae0a37b
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988647"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files avec Azure VMware Solution

Dans cet article, nous allons parcourir les étapes de l’intégration d’Azure NetApp Files à des charges de travail Azure VMware Solution. Le système d’exploitation invité s’exécute à l’intérieur des machines virtuelles qui accèdent aux volumes Azure NetApp Files. 

## <a name="azure-netapp-files-overview"></a>Présentation d’Azure NetApp Files

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) est un service Azure pour la migration et l’exécution des charges de travail de fichier d’entreprise les plus exigeantes dans le cloud. Il inclut des bases de données, SAP et des applications informatiques hautes performances, sans modification du code.

### <a name="features"></a>Fonctionnalités
(Services où les Azure NetApp Files sont utilisés.)

- **Connexions Active Directory** : Azure NetApp Files prend en charge [Active Directory Domain Services (ADDS) et Azure Active Directory Domain Services (AADDS)](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use).

- **Protocole de partage** : Azure NetApp Files prend en charge les protocoles SMB (Server Message Block) et NFS (Network File System). Cette prise en charge signifie que les volumes peuvent être montés sur le client Linux et peuvent être mappés sur le client Windows.

- **Azure VMware Solution** : Les partages Azure NetApp Files peuvent être montés à partir de machines virtuelles créées dans l’environnement de solution VMware Azure.

Azure NetApp Files est disponible dans de nombreuses régions Azure et prend en charge la réplication entre les régions. Pour plus d’informations sur les méthodes de configuration d’Azure NetApp Files, consultez [Hiérarchie de stockage d’Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Architecture de référence

Le diagramme suivant illustre une connexion via Azure ExpressRoute à un cloud privé Azure VMware Solution. L’environnement Azure VMware Solution accède au partage Azure NetApp Files qui est monté sur des machines virtuelles Azure VMware Solution.

![Diagramme représentant NetApp Files pour l’architecture Azure VMware Solution.](media/net-app-files/net-app-files-topology.png)

Cet article explique comment configurer, tester et vérifier le volume Azure NetApp Files en tant que partage de fichiers pour les machines virtuelles Azure VMware Solution. Dans ce scénario, nous avons utilisé le protocole NFS. Azure NetApp Files et Azure VMware Solution sont créées dans la même région Azure.

## <a name="prerequisites"></a>Prérequis 

> [!div class="checklist"]
> * Un abonnement Azure avec Azure NetApp Files activé
> * Un sous-réseau pour Azure NetApp Files
> * Une machine virtuelle Linux sur Azure VMware Solution
> * Des machines virtuelles Windows sur Azure VMware Solution

## <a name="regions-supported"></a>Régions prises en charge

Vous trouverez la liste des régions prises en charge à la rubrique [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Vérifier les volumes Azure NetApp Files préconfigurés 

Suivez les instructions pas à pas dans les articles suivants pour créer et monter des volumes Azure NetApp Files sur des machines virtuelles Azure VMware Solution.

- [Créer un compte NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Configurer un pool de capacité](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Créer un volume SMB pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Créer un volume NFS pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Déléguer un sous-réseau à Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

Les étapes suivantes comprennent la vérification des volumes Azure NetApp Files pré-configurés créés dans Azure au niveau du service Azure NetApp Files Premium.

1. Dans le portail Azure, sous **STOCKAGE**, sélectionnez **Azure NetApp Files**. Une liste de vos instances Azure NetApp Files configurées s’affiche. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Capture d’écran montrant la liste des volumes Azure NetApp Files préconfigurés."::: 

2. Sélectionnez un compte de fichiers NetApp configuré pour afficher ses paramètres. Par exemple, sélectionnez **Contoso-anf2**. 

3. Sélectionnez **Pools de capacités** pour vérifier le pool configuré. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Capture d’écran montrant les options permettant d’afficher les pools de capacité et les volumes d’un compte NetApp Files configuré.":::

    La page Pools de capacité s’ouvre et affiche la capacité et le niveau de service. Dans cet exemple, le pool de stockage est configuré avec 4 Tio et un niveau de service Premium.

4. Sélectionnez **Volumes** pour voir les volumes créés sous le pool de capacités. (Voir la capture d’écran précédente.)

5. Sélectionnez un volume pour voir sa configuration.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Capture d’écran montrant les volumes créés sous le pool de capacités.":::

    La fenêtre qui s’ouvre affiche les détails de configuration du volume.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Capture d’écran montrant les détails de configuration d’un volume.":::

    Vous pouvez voir que le volume anfvolume a une taille de 200 Gio et qu’il se trouve dans le pool de capacité anfpool1.  Il est exporté en tant que partage de fichiers NFS via 10.22.3.4:/ANFVOLUME. Une adresse IP privée du réseau virtuel Azure (VNet) a été créée pour Azure NetApp Files et le chemin NFS à monter sur la machine virtuelle. Pour découvrir les performances de volume Azure NetApp Files par taille ou « quota », consultez [Considérations sur les performances pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Vérifier le mappage préconfiguré du partage de la machine virtuelle Azure VMware Solution

Pour rendre un partage Azure NetApp Files accessible à une machine virtuelle Azure VMware Solution, il est important de comprendre le mappage de partage SMB et NFS. Une fois les volumes SMB ou NFS configurés, ils peuvent être montés comme indiqué ici.

- Partage SMB : Créez une connexion Active Directory avant de déployer un volume SMB. Les contrôleurs de domaine spécifiés doivent être accessibles par le sous-réseau délégué d’Azure NetApp Files pour une connexion réussie. Une fois Active Directory configuré dans le compte Azure NetApp Files, il apparaît comme élément sélectionnable lors de la création de volumes SMB.

- Partage NFS : Azure NetApp Files contribue à la création des volumes à l’aide de NFS ou du protocole double (NFS et SMB). La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool. Vous pouvez monter NFS sur le serveur Linux à l’aide des lignes de commande ou des entrées /etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Cas d’usage d’Azure NetApp Files avec Azure VMware Solution

Voici quelques cas d’usage Azure NetApp Files intéressants. 
- Gestion des profils Horizon
- Gestion des profils Citrix
- Gestion des profils Services Bureau à distance
- Partage de fichiers sur Azure VMware Solution

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment intégrer Azure NetApp Files avec vos charges de travail Azure VMware Solution, vous pouvez découvrir ce qui suit :

- [Limites des ressources pour Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Consignes pour planifier un réseau Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Réplication inter-région des volumes Azure NetApp Files](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Questions fréquentes (FAQ) sur Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md)
