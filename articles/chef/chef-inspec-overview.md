---
title: Utiliser InSpec pour automatiser la conformité de votre infrastructure Azure
description: Découvrez comment utiliser InSpec pour détecter les problèmes de vos déploiements Azure
keywords: azure, chef, devops, machines virtuelles, présentation, automate, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 9256a4daf6564761553b495e559805a46e4eae32
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050725"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Utiliser InSpec pour automatiser la conformité de votre infrastructure Azure
[InSpec](https://www.chef.io/inspec/) est un framework open source gratuit qui permet de tester et d’auditer vos applications et votre infrastructure. InSpec compare l’état actuel de votre système avec l’état souhaité, que vous spécifiez sous la forme d’un code InSpec facile à lire et à écrire. InSpec détecte les violations et affiche ce qu’il a trouvé dans un rapport. Toutefois, c’est vous qui contrôlez la résolution de ces violations. Vous pouvez utiliser InSpec pour valider l’état de vos machines virtuelles exécutées dans Azure. Vous pouvez également utiliser InSpec pour analyser et valider l’état des ressources et des groupes de ressources d’un abonnement.

Cet article décrit les avantages d’InSpec pour assurer la conformité et la sécurité dans Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Faciliter la compréhension et l’évaluation de la conformité
Avec InSpec, vous transformez vos exigences en code exécutable et versionné que tout le monde peut lire. Vous pouvez ainsi organiser vos tests en profils composables, dans lesquels vous définissez et personnalisez des exceptions en fonction des besoins.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Détecter les problèmes affectant la flotte et hiérarchiser leur correction
Le mode de détection sans agent d’InSpec vous permet d’évaluer votre niveau d’exposition rapidement et à grande échelle. Les métadonnées intégrées pour le scoring d’impact et de gravité permettent de déterminer les zones où une correction doit être appliquée.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Inspecter les machines, les données et les nouvelles API SaaS
Les fonctionnalités de conformité des API cloud d’InSpec vous permettent d’effectuer des assertions plus ou moins précises concernant votre conformité cloud et de créer des rapports de conformité en continu.

## <a name="satisfy-audits"></a>Satisfaire aux audits
Avec InSpec, vous pouvez répondre aux questions d’audit à tout moment, et non uniquement à des intervalles prédéterminés (trimestriel ou annuel, par exemple). InSpec vous permet de démarrer un cycle d’audit en sachant exactement où se situe votre conformité, plutôt que d’être surpris par les résultats que l’auditeur a obtenus.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Réduire l’ambiguïté et la mauvaise communication concernant les règles
Dans la documentation, les configurations et les processus sont souvent sujets à interprétation. Le code exécutable supprime toute ambiguïté quant à ce qui doit être évalué, en fournissant des tests tangibles avec une intention claire.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Suivre l’évolution rapide du paysage des menaces et de la conformité
InSpec permet d’écrire et de publier du code de détection le jour même, et d’écrire de nouvelles règles pour répondre rapidement aux nouvelles réglementations. Ainsi, les changements au niveau des menaces et des réglementations ne constituent plus une urgence.

## <a name="next-steps"></a>Étapes suivantes
* [Créer une machine virtuelle Windows dans Azure avec Chef](/azure/virtual-machines/windows/chef-automation)