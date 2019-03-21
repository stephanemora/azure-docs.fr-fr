---
title: Utiliser InSpec pour automatiser la conformité de votre infrastructure Azure
description: Découvrez comment utiliser InSpec pour détecter les problèmes de vos déploiements Azure
keywords: azure, chef, devops, machines virtuelles, présentation, automate, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293921"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Utiliser InSpec pour automatiser la conformité de votre infrastructure Azure

[InSpec](https://www.chef.io/inspec/) est le langage open source de Chef pour la description des règles de sécurité et de conformité qui peuvent être partagées par les ingénieurs logiciels, les opérateurs et les ingénieurs de sécurité. InSpec compare l’état actuel de votre infrastructure avec l’état souhaité, que vous spécifiez sous la forme d’un code InSpec facile à lire et à écrire. InSpec détecte les violations et affiche ce qu’il a trouvé dans un rapport. Toutefois, c’est vous qui contrôlez la résolution de ces violations.

Vous pouvez utiliser InSpec pour valider l’état des ressources et des groupes de ressources dans un abonnement, y compris les machines virtuelles, des configurations réseau, des paramètres Azure Active Directory et bien plus encore.

Cet article décrit les avantages d’InSpec pour assurer la conformité et la sécurité dans Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Faciliter la compréhension et l’évaluation de la conformité

Documentation sur la conformité écrites dans des feuilles de calcul ou des documents Word laisse exigences ouvert à l’interprétation. Avec InSpec, vous transformez vos exigences en code exécutable et versionné que tout le monde peut lire. Le code remplace les conversations quant à ce qui doit être évalué par des tests tangibles avec une intention claire.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Détecter les problèmes affectant la flotte et hiérarchiser leur correction

Le mode de détection sans agent d’InSpec vous permet d’évaluer votre niveau d’exposition rapidement et à grande échelle. Les métadonnées intégrées pour le scoring d’impact et de gravité permettent de déterminer les zones où une correction doit être appliquée. Vous pouvez également écrire rapidement des règles en réponse à de nouvelles vulnérabilités ou réglementations, et les déployer immédiatement.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Audit des machines virtuelles Azure avec la Configuration de stratégie invité

Azure prend directement en charge l’utilisation des définitions de Chef InSpec pour auditer des machines virtuelles par le biais de [invité Configuration de la stratégie Azure](/azure/governance/policy/concepts/guest-configuration). Configuration de l’invité évalue une machine virtuelle de Linux pour une définition Chef InSpec fournie et la conformité des rapports par le biais de la stratégie de Azure. Les résultats de ces audits sont également signalées par les journaux Azure Monitor ; activation des alertes et autres scénarios d’automatisation.

## <a name="satisfy-audits"></a>Satisfaire aux audits

Avec InSpec, vous pouvez répondre aux questions d’audit à tout moment, et non uniquement à des intervalles prédéterminés (trimestriel ou annuel, par exemple). En exécutant en continu les tests InSpec, vous entrez dans un cycle d’audit en sachant exactement où vous vous situez en matière de conformité et d’historique, au lieu d’être surpris par les résultats d’un auditeur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"] 
> [Essayez InSpec dans Azure Cloud Shell](https://shell.azure.com)