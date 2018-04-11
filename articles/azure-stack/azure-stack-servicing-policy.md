---
title: Stratégie de maintenance Azure Stack | Microsoft Docs
description: Découvrez la stratégie de maintenance Azure Stack et comment conserver un système intégré dans un état de prise en charge.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Stratégie de maintenance Azure Stack
Cet article décrit la stratégie de maintenance pour les systèmes intégrés Azure Stack et la marche à suivre pour conserver votre système dans un état de prise en charge. 

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe deux types de packages de mise à jour pour les systèmes intégrés : les mises à jour logicielles Microsoft et les mises à jour propres à votre fabricant de matériel OEM, telles que les microprogrammes et les pilotes. Ces mises à jour sont remises sous forme de packages de mise à jour Azure Stack distincts et sont gérées indépendamment.

- **Mises à jour logicielles Microsoft**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack. Vous pouvez télécharger ces packages de mise à jour directement à partir de Microsoft.
- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack sont responsables du cycle de vie de maintenance de bout en bout (y compris les instructions) pour les mises à jour logicielles des microprogrammes et des pilotes liées au matériel. De plus, ils sont propriétaires et gèrent les instructions pour tous les logiciels et le matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="update-package-release-cadence"></a>Cadence de publication des packages de mise à jour

Microsoft prévoie de publier des packages de mise à jour logicielle chaque mois. Toutefois, il est possible qu’il y ait plusieurs mises à jour durant un mois donné, ou qu’il n’y en ait aucune. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins.

Un package de mise à jour Microsoft a la convention de nommage suivante pour vous aider à identifier facilement la date de publication :

*Version_principale_produit.Version_mineure_produit.AAMMJJ.Numéro_Build*

Par exemple, une mise à jour logicielle Microsoft publiée le 15 juin 2017 a la version « 1.0.170615.1 ».

## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge
Pour continuer à bénéficier de la prise en charge, vous devez conserver votre déploiement Azure Stack en cours. La stratégie de report des mises à jour vise à conserver Azure Stack dans la prise en charge. Pour ce faire, elle doit exécuter la version de mise à jour la plus récente de mise à jour publiée ou exécuter les deux versions de mise à jour majeure précédentes.  Les correctifs logiciels ne sont pas considérés comme des versions de mise à jour majeure.  Si votre cloud Azure Stack est en retard de *plus de deux mises à jour*, il est considéré comme non conforme et doit être mis à jour jusqu’à au moins la version minimale prise en charge pour bénéficier de la prise en charge. 

Par exemple, si la dernière version mise à jour disponible est 1805 et que les deux mises à jour précédentes étaient les versions 1804 et 1803, 1803 et 1804 restent prises en charge. Toutefois, 1802 est hors de la prise en charge. La stratégie reste vraie lorsqu’aucune version n’est pour un ou deux mois. Par exemple, si la version actuelle est 1805 et qu’il n’existait aucune version 1804, les deux mises à jour précédentes des versions 1803 et 1802 restent prises en charge.

La mise à jour logicielle Microsoft est non cumulative et nécessite la mise à jour précédente comme composant requis. Si vous décidez de reporter une ou plusieurs mises à jour, prenez le runtime général en compte si vous souhaitez obtenir la dernière version. 

Le tableau suivant présente des exemples de versions de package de mise à jour, leur prérequis et la version minimale prise en charge dont votre système doit disposer pour bénéficier de la prise en charge. Ce tableau est basé sur la version initiale des systèmes intégrés Azure Stack (build 1708), avec la première publication de package de mise à jour (1709) en septembre 2017. 

| Package de mise à jour le plus récent (*exemple*) | Configuration requise | Version minimale prise en charge |
| -- | -- | -- |
| 1710 | 1709 | N/A |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
Dans la table précédente, il n’existe aucune version 1801.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md)


