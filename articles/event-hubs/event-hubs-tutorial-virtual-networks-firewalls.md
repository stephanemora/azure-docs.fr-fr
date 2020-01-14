---
title: Azure Event Hubs - Activer l’intégration des réseaux virtuels et les pare-feu
description: Dans ce tutoriel, vous allez apprendre à intégrer Event Hubs avec des réseaux virtuels et des pare-feux pour permettre un accès sécurisé.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 12/20/2019
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: f911a1513c6f89180ea51cc0de96dc8a475c7fc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437117"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Tutoriel : Activer l’intégration des réseaux virtuels et des pare-feu sur l’espace de noms Event Hubs

Les [points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) étendent votre espace d’adressage privé de réseau virtuel et l’identité de votre réseau virtuel aux services Azure, par le biais d’une connexion directe. Les points de terminaison permettent de sécuriser vos ressources critiques du service Azure pour vos réseaux virtuels uniquement. Le trafic à partir de votre réseau virtuel vers le service Azure reste toujours sur le réseau principal de Microsoft Azure.

Les pare-feu vous permettent de limiter l’accès à l’espace de noms Event Hubs à partir d’adresses IP (ou de plages d’adresses IP) spécifiques.

Ce tutoriel montre comment intégrer des points de terminaison de service de réseaux virtuels et configurer des pare-feu (filtrage IP) avec votre espace de noms Azure Event Hubs existant, à l’aide du portail.

Dans ce tutoriel, vous allez découvrir comment :
> [!div class="checklist"]
> * Intégrer des points de terminaison de service de réseaux virtuels à votre espace de noms Event Hubs.
> * Configurer le pare-feu (filtrage IP) avec votre espace de noms Event Hubs.

>[!WARNING]
> L’implémentation de l’intégration de réseaux virtuels peut empêcher d’autres services Azure d’interagir avec Event Hubs.
>
> Les intégrations internes ne sont pas prises en charge quand les réseaux virtuels sont activés.
> Scénarios Azure courants qui ne fonctionnent pas avec les réseaux virtuels -
> * Diagnostics Azure et journalisation
> * Azure Stream Analytics
> * Intégration Event Grid
> * Web Apps et Functions doivent être sur un réseau virtuel.
> * Routes IoT Hub
> * Explorateur d’appareils IoT


> [!IMPORTANT]
> Les réseaux virtuels sont pris en charge dans les niveaux **standard** et **dédié** d’Event Hubs. Il ne sont pas pris en charge dans le niveau de base.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit][] avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Nous utiliserons un espace de noms Event Hubs existant. Vérifiez donc que vous en avez un à votre disposition. Si ce n’est pas le cas, consultez [ce tutoriel](./event-hubs-create.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Tout d’abord, accédez au [portail Azure][Azure portal] et connectez-vous à l’aide de votre abonnement Azure.

## <a name="select-event-hubs-namespace"></a>Sélectionner un espace de noms Event Hubs

Pour les besoins de ce tutoriel, nous avons créé un espace de noms Event Hubs et nous allons y accéder.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Accéder à l’expérience Pare-feux et réseaux virtuels

Utilisez le menu de navigation dans le volet gauche du portail pour choisir l’option **Pare-feux et réseaux virtuels**.

  ![Accéder au menu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  La première fois que vous accédez à cette page, la case d’option **Tous les réseaux** doit être sélectionnée. Cela implique que l’espace de noms Event Hubs autorise toutes les connexions entrantes.

## <a name="add-virtual-network-service-endpoint"></a>Ajouter un point de terminaison de service de réseau virtuel

Pour limiter l’accès, vous devez intégrer le point de terminaison de service de réseau virtuel pour cet espace de noms Event Hubs.

1. Cliquez sur la case d’option **Réseaux sélectionnés** en haut de la page pour activer le reste de la page avec les options de menu.
  ![réseaux sélectionnés](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Dans la section Réseau virtuel de la page, sélectionnez l’option permettant d’***+Ajouter un réseau virtuel existant***. Un volet s’affiche pour vous permettre de sélectionner un réseau virtuel déjà créé.
  ![ajouter un réseau virtuel existant](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Sélectionnez le réseau virtuel dans la liste et choisissez le sous-réseau.
   ![sélectionner un sous-réseau](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Vous devez activer le point de terminaison de service avant d’ajouter le réseau virtuel à la liste. Si le point de terminaison de service n’est pas activé, le portail vous invite à l’activer.
  ![sélectionner un sous-réseau et activer le point de terminaison](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Si vous ne pouvez pas activer le point de terminaison de service, vous pouvez ignorer le point de terminaison de service de réseau virtuel manquant en utilisant le modèle ARM. Cette fonctionnalité n’est pas disponible dans le portail.

5. Après avoir activé le point de terminaison de service sur le sous-réseau sélectionné, vous pouvez l’ajouter à la liste des réseaux virtuels autorisés.
  ![ajout du sous-réseau après l’activation du point de terminaison](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Cliquez sur le bouton **Enregistrer** sur le ruban supérieur pour enregistrer la configuration de réseau virtuel sur le service. Veuillez patienter quelques minutes jusqu’à ce que la confirmation s’affiche dans la zone de notifications du portail.

## <a name="add-firewall-for-specified-ip"></a>Ajouter un pare-feu pour l’adresse IP spécifiée

Nous pouvons limiter l’accès à l’espace de noms Event Hubs pour une plage limitée d’adresses IP ou pour une adresse IP spécifique à l’aide de règles de pare-feu.

1. Cliquez sur la case d’option **Réseaux sélectionnés** en haut de la page pour activer le reste de la page avec les options de menu.
  ![réseaux sélectionnés](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Dans la section **Pare-feu** sous la grille ***Plage d’adresses***, vous pouvez ajouter une ou plusieurs adresses IP ou plages d’adresses IP spécifiques.
  ![ajouter des adresses IP](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Après avoir ajouté les adresses IP (ou les plages d’adresses IP), cliquez sur **Enregistrer** sur le ruban supérieur pour être sûr que la configuration est enregistrée du côté du service. Veuillez patienter quelques minutes jusqu’à ce que la confirmation s’affiche dans la zone de notifications du portail.
  ![ajouter des adresses ip puis cliquer sur enregistrer](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Ajout de votre adresse IP actuelle aux règles de pare-feu

1. Vous pouvez également ajouter rapidement votre adresse IP actuelle en cochant la case ***Ajouter l’adresse IP de votre client (VOTRE ADRESSE IP ACTUELLE)*** juste au-dessus de la grille ***Plage d’adresses***.
  ![Ajout d’adresse IP actuelle](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Une fois que vous avez ajouté votre adresse IP actuelle aux règles de pare-feu, cliquez sur **Enregistrer** sur le ruban supérieur pour être sûr que la configuration est enregistrée du côté du service. Veuillez patienter quelques minutes jusqu’à ce que la confirmation s’affiche dans la zone de notifications du portail.
  ![ajouter l’adresse IP actuelle, puis cliquer sur enregistrer](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Conclusion

Dans ce tutoriel, vous avez intégré des points de terminaison de réseau virtuel et des règles de pare-feu avec un espace de noms Event Hubs. Vous avez découvert comment :
> [!div class="checklist"]
> * Intégrer des points de terminaison de service de réseaux virtuels à votre espace de noms Event Hubs.
> * Configurer le pare-feu (filtrage IP) avec votre espace de noms Event Hubs.


[Azure portal]: https://portal.azure.com/
