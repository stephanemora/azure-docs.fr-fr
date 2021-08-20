---
title: À propos d’Azure Network Function Manager
description: En savoir plus sur Gestionnaire de fonctions réseau Azure, un service d’orchestration natif cloud complètement managé qui vous permet de déployer et de configurer des fonctions réseau sur Azure Stack Edge Pro avec GPU pour une expérience hybride cohérente à l’aide du Portail Azure.
author: cherylmc
ms.service: network-function-manager
ms.topic: overview
ms.date: 06/16/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: df063a5b19e9287e1275683719d2d41a13200b5e
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128927"
---
# <a name="what-is-azure-network-function-manager-preview"></a>Présentation d’Azure Network Function Manager (Préversion)


Le Gestionnaire de fonctions réseau offre une expérience [Place de marché Azure](https://azure.microsoft.com/marketplace/) pour déployer des fonctions réseau telles que le noyau de paquets mobiles, la périphérie SD-WAN et les services VPN sur votre appareil [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) fonctionnant dans votre environnement local. Vous pouvez maintenant déployer rapidement un service réseau mobile privé ou une solution SD-WAN sur votre périphérique directement à partir du Portail de gestion Azure. Le Gestionnaire de fonctions réseau apporte des fonctions réseau à partir d’un écosystème de [partenaires](#partners) en pleine croissance. Cette préversion est prise en charge sur [Azure Stack Edge Pro avec GPU](../databox-online/azure-stack-edge-gpu-overview.md). 

## <a name="preview-features"></a><a name="preview"></a>Fonctionnalités en préversion

* **Expérience de gestion cohérente :** le Gestionnaire de fonctions réseau offre une expérience de gestion Azure cohérente pour les fonctions de réseau de différents partenaires déployés sur la périphérie de votre entreprise. Cela vous permet de simplifier la gouvernance et la gestion. Vous pouvez utiliser vos outils et votre kit de développement logiciel (SDK) Azure familiers pour automatiser le déploiement de fonctions réseau par le biais de modèles déclaratifs. Vous pouvez également appliquer le contrôle d’accès en fonction du rôle [Azure RBAC](../role-based-access-control/overview.md) pour un déploiement global de fonctions réseau sur vos périphériques Azure Stack Edge.

* **Expérience de la Place de marché Azure pour les fonctions de réseau 5G :** accélérez le déploiement de la solution de réseau mobile privé sur votre appareil Azure Stack Edge en sélectionnant l’une des fonctions de réseau de base de paquets mobile LTE et 5G directement à partir de la Place de marché Azure.

* **Une expérience cloud-à-périphérie transparente pour les solutions de réseau privé virtuel SD-WAN et VPN :** le Gestionnaire de fonctions réseau étend l’expérience de gestion Azure pour les fonctions de réseau de la Place de marché que vous connaissez dans le cloud public sur votre périphérique. Cela vous permet de tirer parti d’une expérience de déploiement cohérente pour votre choix de fonctions réseau de partenaires SD-WAN et VPN déployées dans le cloud public Azure ou l’appareil Azure Stack Edge.

* **Expérience des applications managées Azure pour les fonctions réseau sur la périphérie de l’entreprise :** le Gestionnaire de fonctions réseau offre une expérience de déploiement simplifiée pour les fonctions réseau spécialisées, telles que le noyau de paquets mobiles, sur votre appareil Azure Stack Edge. Nous avons prévalidé la gestion du cycle de vie des images de fonctions réseau approuvées par les partenaires. Vous pouvez avoir la certitude que vos ressources de fonctions réseau sont déployées dans un état cohérent sur l’ensemble de votre flotte. Pour plus d’informations, consultez [Applications managées Azure](../azure-resource-manager/managed-applications/overview.md).

* **Accélération réseau et choix de l’allocation d’adresses IP dynamiques ou statiques pour les fonctions réseau :** le Gestionnaire de fonctions réseau et [Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) prennent en charge des performances réseau améliorées pour les charges de travail de fonction réseau. Les fonctions réseau spécialisées, telles que le noyau de paquets mobiles, peuvent désormais être déployées sur l’appareil Azure Stack Edge avec des performances de chemin de données plus rapides sur le réseau de point d’accès et le réseau de plan utilisateur. Vous pouvez également choisir d’allouer des adresses IP statiques ou dynamiques pour différentes interfaces virtuelles pour le déploiement de vos fonctions réseau. Vérifiez auprès de votre partenaire de fonctions réseau la prise en charge de ces fonctionnalités de mise en réseau.  

## <a name="azure-managed-applications-for-network-functions"></a><a name="managed"></a>Applications managées Azure pour les fonctions réseau

Les fonctions réseau disponibles pour être déployées à l’aide du Gestionnaire de fonctions réseau sont conçues pour s’exécuter spécifiquement sur votre appareil Azure Stack Edge. L’offre de fonction réseau est publiée sur la Place de marché Azure en tant qu'[application managée Azure](../azure-resource-manager/managed-applications/overview.md). Les clients peuvent déployer l’offre directement à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/) ou à partir de la ressource d’appareil du Gestionnaire de fonctions réseau via le Portail Azure. 

:::image type="content" source="./media/overview/managed-app-workflow.png" alt-text="Diagramme du flux de travail de l’application managée.":::

Toutes les offres de fonctions réseau disponibles pour être déployées à l’aide du Gestionnaire de fonctions réseau ont une application managée disponible dans la Place de marché Azure. Les applications managées permettent aux partenaires d’effectuer les opérations suivantes :

* Générer une expérience de déploiement personnalisée pour leur fonction réseau avec l’expérience Portail Azure. 

* Fournir un modèle Resource Manager spécialisé qui leur permet de créer la fonction réseau directement dans le périphérique Azure Stack Edge. 

* Facturer des frais de licence logicielle directement ou via Place de marché Azure. 

* Exposer des propriétés personnalisées et des compteurs de ressources. 

Les partenaires des fonctions réseau peuvent créer des ressources différentes, en fonction du déploiement de leur appliance, des licences de configuration et des besoins de gestion. Comme c’est le cas avec toutes les applications managées Azure, lorsqu’un client crée une fonction réseau dans l’appareil Azure Stack Edge, deux groupes de ressources sont créés dans leur abonnement : 

* **Groupe de ressources du client** : ce groupe de ressources contient un espace réservé d’applications pour l’application managée. Les partenaires peuvent l’utiliser pour exposer les propriétés du client qu’ils choisissent ici. 

* **Groupe de ressources managées** : les clients ne peuvent pas configurer ni modifier directement les ressources de ce groupe de ressources, car celui-ci est contrôlé par l’éditeur de l’application managée. Ce groupe de ressources contient la ressource **fonctions réseau**  du Gestionnaire de fonctions réseau.

:::image type="content" source="./media/overview/managed-app-resource-groups.png" alt-text="Diagramme des groupes de ressources des applications managées.":::

## <a name="network-function-configuration-process"></a><a name="configuration"></a>Processus de configuration des fonctions réseau 

Les partenaires de fonctions réseau qui proposent leurs applications managées Azure avec le Gestionnaire de fonctions réseau fournissent une expérience qui configure automatiquement la fonction réseau dans le cadre du processus de déploiement. Une fois le déploiement de l’application managée réussi et l’instance de la fonction réseau configurée dans Azure Stack Edge, toute configuration supplémentaire qui peut être requise pour la fonction réseau doit être effectuée via le Portail de gestion des partenaires de la fonction réseau. Contactez votre partenaire de fonction réseau pour obtenir une expérience de gestion de bout en bout des fonctions réseau déployées sur l’appareil Azure Stack Edge. 

## <a name="prerequisites"></a><a name="prereq"></a>Configuration requise

### <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>Azure Stack Pro Edge avec GPU installé et activé

Le service Gestionnaire de fonctions réseau Azure est activé sur l’appareil Azure Stack Edge Pro. Avant de déployer des fonctions réseau, vérifiez que Azure Stack Edge Pro est installé et activé. La ressource Azure Stack Edge doit être déployée dans une région prise en charge par des ressources de Gestionnaire de fonctions réseau. Pour plus d’informations, consultez [Disponibilité des régions](#regions). Veillez à suivre toutes les étapes des [Tutoriels[ et ](../databox-online/azure-stack-edge-gpu-deploy-checklist.md)Guides de démarrage rapide](../databox-online/azure-stack-edge-gpu-quickstart.md) Azure Stack Edge Pro.

Vous devez également vérifier que l'**État** de l’appareil, situé dans la section Propriétés de la ressource Azure Stack Edge dans le Portail de gestion Azure, est **En ligne**.

:::image type="content" source="./media/overview/properties.png" alt-text="Capture d’écran des propriétés." lightbox="./media/overview/properties.png":::

### <a name="partner-prerequisites"></a><a name="partner-prereq"></a>Conditions préalables pour les partenaires 

Les clients peuvent choisir parmi un ou plusieurs [partenaires](#partners) Gestionnaire de fonctions réseau pour déployer leur fonction réseau sur un appareil Azure Stack Edge. Chaque partenaire a des exigences de mise en réseau pour le déploiement de sa fonction réseau sur un appareil Azure Stack Edge. Reportez-vous à la documentation du produit à partir des partenaires de fonctions réseau pour effectuer les tâches de configuration suivantes :

* [Configurer le réseau sur des ports différents](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
* [Activer le réseau de calcul sur votre appareil Azure Stack Edge](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network). 


### <a name="azure-account"></a><a name="account"></a>Compte Azure

Le service Gestionnaire de fonctions réseau Azure se compose des ressources **Appareil** du Gestionnaire de fonctions réseau et **Fonction réseau** du Gestionnaire de fonctions réseau. Les ressources Appareil et Fonction réseau se trouvent dans les abonnements Azure. L’ID de l’abonnement utilisé pour activer l’appareil Azure Stack Edge Pro et les ressources du Gestionnaire de fonctions réseau doivent être les mêmes. 

Intégrez votre ID d’abonnement Azure pour la préversion du Gestionnaire de fonctions réseau en complétant le [formulaire de préversion du Gestionnaire de fonctions Azure](https://go.microsoft.com/fwlink/?linkid=2163583). Pour la préversion, les partenaires du Gestionnaire de fonctions réseau Azure doivent activer le même ID d’abonnement Azure pour déployer leur fonction réseau à partir de la Place de marché Azure. Assurez-vous que votre ID d’abonnement Azure est intégré pour la préversion dans les deux emplacements. 

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>Enregistrement et autorisations du fournisseur de ressources

Les ressources du Gestionnaire de fonctions réseau Azure se trouvent dans le fournisseur de ressources Microsoft.HybridNetwork. Une fois l’ID d’abonnement Azure intégré à la préversion avec le service Gestionnaire de fonctions réseau, inscrivez l’ID d’abonnement auprès du fournisseur de ressources Microsoft.HybridNetwork. Pour plus d’informations sur les fournisseurs de ressources, consultez [Fournisseurs et types de ressources Azure](../azure-resource-manager/management/resource-providers-and-types.md).

Les comptes que vous utilisez pour créer la ressource d’appareil du Gestionnaire de fonctions réseau doivent être affectés à un rôle personnalisé auquel sont affectés les actions nécessaires à partir de la table suivante. Pour plus d’informations, consultez [Rôles personnalisées](../role-based-access-control/custom-roles.md).

| Nom | Action|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|Obligatoire pour lire la ressource Azure Stack Edge sur laquelle les fonctions réseau seront déployées. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Obligatoire pour lire la section des propriétés de la ressource Azure Stack Edge. |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Obligatoire pour créer la ressource du périphérique Gestionnaire de fonctions réseau sur la ressource Azure Stack Edge.|
| Microsoft.HybridNetwork/devices/* | Obligatoire pour créer, mettre à jour, supprimer la ressource du périphérique Gestionnaire de fonctions réseau. |

Les comptes que vous utilisez pour créer la ressource des applications managées doivent être affectés à un [rôle personnalisé](../role-based-access-control/custom-roles.md) auquel sont affectés les actions nécessaires à partir de la table suivante : 

|Nom |Action |
|---|---|
|[Rôle Contributeur d'application managée](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|Obligatoire pour créer des ressources d’applications managées.|

## <a name="managed-identity"></a><a name="managed-identity"></a>Identité managée 

Les partenaires de fonctions réseau qui proposent leurs applications managées Azure avec le Gestionnaire de fonctions réseau offrent une expérience qui vous permet de déployer une application managée associée à une ressource d’appareil du Gestionnaire de fonctions réseau existante. Lorsque vous déployez l’application managée par le partenaire dans le Portail Azure, vous devez fournir une ressource d’identité gérée affectée par l’utilisateur Azure qui a accès à la ressource d’appareil du Gestionnaire de fonctions réseau. L’identité managée affectée par l'utilisateur permet au fournisseur de ressources de l’application managée et au serveur de publication de la fonction réseau d’avoir les autorisations appropriées pour la ressource d’appareil du Gestionnaire de fonctions réseau déployée en dehors du groupe de ressources managées. Pour plus d’informations, consultez [Gérer une identité managée affectée par l’utilisateur dans le Portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Pour créer une identité managée affectée par l’utilisateur pour le déploiement de fonctions réseau :

1. Créez une identité managée affectée par l’utilisateur et affectez-la à un rôle personnalisé avec des autorisations pour Microsoft.HybridNetwork/devices/join/action. Pour plus d’informations, consultez [Gérer une identité managée affectée par l’utilisateur dans le Portail Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Fournissez cette identité managée lors de la création de l’application managée d’un partenaire dans le Portail Azure. Pour plus d’informations, consultez [Attribuer à une identité managée un accès à une ressource à l’aide du Portail Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).


## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>Exigences relatives aux ports et règles de pare-feu

Les services NFM (Gestionnaire de fonctions réseau) exécutés sur Azure Stack Edge nécessitent une connectivité sortante vers le service cloud NFM pour le trafic de gestion afin de déployer des fonctions réseau. NFM est entièrement intégré au service Azure Stack Edge. Analysez les exigences relatives aux ports réseau et aux règles de pare-feu pour l’appareil [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements).  

Les partenaires de fonction réseau auront des exigences différentes pour les règles de configuration de port et de pare-feu pour gérer le trafic vers le Portail de gestion des partenaires. Vérifiez auprès de votre partenaire de fonctions réseau des exigences spécifiques.  

## <a name="region-availability"></a><a name="regions"></a>Disponibilité des régions

La ressource Azure Stack Edge, le périphérique du Gestionnaire de fonctions réseau Azure et les applications managées Azure pour les fonctions réseau doivent se trouver dans la même région Azure. L’appareil physique Azure Stack Edge Pro avec GPU ne doit pas être dans la même région. 

* **Disponibilité des ressources :** pour la préversion, les ressources du Gestionnaire de fonctions réseau sont disponibles dans les régions suivantes :

   [!INCLUDE [Preview- available regions](../../includes/network-function-manager-regions-include.md)]

* **Disponibilité de l’appareil** : pour obtenir la liste de tous les pays/régions où l’appareil Azure Stack Edge Pro avec GPU est disponible, accédez à la page [Tarifs du GPU Azure Stack Edge Pro](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). Sous l’onglet **Azure Stack Edge Pro**, consultez la section  **Disponibilité** .

Avec la version actuelle, le Gestionnaire de fonctions réseau est un service régional. Pour les pannes sur l’ensemble de la région, les opérations de gestion des ressources du Gestionnaire de fonctions réseau seront affectées, mais les fonctions réseau exécutées sur le périphérique Azure Stack Edge ne seront pas affectées par la panne sur l’ensemble de la région. 

## <a name="partner-solutions"></a><a name="partners"></a>Solutions de partenaires

Reportez-vous à la [page des partenaires](partners.md) Gestionnaire de fonctions réseau pour un écosystème de partenaires en pleine croissance proposant des solutions de réseau privé virtuel, SD-WAN et VPN pour leurs applications managées de la Place de marché.

## <a name="faq"></a><a name="faq"></a>Forum Aux Questions

Pour consulter la FAQ, consultez la [FAQ sur le Gestionnaire de fonctions réseau](faq.md).

## <a name="next-steps"></a>Étapes suivantes

[Créer une ressource d’appareil](create-device.md).
