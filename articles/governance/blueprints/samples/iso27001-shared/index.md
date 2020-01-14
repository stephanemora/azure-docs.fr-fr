---
title: Vue d’ensemble de l’exemple de blueprint Services partagés ISO 27001
description: Vue d’ensemble et architecture de l’exemple de blueprint Services partagés ISO 27001. Cet exemple de blueprint aide les clients à évaluer des contrôles ISO 27001 spécifiques.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 0b6a250ebac2744adca813219225388841f1776f
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922541"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Vue d’ensemble de l’exemple de blueprint Services partagés ISO 27001

L’exemple de blueprint Services partagés ISO 27001 fournit un ensemble de modèles d’infrastructure conformes et de garde-fou stratégiques qui facilitent l’attestation ISO 27001. Ce blueprint aide les clients à déployer des architectures cloud qui offrent des solutions aux scénarios qui impliquent des obligations d’accréditation ou de conformité.

L’exemple de blueprint [Charge de travail App Service Environment/SQL Database ISO 27001](../iso27001-ase-sql-workload/index.md) développe cet exemple.

## <a name="architecture"></a>Architecture

L’exemple de blueprint Services partagés ISO 27001 déploie une infrastructure de base dans Azure que les organisations peuvent utiliser pour héberger plusieurs charges de travail selon l’approche du centre de données virtuel.
Le centre de données virtuel est un ensemble éprouvé d’architectures de référence, d’outils d’automatisation et de modèles d’engagement utilisés par Microsoft avec ses grandes entreprises clientes. L’exemple de blueprint Services partagés s’appuie sur un environnement de centre de données virtuel Azure entièrement natif illustré ci-dessous.

![Conception de l’exemple de blueprint Services partagés ISO 27001](../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png)

Cet environnement se compose de plusieurs services Azure utilisés pour fournir une infrastructure de services partagés sécurisée, entièrement supervisée et prête pour les entreprises, basée sur les normes ISO 27001. Cet environnement comporte les éléments suivants :

- Rôles de [contrôle d’accès en fonction du rôle](../../../../role-based-access-control/overview.md) (RBAC) utilisés pour la ségrégation des tâches du point de vue du plan de contrôle. Trois rôles sont définis avant le déploiement d’une infrastructure :
  - Le rôle NetOps dispose des droits nécessaires pour gérer l’environnement réseau, notamment les paramètres du pare-feu, les paramètres du groupe de sécurité réseau, le routage et d’autres fonctionnalités réseau.
  - Le rôle SecOps dispose des droits nécessaires pour déployer et gérer [Azure Security Center](../../../../security-center/security-center-intro.md), définir des [stratégies Azure](../../../policy/overview.md) et d’autres droits liés à la sécurité.
  - Le rôle SysOps dispose des droits nécessaires pour définir des [stratégies Azure](../../../policy/overview.md) au sein de l’abonnement, gérer [Log Analytics](../../../../azure-monitor/overview.md) pour tout l’environnement, ainsi que d’autres droits opérationnels.
- [Log Analytics](../../../../azure-monitor/overview.md) est déployé en tant que premier service Azure pour vérifier que toutes les actions et tous les services journalisent à un emplacement central à partir du moment où vous démarrez votre déploiement sécurisé.
- Un réseau virtuel prenant en charge des sous-réseaux pour la connectivité à un centre de données local, une pile d’entrée et de sortie pour la connectivité Internet et un sous-réseau de service partagé qui utilise des groupes de sécurité réseau et ASG pour la micro-segmentation complète contenant :
  - Un serveur de rebond ou hôte bastion utilisé à des fins de gestion, uniquement accessible par le biais d’un [pare-feu Azure](../../../../firewall/overview.md) déployé dans le sous-réseau de la pile d’entrée
  - Deux machines virtuelles exécutant AADS (Active Directory Domain Services) et DNS uniquement accessibles par Jumpbox, pouvant être configurées uniquement pour répliquer AD sur un VPN ou une connexion [ExpressRoute](../../../../expressroute/expressroute-introduction.md) (non déployée par le blueprint)
  - Utilisation d’[Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) et d’une protection DDoS standard
- Une instance [Azure Key Vault](../../../../key-vault/key-vault-overview.md) utilisée pour héberger les secrets utilisés pour les machines virtuelles déployées dans l’environnement des services partagés

Tous ces éléments se conforment aux pratiques éprouvées publiés dans [Centre des architectures Azure - Architectures de référence](/azure/architecture/reference-architectures/).

> [!NOTE]
> L’infrastructure Services partagés ISO 27001 expose une architecture de base pour les charges de travail.
> Vous devez quand même déployer des charges de travail derrière cette architecture de base.

Pour plus d’informations, consultez la [documentation du centre de données virtuel](/azure/architecture/vdc/).

## <a name="next-steps"></a>Étapes suivantes

Vous avez parcouru la vue d’ensemble et l’architecture de l’exemple de blueprint Services partagés ISO 27001.
Continuez avec les articles suivants pour découvrir les correspondances de contrôles et la manière de déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprint Services partagés ISO 27001 - Correspondance des contrôles](./control-mapping.md)
> [Blueprint Services partagés ISO 27001 - Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).