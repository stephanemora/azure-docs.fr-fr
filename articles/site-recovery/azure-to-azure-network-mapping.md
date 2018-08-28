---
title: Mappage de réseaux virtuels entre deux régions Azure dans Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: aed804a257376308c668ce0c2f3e8ce652ee9b3f
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42143995"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mapper des réseaux virtuels dans différentes régions Azure


Cet article explique comment mapper deux instances de réseau virtuel Azure dans différentes régions Azure entre elles. Le mappage réseau garantit que, quand une machine virtuelle répliquée est créée dans la région Azure cible, elle est aussi créée sur le réseau virtuel qui est mappé au réseau virtuel de la machine virtuelle source.  

## <a name="prerequisites"></a>Prérequis
Avant de mapper des réseaux, vérifiez que vous avez créé des [réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) dans les régions Azure source et cible.

## <a name="map-virtual-networks"></a>Mappage des réseaux virtuels

Pour mapper un réseau virtuel Azure qui se trouve dans une région Azure (réseau source) à un réseau virtuel qui se trouve dans une autre région (réseau cible), pour les machines virtuelles Azure, accédez à **Infrastructure Site Recovery** > **Mappage de réseaux**. Créez un mappage réseau.

![Fenêtre Mappages réseau - Créer un mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Dans l’exemple suivant, la machine virtuelle est en cours d’exécution dans la région Asie Est. La machine virtuelle est en cours de réplication pour la région Asie Sud-Est.

Pour créer un mappage réseau de la région Asie Est vers la région Asie Sud-Est, sélectionnez l’emplacement du réseau source et l’emplacement du réseau cible. Ensuite, sélectionnez **OK**.

![Ajouter la fenêtre de mappage réseau - Sélectionner les emplacements source et cible pour le réseau source](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Répétez la procédure précédente pour créer un mappage réseau de la région Asie Sud-Est vers la région Asie Est.

![Volet Ajouter mappage réseau - Sélectionner les emplacements source et cible pour le réseau cible](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mapper un réseau lorsque vous activez la réplication

Lorsque vous répliquez une machine virtuelle d’une région Azure vers une autre région pour la première fois, si aucun mappage réseau n’existe, vous pouvez définir le réseau cible lorsque vous configurez la réplication. Site Recovery crée des mappages réseau de la région source à la région cible et de la région cible à la région source, en fonction de ce paramètre.   

![Volet Configurer les paramètres - Choisir l’emplacement cible](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Par défaut, Site Recovery crée dans la région cible un réseau qui est identique au réseau source. Site Recovery crée un réseau ajoutant **-asr** comme suffixe au nom du réseau source. Pour choisir un réseau qui a déjà été créé, sélectionnez **Personnaliser**.

![Personnaliser le volet de paramètres cible - Définir le nom de groupe de ressources cible et le nom de réseau virtuel cible](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Si le mappage réseau est déjà survenu, vous ne pouvez pas changer le réseau virtuel cible lors de l’activation de la réplication. Dans ce cas, pour modifier le réseau virtuel cible, vous devez modifier le mappage réseau existant.  

![Personnaliser le volet de paramètres cible - Définir le nom de groupe de ressources cible](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Modifier le volet de mappage réseau - Modifier un nom de réseau virtuel cible existant](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Si vous modifiez un mappage réseau de la région A à la région B, assurez-vous que vous modifiez également le mappage réseau de la région B à la région A.
>
>


## <a name="subnet-selection"></a>Sélection de sous-réseau
Le sous-réseau de la machine virtuelle cible est sélectionné en fonction du nom du sous-réseau de la machine virtuelle source. S’il existe sur le réseau cible un sous-réseau portant le même nom que la machine virtuelle source, il sera défini pour la machine virtuelle cible. S’il n’y a aucun sous-réseau du même nom sur le réseau cible, le premier sous-réseau dans l’ordre alphabétique est défini comme sous-réseau cible.

Pour modifier le sous-réseau, accédez aux paramètres **Calcul et Réseau** de la machine virtuelle.

![Fenêtre Propriétés de calcul et réseau](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Adresse IP

L’adresse IP de chacune des interfaces réseau de la machine virtuelle cible est définie comme expliqué dans les sections suivantes.

### <a name="dhcp"></a>DHCP
Si l’interface réseau de la machine virtuelle source utilise le protocole DHCP, l’interface réseau de la machine virtuelle cible est également réglée pour utiliser DHCP.

### <a name="static-ip-address"></a>Adresse IP statique
Si l’interface réseau de la machine virtuelle source utilise une adresse IP statique, l’interface réseau de la machine virtuelle cible est également configurée pour utiliser une adresse IP statique. Les sections suivantes décrivent la façon dont une adresse IP statique est définie.

### <a name="ip-assignment-behavior-during-failover"></a>Comportement de l’affectation des adresses IP pendant le basculement
#### <a name="1-same-address-space"></a>1. Même espace d’adressage

Si les sous-réseaux source et cible ont le même espace d’adressage, l’adresse IP de l’interface réseau de la machine virtuelle source est définie comme adresse IP cible. S’il n’existe pas d’adresse IP identique, l’adresse IP suivante est définie comme adresse IP cible.

#### <a name="2-different-address-spaces"></a>2. Espaces d’adressage différents

Si les sous-réseaux source et cible ont un espace d’adressage différent, l’adresse IP suivante disponible sur le sous-réseau cible est définie comme adresse IP cible.


### <a name="ip-assignment-behavior-during-test-failover"></a>Comportement de l’affectation des adresses IP pendant le test de basculement
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. Si le réseau cible choisi est le réseau virtuel de production
- L’adresse IP de récupération (adresse IP cible) sera une adresse IP statique, mais **ce ne sera pas la même adresse IP** que celle réservée pour le basculement.
- L’adresse IP attribuée sera la prochaine adresse IP disponible à partir de la fin de la plage d’adresses du sous-réseau.
- Par exemple, si l’adresse IP statique de la machine virtuelle source est configurée pour être : 10.0.0.19 et si le test de basculement a été tenté avec le réseau de production configuré : ***dr-PROD-nw***, avec une plage de sous-réseau de 10.0.0.0/24. </br>
La machine virtuelle basculée serait affectée avec : la prochaine adresse IP disponible à partir de la fin de la plage d’adresses de sous-réseau, soit : 10.0.0.254 </br>

**Remarque :** le terme **réseau virtuel de production** est appelé « réseau cible » mappé lors de la configuration de la récupération d’urgence.
####<a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. Si le réseau cible choisi n’est pas le réseau virtuel de production, mais dispose de la même plage de sous-réseau en tant que réseau de production 

- L’adresse IP de récupération (adresse IP cible) sera une adresse IP statique avec la **même adresse IP** (c’est-à-dire, l’adresse IP statique configurée) que celle réservée pour le basculement. À condition que la même adresse IP soit disponible.
- Si l’adresse IP statique configurée est déjà affectée à une autre machine virtuelle/périphérique, l’adresse IP de récupération sera la prochaine adresse IP disponible à partir de la fin de la plage d’adresses du sous-réseau.
- Par exemple, si l’adresse IP statique de la machine virtuelle source est configurée pour être : 10.0.0.19 et si le test de basculement a été tenté avec un réseau test : ***dr-NON-PROD-nw***, avec une plage de sous-réseau identique au réseau de production : 10.0.0.0/24. </br>
  L’adresse IP statique suivante serait assignée à la machine virtuelle basculée </br>
    - adresse IP statique configuré : 10.0.0.19 si l’adresse IP est disponible.
    - Prochaine adresse IP disponible : 10.0.0.254 si l’adresse IP 10.0.0.19 est déjà en cours d’utilisation.


Pour modifier l’adresse IP cible sur chacune des interfaces réseau, accédez aux paramètres **Calcul et Réseau** de la machine virtuelle.</br>
À titre de meilleure pratique, il est toujours recommandé de choisir un réseau de test pour effectuer le test de basculement.
## <a name="next-steps"></a>Étapes suivantes

* Vérifiez [Aide à la mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
