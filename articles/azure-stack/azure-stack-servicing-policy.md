---
title: Stratégie de maintenance Azure Stack | Microsoft Docs
description: Découvrez la stratégie de maintenance Azure Stack et comment conserver un système intégré dans un état de prise en charge.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: harik
ms.openlocfilehash: f00b175fed36fbe2b28f1d11843bb17c54ac3873
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086962"
---
# <a name="azure-stack-servicing-policy"></a>Stratégie de maintenance Azure Stack
Cet article décrit la stratégie de maintenance pour les systèmes intégrés Azure Stack et la marche à suivre pour conserver votre système dans un état de prise en charge. 

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe deux types de packages de mises à jour pour les systèmes intégrés : 

- **Mises à jour logicielles Microsoft**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack. Vous pouvez télécharger ces packages de mise à jour directement à partir de Microsoft.

- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack sont responsables du cycle de vie de maintenance de bout en bout (y compris les instructions) pour les mises à jour logicielles des microprogrammes et des pilotes liées au matériel. De plus, ils sont propriétaires et gèrent les instructions pour tous les logiciels et le matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.


## <a name="update-package-release-cadence"></a>Cadence de publication des packages de mise à jour
Microsoft prévoie de publier des packages de mise à jour logicielle chaque mois. Toutefois, il est possible qu’il y ait plusieurs mises à jour durant un mois donné, ou qu’il n’y en ait aucune. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins. 

Pour lire la documentation relative à la planification et à la gestion des mises à jour, et pour savoir comment déterminer la version que vous utilisez, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md). 

Pour en savoir plus sur une mise à jour, notamment comment la télécharger, consultez les notes de publication qui s’y rapportent : 
- [Mise à jour 1809 d’Azure Stack](azure-stack-update-1809.md)
- [Mise à jour 1808 d’Azure Stack](azure-stack-update-1808.md)
- [Mise à jour 1807 d’Azure Stack](azure-stack-update-1807.md)

## <a name="hotfixes"></a>Correctifs logiciels
Parfois, Microsoft fournit des correctifs logiciels pour Azure Stack qui corrigent un problème précis souvent préventif ou urgent.  Chaque correctif est publié en même temps que son article correspondant dans la Base de connaissances Microsoft, où sont détaillés le problème, la cause et la résolution. 

Les correctifs logiciels sont téléchargés et installés comme les packages de mises à jour complets pour Azure Stack. Toutefois, contrairement à une mise à jour complète, les correctifs logiciels peuvent s’installer en quelques minutes. Il est recommandé aux opérateurs Azure Stack de définir des fenêtres de maintenance lors de l’installation des correctifs logiciels. Les correctifs mettent à jour la version de votre cloud Azure Stack. Il est donc facile de déterminer si le correctif a été appliqué. Un correctif logiciel distinct est fourni pour chaque version d’Azure Stack prise en charge. Chaque correctif d’une itération est cumulatif et englobe les mises à jour précédentes de la même version. Pour en savoir plus sur la nécessité de l’application d’un correctif logiciel, vous pouvez consulter l’article correspondant dans la Base de connaissances.  


## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge
Pour continuer à bénéficier de la prise en charge, vous devez conserver votre déploiement Azure Stack en cours. La stratégie de report des mises à jour est la suivante : pour que votre déploiement d’Azure Stack continue d’être pris en charge, vous devez exécuter la version de mise à jour la plus récente ou exécuter les deux versions de mise à jour précédentes. Les correctifs logiciels ne sont pas considérés comme des versions de mise à jour majeure. Si votre cloud Azure Stack est en retard de *plus de deux mises à jour*, il est considéré comme non conforme et doit être mis à jour jusqu’à au moins la version minimale prise en charge pour bénéficier de la prise en charge. 

Par exemple, si la dernière version mise à jour disponible est 1805 et que les deux mises à jour précédentes étaient les versions 1804 et 1803, 1803 et 1804 restent prises en charge. Toutefois, 1802 est hors de la prise en charge. La stratégie reste vraie lorsqu’aucune version n’est pour un ou deux mois. Par exemple, si la version actuelle est 1805 et qu’il n’existait aucune version 1804, les deux mises à jour précédentes des versions 1803 et 1802 restent prises en charge.

La mise à jour logicielle Microsoft est non cumulative et nécessite la mise à jour précédente comme composant requis. Si vous décidez de reporter une ou plusieurs mises à jour, prenez le runtime général en compte si vous souhaitez obtenir la dernière version. 

## <a name="get-support"></a>Obtenir de l’aide
Azure Stack suit le même processus de prise en charge qu’Azure. Les clients d’entreprise peuvent suivre le processus décrit dans [Création d’une demande de support Azure](/azure/azure-supportability/how-to-create-azure-support-request). Si vous êtes client d’un fournisseur de services cloud (CSP), contactez-le pour obtenir de l’aide.  Pour plus d’informations, consultez le [Forum Aux Questions sur le support technique Azure](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>Étapes suivantes

- [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md)


