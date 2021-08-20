---
title: Présentation d’Affirmed Private Network Service sur Azure
description: En savoir plus sur les solutions d’Affirmed Private Network Service sur Azure pour les réseaux privés LTE/5G.
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: ca2327ee8e9245698dc8d9b57b2c5bf2bf8e6881
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458521"
---
# <a name="what-is-affirmed-private-network-service-on-azure"></a>Présentation d’Affirmed Private Network Service sur Azure

Le service APNS (Affirmed Private Network Service) est une offre de service de réseau managé qui a été créée pour les fournisseurs de service managé et les opérateurs de réseau mobile dans le but de fournir des solutions LTE privées et 5G privées aux entreprises.

Affirmed a combiné sa technologie de base mobile aux fonctionnalités d’Azure en vue de créer une solution clé en main pour les réseaux LTE/5G privés, afin d’aider les opérateurs et les entreprises à tirer parti des réseaux managés et de la périphérie mobile. La combinaison de la gestion cloud et de l’automatisation permet aux fournisseurs de services managés de fournir une infrastructure entièrement managée. Cela constitue également une solution complète de bout en bout permettant aux opérateurs de choisir le réseau d’accès radio, la carte SIM et les services Azure les plus performants dans un vaste écosystème de partenaires proposés sur la Place de marché Azure. La solution comprend cinq composants :

- **Cœur mobile natif Cloud** : ce composant est conforme à la norme 3GPP. Il prend en charge les fonctions de réseau pour la 4G et la 5G, et dispose de sondes de réseau virtuel situées nativement dans le cœur mobile. Le cœur mobile peut être déployé sur des machines virtuelles, sur des serveurs physiques ou sur le cloud d’un opérateur. Un matériel dédié n’est donc pas nécessaire.

- **Private Network Service Manager – Affirmed Networks** : Private Network Service Manager est l’application que les opérateurs utilisent pour déployer, superviser et gérer les réseaux de base mobiles privés sur la plateforme Azure. Il offre un ensemble complet de fonctionnalités de gestion, notamment l’auto-activation simple et la gestion de ressources réseau privées via un portail programmatique piloté par GUI (interface graphique utilisateur).

- **Azure Network Functions Manager** : Azure Network Functions Manager (NFM) est un service d’orchestration natif cloud complètement managé qui permet aux clients de déployer et de configurer des fonctions réseau sur Azure Stack Edge Pro avec GPU pour une expérience hybride cohérente à l’aide du portail Azure.

- **Azure Cloud** : plateforme publique du cloud avec des solutions, notamment des services IaaS (Infrastructure as a Service), PaaS (Platform as a Service) et SaaS (Software as a Service) qui peuvent être utilisés par exemple pour l’analytique, l’informatique virtuelle, le stockage, la mise en réseau et bien plus encore.

- **Azure Stack Edge** : solution matérielle en tant que service managé par le cloud et fournie par Microsoft. Il offre la puissance du cloud Azure à un serveur local et robuste qui peut être déployé pratiquement à tous les emplacements où des tâches de calcul avancées et d’intelligence artificielle locale doivent être effectuées.


:::image type="content" source="./media/affirmed-overview/affirmed-private-network-service-solution.png" alt-text="Solution Affirmed Private Network Service":::

## <a name="why-use-the-affirmed-private-network-solution"></a>Pourquoi utiliser la solution Affirmed Private Network Service ?
APNS offre les principaux avantages suivants aux opérateurs et à leurs clients :

- **Flexibilité du déploiement** : APNS utilise la technologie de séparation du plan de contrôle et du plan utilisateur (CUPS, Control User Plane Separation) et prend en charge trois types de modes de déploiement pour répondre à un large éventail de scénarios d’opérateur souhaités pour l’offre aux entreprises. À l’aide de Private Network Service Manager, les opérateurs peuvent configurer les modèles de déploiement suivants :

    - Le modèle autonome permet aux opérateurs de fournir un réseau privé autonome complet localement, en fournissant le RAN, le noyau 5G sur Azure Stack Edge et la couche de gestion sur le cloud centralisé.

    - Le modèle distribué permet un traitement plus rapide des données en distribuant le plan utilisateur plus près de la périphérie de l’entreprise sur Azure Stack Edge, tandis que le plan de contrôle se trouve sur le cloud. Comme modèle de ce type, on peut citer par exemple des sites de fabrication.

    - Les solutions « Tout dans le cloud » permettent de déployer la totalité du noyau 5G dans le cloud alors que le RAN est en périphérie. Cela permet l’allocation dynamique des ressources cloud pour répondre aux demandes fluctuantes des charges de travail.

- **Intégration MNO**  – APNS offre l’intégration aux opérateurs de réseau mobile (MNO, Mobile Network Operator), c’est-à-dire qu’il offre une mobilité complète dans les réseaux d’opérateurs privés et publics avec son noyau d’abonné distribué. Les opérateurs ont l’avantage de mettre à l’échelle le réseau mobile privé vers des milliers de sites périphériques d’entreprise.

    - Prend en charge toutes les options de spectre – licence MNO, licence privée, CBRS, partagé, sans licence.

    - Prend en charge les réseaux privés isolés/autonomes, l’itinérance sur plusieurs sites et l’itinérance des macros grâce à l’intégration MNO.

    - Peut fournir une disponibilité de service de 99,999 % et interfonctionner avec toute technologie radio LTE et 5G NR conforme 3GPP. Offre une résilience de classe opérateur pour les entreprises.

- **Automatisation et facilité de gestion** – la solution APNS peut être entièrement managée à distance via Service Manager sur le cloud Azure. Par le biais de Service Manager, les utilisateurs finaux ont accès à leur tableau de bord personnalisé et peuvent gérer, afficher, activer ou désactiver des appareils sur le réseau mobile privé. Les opérateurs peuvent superviser l’état des réseaux pour détecter les problèmes de réseau et les paramètres clés afin de garantir des performances optimales.

    - Fournit un service de réseau mobile privé sécurisé, fiable, à bande passante élevée et à latence faible qui s’exécute sur le calcul de périphérie multi-accès privé Azure.

    - Prend en charge la gestion à distance complète, sans qu’une intervention sur place ne soit nécessaire.

    - Fournit l’automatisation du cloud pour permettre aux opérateurs d’offrir des services managés aux entreprises ou de réaliser un partenariat avec des fournisseurs MSP qui, à leur tour, proposent des services managés.

- **Insights métier et réseau plus intelligents** – Le cœur mobile Affirmed dispose d’une fonction incorporée de sonde virtuelle / Packet Broker qui peut être utilisée pour fournir des insights réseau. L’opérateur peut utiliser ces insights pour prendre de meilleures décisions en matière de réseau, tandis que ses clients peuvent utiliser ces insights pour prendre des décisions de monétisation plus intelligentes.

- **Sécurité et confidentialité des données** – APNS utilise Azure pour assurer la sécurité et la conformité sur les réseaux privés et les applications d’entreprise. Les opérateurs peuvent déployer la solution en toute confiance pour des cas d’usage dans des secteurs soumis à des contraintes légales strictes, comme la santé, les services publics, la sécurité publique et la défense.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [déployer la solution Affirmed Private Network Service](deploy-affirmed-private-network-service-solution.md)



