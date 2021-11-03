---
title: À propos d’Azure Network Function Manager
description: En savoir plus sur Gestionnaire de fonctions réseau Azure, un service d’orchestration natif cloud complètement managé qui vous permet de déployer et de configurer des fonctions réseau sur Azure Stack Edge Pro avec GPU pour une expérience hybride cohérente à l’aide du Portail Azure.
author: prmitt
ms.service: network-function-manager
ms.topic: overview
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 55ea5ebe1dbeeb0d3e1d17c2085c19d471396b21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024104"
---
# <a name="what-is-azure-network-function-manager"></a>Présentation d’Azure Network Function Manager

Le Gestionnaire de fonctions réseau offre une expérience [Place de marché Azure](https://azure.microsoft.com/marketplace/) pour déployer des fonctions réseau telles que le noyau de paquets mobiles, la périphérie SD-WAN et les services VPN sur votre appareil [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) fonctionnant dans votre environnement local. Vous pouvez maintenant déployer rapidement un service réseau mobile privé ou une solution SD-WAN sur votre périphérique directement à partir du Portail de gestion Azure. Le Gestionnaire de fonctions réseau apporte des fonctions réseau à partir d’un écosystème de [partenaires](#partners) en pleine croissance. Network Function Manager est pris en charge sur [Azure Stack Edge Pro avec GPU](../databox-online/azure-stack-edge-gpu-overview.md).

## <a name="features"></a><a name="features"></a>Fonctionnalités

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

## <a name="region-availability"></a><a name="regions"></a>Disponibilité des régions

La ressource Azure Stack Edge, le périphérique du Gestionnaire de fonctions réseau Azure et les applications managées Azure pour les fonctions réseau doivent se trouver dans la même région Azure. L’appareil physique Azure Stack Edge Pro avec GPU ne doit pas être dans la même région.

* **Disponibilité des ressources -**  Les ressources du Network Function Manager sont disponibles dans les régions suivantes :

   [!INCLUDE [Available regions](../../includes/network-function-manager-regions-include.md)]

* **Disponibilité de l’appareil** : pour obtenir la liste de tous les pays/régions où l’appareil Azure Stack Edge Pro avec GPU est disponible, accédez à la page [Tarifs du GPU Azure Stack Edge Pro](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro). Sous l’onglet **Azure Stack Edge Pro**, consultez la section  **Disponibilité** .

Avec la version actuelle, le Gestionnaire de fonctions réseau est un service régional. Pour les pannes sur l’ensemble de la région, les opérations de gestion des ressources du Gestionnaire de fonctions réseau seront affectées, mais les fonctions réseau exécutées sur le périphérique Azure Stack Edge ne seront pas affectées par la panne sur l’ensemble de la région.

## <a name="partner-solutions"></a><a name="partners"></a>Solutions de partenaires

Reportez-vous à la [page des partenaires](partners.md) Gestionnaire de fonctions réseau pour un écosystème de partenaires en pleine croissance proposant des solutions de réseau privé virtuel, SD-WAN et VPN pour leurs applications managées de la Place de marché.

## <a name="faq"></a><a name="faq"></a>Forum Aux Questions

Pour consulter la FAQ, consultez la [FAQ sur le Gestionnaire de fonctions réseau](faq.md).

## <a name="next-steps"></a>Étapes suivantes

[Créer une ressource d’appareil](create-device.md).
