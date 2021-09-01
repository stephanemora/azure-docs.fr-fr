---
title: Comment configurer les stratégies de routage d’un hub Virtual Wan
titleSuffix: Azure Virtual WAN
description: Découvrez comment configurer les stratégies de routage du Virtual WAN.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/01/2021
ms.author: wellee
ms.openlocfilehash: d61e6c3847d9ce64f9c3f17da1300b32a1a8e643
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563406"
---
# <a name="how-to-configure-virtual-wan-hub-routing-intent-and-routing-policies"></a>Comment configurer l’intention de routage et les stratégies de routage d’un hub Virtual Wan

>[!NOTE] 
> L’intention de routage de hub est actuellement en préversion publique contrôlée. 
> 
> Cette préversion est fournie sans contrat de niveau de service et elle n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Pour obtenir l’accès à la préversion, contactez previewinterhub@microsoft.com avec l’ID du WAN virtuel, l’ID de l’abonnement et la région Azure dans laquelle vous souhaitez configurer l’intention de routage. Attendez une réponse dans les 48 heures ouvrables (du lundi au vendredi) avec la confirmation de l’activation des fonctionnalités.

## <a name="background"></a>Arrière-plan 

Les clients qui utilisent le gestionnaire de Pare-feu Azure pour configurer des stratégies pour le trafic public et privé peuvent désormais configurer leurs réseaux de manière plus simple à l’aide de stratégies de routage et d’intention de routage.

Les stratégies de routage et d’intention de routage vous permettent de spécifier la manière dont le hub Virtual WAN transfère les appliances virtuelles Internet et privées (de point à site, de site à site, ExpressRoute, appliances virtuelles réseau dans le hub Virtual WAN et réseau virtuel). Il existe deux types de stratégies de routage : les stratégies de routage du trafic Internet et du trafic privé. Chaque hub Virtual WAN peut avoir au plus une stratégie de routage du trafic Internet et une stratégie de routage du trafic privé, chacune avec une ressource de tronçon suivant. 

Alors que le trafic privé comprend des préfixes d’adresses de branche et de réseau virtuel, les stratégies de routage les considèrent comme une entité unique dans les concepts d’intention de routage.

>[!NOTE]
> Dans la préversion publique contrôlée des stratégies de routage du hub Virtual WAN, le trafic entre hubs est inspecté uniquement par le Pare-feu Azure si les hubs Virtual WAN se trouvent dans la même région. 


* **Stratégie de routage du trafic Internet** : lorsqu’une stratégie de routage du trafic Internet est configurée sur un hub de réseau étendu virtuel, toutes les connexions de branche (VPN utilisateur (VPN de point à site), VPN de site à site et ExpressRoute) et les connexions de réseau virtuel à ce hub de réseau étendu virtuel transfèrent le trafic Internet vers la ressource de pare-feu Azure ou au fournisseur tiers spécifié dans la stratégie de routage.
 

* **Stratégie de routage du trafic privé** : Lorsqu’une stratégie de routage du trafic privé est configurée sur un hub Virtual WAN, **tout** le trafic de la branche et du réseau virtuel entrant et sortant du hub Virtual WAN, y compris le trafic entre hubs, sera transmis à la ressource de Pare-feu Azure du tronçon suivant spécifiée dans la stratégie de routage du trafic privé. 

    En d’autres termes, lorsqu’une stratégie de routage du trafic privé est configurée sur le hub Virtual WAN, tous les trafics de branche à branche, de réseau virtuel à branche et inter-hub sont envoyés via le Pare-feu Azure.


## <a name="key-considerations"></a>Considérations importantes
* Vous ne pourrez **pas** activer les stratégies de routage sur vos déploiements avec des tables de routage personnalisées existantes configurées ou s’il existe des routes statiques configurées dans votre table de routage par défaut.
* Actuellement, les stratégies de routage du trafic privé ne sont pas prises en charge dans les hubs avec des connexions ExpressRoute chiffrées (Tunnel VPN de site à site s’exécutant sur une connectivité privée ExpressRoute). 
* Dans la préversion publique contrôlée des stratégies de routage du hub Virtual WAN, le trafic entre hubs est inspecté uniquement par le Pare-feu Azure si les hubs Virtual WAN se trouvent dans la même région.
* L’intention de routage et les stratégies de routage doivent actuellement être configurées via le lien du portail personnalisé fourni à l’étape 3 de **Prérequis**. Les intentions et les stratégies de routage ne sont pas prises en charge via Terraform, PowerShell et l’interface CLI. 

## <a name="prerequisites"></a>Configuration requise
1. Créer un réseau étendu virtuel. Assurez-vous de créer au moins deux hubs virtuels dans la **même** région. Par exemple, vous pouvez créer un Virtual WAN avec deux hubs virtuels dans la région USA Est. 
2. Convertissez votre hub Virtual WAN en hub Virtual WAN sécurisé en déployant un Pare-feu Azure dans les hubs virtuels de la région choisie. Pour plus d’informations sur la conversion de votre hub Virtual WAN en hub virtuel sécurisé, veuillez consulter ce [document](howto-firewall.md).
3. Contactez **previewinterhub@microsoft.com** avec l’**ID de ressource Virtual WAN** et la **région de hub virtuel Azure** dans laquelle vous souhaitez configurer les stratégies de routage. Pour localiser l’ID du WAN virtuel, ouvrez le portail Azure, naviguez jusqu’à votre ressource WAN virtuelle et sélectionnez Paramètres > Propriétés > ID de ressources. Par exemple : 
```
    /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>
```
4.  Une réponse est attendue dans un délai de 24-48 heures avec confirmation de l’activation des fonctionnalités. 
5. Assurez-vous que vos hubs virtuels n’ont **pas** de tables de routage personnalisées ou de routes que vous avez pu ajouter dans defaultRouteTable. Vous ne pourrez **pas** activer les stratégies de routage sur vos déploiements avec des tables de routage personnalisées existantes configurées ou s’il existe des routes statiques configurées dans votre table de routage par défaut. 

## <a name="configuring-routing-policies"></a>Configuration des stratégies de routage
1. À partir du lien du portail personnalisé fourni dans l’e-mail de confirmation de l’étape 3, dans la section **Prérequis**, naviguez vers le hub Virtual WAN sur lequel vous souhaitez configurer les stratégies de routage.
1. Sous Sécurité, sélectionnez **Paramètres du hub virtuel sécurisé**, puis **Gérer les paramètres de fournisseur de sécurité et de routage pour ce hub virtuel sécurisé dans Azure Firewall Manager**
:::image type="content" source="./media/routing-policies/secured-hub-settings.png"alt-text="Capture d’écran montrant comment modifier les paramètres du hub sécurisé"lightbox="./media/routing-policies/secured-hub-settings.png":::.
1. Dans le menu, sélectionnez le hub sur lequel vous souhaitez configurer vos stratégies de routage.
1. Sous **Paramètres**, sélectionnez **Configuration de la sécurité**.
1. Si vous souhaitez configurer une stratégie de routage de trafic Internet, sélectionnez **Pare-feu Azure** ou le fournisseur de sécurité Internet concerné dans la liste déroulante pour **Trafic Internet**. Si ce n’est pas le cas, sélectionnez **Aucun**
1. Si vous souhaitez configurer une stratégie de routage du trafic privé (pour le trafic de la branche et du réseau virtuel) via le Pare-feu Azure, sélectionnez **Pare-feu Azure** dans la liste déroulante pour **Trafic privé**. Sinon, sélectionnez **Contourner le Pare-feu Azure**.

:::image type="content" source="./media/routing-policies/configuring-intents.png"alt-text="Capture d’écran montrant comment configurer des stratégies de routage."lightbox="./media/routing-policies/configuring-intents.png":::

7. Si vous voulez configurer une politique de routage de trafic privé et que des branches ou des réseaux virtuels annoncent des préfixes non IANA RFC1918, sélectionnez **Préfixes de trafic privé** et spécifiez les plages de préfixes non IANA RFC1918 dans la zone de texte qui s’affiche. Sélectionnez **Terminé**. 

:::image type="content" source="./media/routing-policies/private-prefixes.png"alt-text="Capture d’écran montrant comment modifier les préfixes de trafic privé."lightbox="./media/routing-policies/private-prefixes.png":::

8. Réglez **Interhub** sur **Activé**. L’activation de cette option garantit que vos stratégies de routage sont appliquées à l’intention de routage de ce hub Virtual WAN. 
9. Sélectionnez **Enregistrer**. Cette opération prend environ 10 minutes. 
10. Répétez les étapes 2-8 pour les autres hubs Virtual WAN sécurisés pour lesquels vous souhaitez configurer des stratégies de routage. 
 
## <a name="routing-policy-configuration-examples"></a>Exemples de configuration de stratégie de routage 

La section suivante décrit deux scénarios courants qui permettent aux clients d’appliquer des stratégies de routage à des hubs Virtual WAN sécurisés.

### <a name="all-virtual-wan-hubs-are-secured-deployed-with-azure-firewall"></a>Tous les hubs Virtual WAN sont sécurisés (déployés avec le Pare-feu Azure)

Dans ce scénario, tous les hubs Virtual WAN sont déployés avec un Pare-feu Azure. Dans ce scénario, vous pouvez configurer une stratégie de routage du trafic Internet, une stratégie de routage du trafic privé ou les deux sur chaque hub Virtual WAN. 

:::image type="content" source="./media/routing-policies/two-secured-hubs-diagram.png"alt-text="Capture d’écran montrant l’architecture avec deux hubs sécurisés."lightbox="./media/routing-policies/two-secured-hubs-diagram.png":::

Considérez la configuration suivante, où les hubs 1 et 2 ont des stratégies de routage pour le trafic privé et Internet. 

**Configuration du Hub 1 :**
* Stratégie de trafic privé avec tronçon suivant Hub 2 Pare-feu Azure
* Stratégie de trafic Internet avec tronçon suivant Hub 1 Pare-feu Azure 

**Configuration de Hub 2 :**
* Stratégie de trafic privé avec tronçon suivant Hub 2 Pare-feu Azure
* Stratégie de trafic Internet avec tronçon suivant Hub 2 Pare-feu Azure 

Voici les flux de trafic qui résultent d’une telle configuration. 

> [!NOTE]
> Le trafic Internet doit émettre une sortie via le Pare-feu Azure **local**, car la route par défaut (0.0.0.0.0/0) ne se propage **pas** à travers les hubs.

| Du |   À |  Réseaux virtuels Hub 1 | Branches Hub 1 | Réseaux virtuels Hub 2 | Branches Hub 2| Internet|
| -------------- | -------- | ---------- | ---| ---| ---| ---|
| Réseaux virtuels Hub 1     | &#8594;| Hub 1 AzFW |   Hub 1 AzFW    | Hub 1 et 2 AzFW | Hub 1 et 2 AzFW | Hub 1 AzFW |
| Branches Hub 1   | &#8594;|  Hub 1 AzFW  |   Hub 1 AzFW    | Hub 1 et 2 AzFW | Hub 1 et 2 AzFW | Hub 1 AzFW|
| Réseaux virtuels Hub 2     | &#8594;| Hub 1 et 2 AzFW |   Hub 1 et 2 AzFW    | Hub 2 AzFW  | Hub 2 AzFW | Hub 2 AzFW|
| Branches Hub 2   | &#8594;|   Hub 1 et 2 AzFW  |    Hub 1 et 2 AzFW    | Hub 2 AzFW |  Hub 2 AzFW | Hub 2 AzFW|


### <a name="mixture-of-secured-and-regular-virtual-wan-hubs"></a>Mélange de hubs Virtual WAN sécurisés et ordinaires 

Dans ce scénario, tous les hubs Virtual WAN ne sont pas déployés avec un Pare-feu Azure. Dans ce scénario, vous pouvez configurer une stratégie de routage du trafic Internet, une stratégie de routage du trafic privé sur les hubs Virtual WAN sécurisés. 

Considérez la configuration suivante, où Hub 1 (normal) et Hub 2 (sécurisé) sont déployés dans un Virtual WAN. Hub 2 a des stratégies de routage pour le trafic privé et Internet. 

**Configuration du Hub 1 :**
* N/A (impossible de configurer des stratégies de routage si le hub n’est pas déployé avec le Pare-feu Azure)

**Configuration de Hub 2 :**
* Stratégie de trafic privé avec tronçon suivant Hub 2 Pare-feu Azure
* Stratégie de trafic Internet avec tronçon suivant Hub 2 Pare-feu Azure 


:::image type="content" source="./media/routing-policies/one-secured-one-normal-diagram.png"alt-text="Capture d’écran montrant l’architecture avec un hub sécurisé un hub normal."lightbox="./media/routing-policies/one-secured-one-normal-diagram.png":::


 Voici les flux de trafic qui résultent d’une telle configuration. Les branches et les réseaux virtuels connectés au Hub 1 **ne peuvent pas** accéder à Internet via le Pare-feu Azure dans le hub, car la route par défaut (0.0.0.0/0) ne se propage **pas** à travers les hubs.

| Du |   À |  Réseaux virtuels Hub 1 | Branches Hub 1 | Réseaux virtuels Hub 2 | Branches Hub 2| Internet |
| -------------- | -------- | ---------- | ---| ---| ---| --- |
| Réseaux virtuels Hub 1     | &#8594;| Direct |   Direct   | Hub 2 AzFW | Hub 2 AzFW | - |
| Branches Hub 1   | &#8594;|  Direct |   Direct    | Hub 2, AzFW | Hub 2 AzFW | - |
| Réseaux virtuels Hub 2     | &#8594;| Hub 2 AzFW |   Hub 2 AzFW    | Hub 2 AzFW  | Hub 2 AzFW | Hub 2 AzFW|
| Branches Hub 2   | &#8594;|   Hub 2 AzFW  |    Hub 2 AzFW    | Hub 2 AzFW |  Hub 2 AzFW | Hub 2 AzFW|


## <a name="troubleshooting"></a>Résolution des problèmes

La section suivante décrit les problèmes courants rencontrés lors de la configuration des stratégies de routage sur votre hub Virtual WAN.  Lisez les sections ci-dessous et si votre problème n’est toujours pas résolu, et contactez previewinterhub@microsoft.com pour le support. Attendez-vous à une réponse dans les 48 heures ouvrables (du lundi au vendredi). 

### <a name="troubleshooting-configuration-issues"></a>Dépannage des problèmes de configuration
*  Assurez-vous que vous avez reçu la confirmation de previewinterhub@microsoft.com que l’accès à la préversion publique fermée a été accordé à votre abonnement et à la région choisie. Vous ne pourrez **pas** configurer les stratégies de routage sans avoir accès à la préversion.
* Après avoir activé la fonction de stratégie de routage sur votre déploiement, assurez-vous d’utiliser **uniquement** le lien du portail personnalisé fourni dans le cadre de votre e-mail de confirmation. N’utilisez pas d’appels d’API PowerShell, CLI ou REST pour gérer vos déploiements Virtual WAN.  Cela comprend la création de connexions de branche (VPN de site à site, VPN de point à site ou ExpressRoute). 

>[!NOTE]
> Si vous utilisez Terraform, les stratégies de routage ne sont pas prises en charge pour le moment.

*  Assurez-vous que vos hubs virtuels n’ont pas de tables de routage personnalisées ou d’itinéraires statiques dans DefaultRouteTable. Vous ne pourrez **pas** sélectionner **Activer interhub** dans le gestionnaire de pare-feu sur votre hub Virtual WAN si des tables de routage personnalisées sont configurées ou s’il y a des routes statiques dans votre defaultRouteTable. 

### <a name="troubleshooting-data-path"></a>Résolution des problèmes de chemin de données 

* Actuellement, l’utilisation du Pare-feu Azure pour inspecter le trafic entre hubs est uniquement disponible pour les hubs Virtual WAN déployés dans la **même** région Azure. 
* Actuellement, les stratégies de routage du trafic privé ne sont pas prises en charge dans les hubs avec des connexions ExpressRoute chiffrées (Tunnel VPN de site à site s’exécutant sur une connectivité privée ExpressRoute). 
* Vous pouvez vérifier que les stratégies de routage ont été appliquées correctement en vérifiant les itinéraires effectifs de DefaultRouteTable. Si des stratégies de routage privées sont configurées, vous devez voir les itinéraires dans DefaultRouteTable pour les préfixes de trafic privé avec le Pare-feu Azure de tronçon suivant. Si les stratégies de routage du trafic Internet sont configurées, vous devez voir un itinéraire par défaut (0.0.0.0/0) dans DefaultRouteTable avec le Pare-feu Azure de tronçon suivant.

### <a name="troubleshooting-azure-firewall"></a>Résolution des problèmes liés au Pare-feu Azure

* Si vous utilisez des préfixes non [IANA RFC1918](https://datatracker.ietf.org/doc/html/rfc1918) dans vos branches/réseaux virtuels, assurez-vous de spécifier ces préfixes dans la zone de texte « Préfixes privés » dans le gestionnaire de pare-feu.
* Si vous avez spécifié des adresses non RFC1918 dans la zone de texte **Préfixes de trafic privés** du gestionnaire de pare-feu, vous devrez peut-être configurer des stratégies SNAT sur votre pare-feu pour désactiver SNAT pour le trafic privé non RFC1918. Pour plus d’informations, reportez-vous au [document](../firewall/snat-private-range.md) suivant. 
* Envisagez de configurer et d’afficher les journaux du Pare-feu Azure pour vous aider à dépanner et analyser le trafic réseau. Pour plus d’informations sur la configuration de la surveillance du Pare-feu Azure, consultez le [document](../firewall/firewall-diagnostics.md) suivant. Une présentation des différents types de journaux de pare-feu se trouve [ici](../firewall/logs-and-metrics.md).
* Pour plus d’informations sur le Pare-feu Azure, consultez la [documentation du Pare-feu Azure](../firewall/overview.md).



## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="why-cant-i-edit-the-defaultroutetable-from-the-custom-portal-link-provided-by-previewinterhubmicrosoftcom"></a>Pourquoi ne puis-je pas modifier defaultRouteTable à partir du lien du portail personnalisé fourni par previewinterhub@microsoft.com ?

Dans le cadre de la préversion publique contrôlée des stratégies de routage, le routage de votre hub Virtual WAN est entièrement managé par le gestionnaire de Pare-feu. En outre, la préversion managée des stratégies de routage n’est **pas** prise en charge avec le routage personnalisé. Le routage personnalisé avec les stratégies de routage sera pris en charge à une date ultérieure. 

Cependant, vous pouvez toujours visualiser les itinéraires effectifs de DefaultRouteTable en accédant à l’onglet **Itinéraires effectifs**.

### <a name="can-i-configure-a-routing-policy-for-private-traffic-and-also-send-internet-traffic-00000-via-a-network-virtual-appliance-in-a-spoke-virtual-network"></a>Puis-je configurer une stratégie de routage pour le trafic privé et également envoyer le trafic Internet (0.0.0.0/0) via une appliance virtuelle réseau dans un réseau virtuel Spoke ?

Ce scénario n’est pas pris en charge dans la préversion publique contrôlée. Cependant, contactez previewinterhub@microsoft.com pour exprimer votre intérêt à mettre en œuvre ce scénario. 

### <a name="does-the-default-route-00000-propagate-across-hubs"></a>L’itinéraire par défaut (0.0.0.0/0) se propage-t-il entre les hubs ?

Non. Actuellement, les branches et les réseaux virtuels sont émis en sortie sur Internet à l’aide d’un Pare-feu Azure déployé à l’intérieur du hub Virtual WAN auquel les branches et les réseaux virtuels sont connectés. Vous ne pouvez pas configurer une connexion pour accéder à Internet via le Pare-feu d’un hub distant.

### <a name="why-do-i-see-rfc1918-prefixes-advertised-to-my-on-premises-devices"></a>Pourquoi les préfixes RFC1918 publiés sur mes appareils locaux sont-ils affichés ?

Lorsque des stratégies de routage de trafic privé sont configurées, les passerelles Virtual WAN annoncent automatiquement les itinéraires statiques qui se trouvent dans la table de routage par défaut (préfixes RFC1918:10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) en plus des préfixes de branche et de réseau virtuel explicites.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
