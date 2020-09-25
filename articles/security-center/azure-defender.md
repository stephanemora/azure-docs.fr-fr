---
title: Vue d’ensemble d’Azure Defender et des plans disponibles
description: Découvrez les plans, les protections et les alertes d’Azure Defender. Ensuite, procédez à l’activation d’Azure Defender sur vos abonnements.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bb1c1e6443b5087b48aad7c3171bef557707adb1
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977291"
---
# <a name="introduction-to-azure-defender"></a>Présentation d’Azure Defender

Les fonctionnalités d’Azure Security Center couvrent les deux piliers de la sécurité cloud :

- **Gestion de la posture de sécurité cloud (CSPM)**
- **Protection de charge de travail cloud (PCTC)**

Les fonctionnalités CSPM de Security Center, telles que le Niveau de sécurité et la détection des erreurs de configurations de la sécurité dans les machines Azure Windows et Linux, font partie de l’expérience Security Center gratuite disponible pour tous les utilisateurs d’Azure. Utilisez ces fonctionnalités CSPM pour renforcer votre posture et garantir la conformité réglementaire.

**Azure Defender** est une plate-forme de protection de la charge de travail Cloud (CWPP) intégrée à Security Center pour une protection intelligente et avancée de vos charges de travail Azure et hybrides.

Voici le tableau de bord Azure Defender d’Azure Security Center :

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Exemple de tableau de bord Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Quels sont les types de ressources qu’Azure Defender peut sécuriser ?

Azure Defender fournit des alertes de sécurité ainsi qu’une protection avancée contre les menaces pour les machines virtuelles, les bases de données SQL, les conteneurs, les applications web, le réseau et plus encore.

Lorsque vous activez Azure Defender à partir de la zone **Tarification et paramètres** d’Azure Security Center, les plans Defender suivants sont tous activés simultanément et fournissent des défenses complètes pour les couches de calcul, de données et de service de votre environnement :

- [Azure Defender pour les serveurs](defender-for-servers-introduction.md)
- [Azure Defender pour App Service](defender-for-app-service-introduction.md)
- [Azure Defender pour Stockage](defender-for-storage-introduction.md)
- [Azure Defender pour SQL](defender-for-sql-introduction.md)
- [Azure Defender pour IoT](defender-for-iot-introduction.md)
- [Azure Defender pour Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender pour les registres de conteneurs](defender-for-container-registries-introduction.md)
- [Azure Defender pour Key Vault](defender-for-key-vault-introduction.md)

Chacun de ces plans est expliqué séparément dans la documentation relative à Security Center.


## <a name="hybrid-cloud-protection"></a>Protection cloud hybride

En plus de protéger votre environnement Azure, vous pouvez ajouter des fonctionnalités Azure Defender à votre environnement cloud hybride :

- Protection de serveurs hors Azure
- Protection de machines virtuelles dans d’autres clouds (par exemple, AWS et GCP)
- Protection d’appareils IoT

Vous bénéficierez de renseignement personnalisé sur les menaces et d’alertes hiérarchisées en fonction de votre environnement pour pouvoir vous concentrer sur le plus important.

Déployez [Azure Arc](https://azure.microsoft.com/services/azure-arc/) et activez Azure Defender pour étendre la protection aux machines virtuelles locales et multicloud et aux bases de données SQL. Azure Arc pour serveurs est un service gratuit, mais les services utilisés sur des serveurs équipés d’Arc (par exemple, Azure Defender) sont facturés sur la base des tarifs de ces services.

[En savoir plus sur Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Alertes Azure Defender 

Quand Azure Defender détecte une menace dans un des domaines de votre environnement, il génère une alerte. Ces alertes décrivent les détails des ressources affectées, les étapes de correction suggérées et, dans certains cas, l’option permettant de déclencher une application logique en réponse.

Il est possible d’exporter les alertes, qu’elles soient générées par Security Center ou envoyées à Security Center par un produit de sécurité intégrée. Pour exporter vos alertes vers Azure Sentinel (ou un système SIEM tiers) ou tout autre outil externe, suivez les instructions indiquées dans [Exportation d’alertes vers SIEM](continuous-export.md).

> [!NOTE]
> Les alertes provenant de différentes sources peuvent prendre différentes durées d’affichage. Par exemple, l’affichage des alertes qui nécessitent une analyse du trafic réseau peut prendre plus de temps que pour les alertes liées à des processus suspects s’exécutant sur des machines virtuelles.


## <a name="azure-defender-advanced-protection-capabilities"></a>Fonctionnalités de protection avancée d’Azure Defender

Azure Defender utilise l’analytique avancée pour formuler des recommandations personnalisées relatives aux ressources. 

Parmi les protections figurent la sécurisation des ports de gestion des machines virtuelles avec un accès juste-à-temps et des contrôles d’application adaptatifs permettant de créer des listes d’autorisation concernant les applications qui doivent ou non s’exécuter sur vos machines. 

Utilisez les vignettes de protection avancée du tableau de bord Azure Defender pour superviser et configurer chacune de ces protections. 

## <a name="vulnerability-assessment-and-management"></a>Gestion et évaluation des vulnérabilités

Azure Defender comprend l’analyse des vulnérabilités des machines virtuelles et des registres de conteneurs sans coût supplémentaire. Même si les scanners sont fournis par Qualys, aucune licence Qualys ni même aucun compte Qualys n’est nécessaire : tout est traité en arrière-plan dans Security Center. 

Passez en revue les résultats de ces scanners de vulnérabilité et répondez-y au sein de Security Center. Security Center se rapproche ainsi un peu plus d’une interface unique centralisant toutes les activités de sécurité dans le cloud.

Pour plus d’informations, consultez les pages suivantes :

- [Solution intégrée d’évaluation des vulnérabilités de Security Center pour les machines virtuelles Azure](deploy-vulnerability-assessment-vm.md)
- [Identification des vulnérabilités dans les images des registres de conteneurs Azure Container Registry](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert les avantages d’Azure Defender. 

> [!div class="nextstepaction"]
> [Activation d’Azure Defender](security-center-pricing.md)