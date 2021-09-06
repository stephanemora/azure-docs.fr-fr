---
title: Déployer Fusion Core sur un appareil Azure Stack Edge
description: Découvrez comment déployer des solutions cloud à partir de Microsoft Azure et Metaswitch Networks, qui peuvent aider à assurer la pérennité de votre réseau, à réduire vos coûts, ainsi qu’à créer de nouveaux modèles d’entreprise et flux de revenus.
author: djrmetaswitch
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: drichards
ms.openlocfilehash: 93e7511cbe8202f15dd055467c253044fa617e74
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286926"
---
# <a name="deploy-fusion-core-on-an-azure-stack-edge-device"></a>Déployer Fusion Core sur un appareil Azure Stack Edge

Cet article décrit en détail le processus de déploiement de Fusion Core sur un appareil Azure Stack Edge.

## <a name="collect-required-azure-resources"></a>Collecter les ressources Azure requises

Vous devez disposer des éléments suivants :

- Un Azure Stack Edge Pro entièrement déployé avec GPU. Les ports de l’appareil doivent être connectés comme suit.

  - Port 5 : LAN
  - Port 6 : WAN
  - Un port connecté à votre réseau de gestion. Vous pouvez choisir n’importe quel port entre 1 et 4. Vous devez avoir activé le port choisi pour le calcul, comme décrit dans l’étape d’activation du réseau de calcul du [Tutoriel : Configurer le réseau pour Azure Stack Edge Pro avec GPU](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Un compte Azure avec un abonnement actif et un accès aux éléments suivants.

  - Le service Azure Network Function Manager. Il comporte l’espace de noms du fournisseur de ressources Microsoft.HybridNetwork. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Network Function Manager ?](../network-function-manager/overview.md).
  - L’application managée Fusion Core - 5G Packet Core. Vous en demander l’accès en accédant à [https://azuremarketplace.microsoft.com/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview) puis en appuyant sur le bouton **ME CONTACTER**. Nos représentants commerciaux vous indiqueront comment vous pouvez évaluer ou acheter des Fusion Core. Après avoir sélectionné l’une de ces options, nous vous donnerons accès à l’application managée.
  - Le rôle **Propriétaire** intégré au niveau d’un abonnement. Si ce n’est pas possible dans votre organisation, contactez votre représentant du support technique de Metaswitch.
- Un objet **Gestion de fonction réseau Azure - Appareil** configuré pour représenter l’appareil Azure Stack Edge.

## <a name="deploy-fusion-core"></a>Déployer Fusion Core

Le diagramme suivant illustre le processus de déploiement de Fusion Core, y compris les objets que vous allez créer et les méthodes de gestion continue après l’installation.

:::image type="content" source="./media/deploy-metaswitch-solution/fusion-core-on-azure-stack-edge-deployment-process.png" alt-text="Processus de déploiement de Fusion Core":::  

Après avoir sélectionné Fusion Core pour ASE sur la Place de marché Azure, vous allez déployer une instance nommée de l’application managée Fusion Core dans le groupe de ressources de votre choix dans votre abonnement Azure.

L’application managée Fusion Core crée un groupe de ressources managées contenant les ressources Packet Core Network Function et Relay à installer sur votre appareil Azure Stack Edge (ASE).

- La ressource Packet Core Network Function la version de machine virtuelle de base de Fusion Core et ses paramètres de configuration. Elle est masquée par défaut dans le Portail Azure.
- La ressource Relay contient un certain nombre de connexions hybrides qui assurent la connectivité nécessaire pour l’installation et l’accès à distance aux interfaces de surveillance.

Fusion Core est alors automatiquement déployé sur votre appareil ASE, comme suit.

1. L’appareil ASE télécharge et démarre la machine virtuelle de base de Fusion Core.
1. L’application de gestion des services Metaswitch installe et provisionne Fusion Core sur le cluster Kubernetes dans la machine virtuelle de base de Fusion Core.

En plus de coordonner l’installation de Fusion Core, l’application de gestion des services Metaswitch permet aux représentants du support technique de Metaswitch de contrôler l’état de fonctionnement du système et d’accéder aux diagnostics.


## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des instructions étape par étape concernant le déploiement de Fusion Core sur un appareil Azure Stack Edge, [Téléchargez Private 5G Edge Fusion Core Solution Guide](https://go.microsoft.com/fwlink/?linkid=2165096) (Guide de la solution Private 5G Edge Fusion Core).
- Apprenez-en davantage sur [Private 5G Edge Fusion Core](metaswitch-fusion-core-overview.md)