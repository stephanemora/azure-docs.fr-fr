---
title: Utiliser InSpec pour automatiser la conformité de votre infrastructure Azure
description: Découvrez comment utiliser InSpec pour détecter les problèmes de vos déploiements Azure
keywords: azure, chef, devops, machines virtuelles, présentation, automate, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158230"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Utiliser InSpec pour automatiser la conformité de votre infrastructure Azure

[InSpec](https://www.chef.io/inspec/) est le langage open source de Chef pour la description des règles de sécurité et de conformité qui peuvent être partagées par les ingénieurs logiciels, les opérateurs et les ingénieurs de sécurité. InSpec compare l’état actuel de votre infrastructure avec l’état souhaité, que vous spécifiez sous la forme d’un code InSpec facile à lire et à écrire. InSpec détecte les violations et affiche ce qu’il a trouvé dans un rapport. Toutefois, c’est vous qui contrôlez la résolution de ces violations.

Vous pouvez utiliser InSpec pour valider l’état des ressources et des groupes de ressources dans un abonnement, notamment les machines virtuelles, les configurations réseau, les paramètres Azure Active Directory et bien plus encore.

Cet article décrit les avantages d’InSpec pour assurer la conformité et la sécurité dans Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Faciliter la compréhension et l’évaluation de la conformité

La documentation sur la conformité écrite dans des feuilles de calcul ou des documents Word laisse les exigences ouvertes à l’interprétation. Avec InSpec, vous transformez vos exigences en code exécutable et versionné que tout le monde peut lire. Le code remplace les conversations quant à ce qui doit être évalué par des tests tangibles avec une intention claire.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Détecter les problèmes affectant la flotte et hiérarchiser leur correction

Le mode de détection sans agent d’InSpec vous permet d’évaluer votre niveau d’exposition rapidement et à grande échelle. Les métadonnées intégrées pour le scoring d’impact et de gravité permettent de déterminer les zones où une correction doit être appliquée. Vous pouvez également écrire rapidement des règles en réponse à de nouvelles vulnérabilités ou réglementations, et les déployer immédiatement.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Auditer les machines virtuelles Azure avec la fonctionnalité Guest Configuration pour Azure Policy

Azure prend directement en charge l’utilisation de définitions InSpec Chef pour auditer les machines virtuelles Azure par le biais de la [fonctionnalité Guest Configuration pour Azure Policy](/azure/governance/policy/concepts/guest-configuration). Guest Configuration évalue une machine virtuelle Linux par rapport à une définition InSpec Chef fournie et indique la conformité par le biais d’Azure Policy. Les résultats de ces audits sont également signalés dans les journaux Azure Monitor, ce qui autorise les alertes et autres scénarios d’automatisation.

## <a name="satisfy-audits"></a>Satisfaire aux audits

Avec InSpec, vous pouvez répondre aux questions d’audit à tout moment, et non uniquement à des intervalles prédéterminés (trimestriel ou annuel, par exemple). En exécutant en continu les tests InSpec, vous entrez dans un cycle d’audit en sachant exactement où vous vous situez en matière de conformité et d’historique, au lieu d’être surpris par les résultats d’un auditeur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Essayer InSpec dans Azure Cloud Shell](https://shell.azure.com)