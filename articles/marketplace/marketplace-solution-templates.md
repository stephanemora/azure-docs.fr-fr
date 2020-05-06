---
title: Guide de publication d’offres de modèle de solution d’applications Azure | Place de marché Azure
description: Cet article décrit les conditions requises pour publier un modèle de solution sur la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 4cce509b8f4db7549a05eccc3127d2c437f9c037
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594574"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Applications Azure : Conditions requises pour la publication d’une offre de modèle de solution

Cet article explique les conditions requises pour le type d’offre de modèle de solution, qui est une façon de publier une offre d’application Azure dans la Place de marché Azure. Le type d’offre de modèle de solution nécessite un [modèle Azure Resource Manager (modèle ARM)](../azure-resource-manager/templates/overview.md) pour déployer automatiquement votre infrastructure de solution.

Utilisez le type d’offre de modèle de solution d’application Azure lorsque les conditions suivantes sont requises :

- Votre solution nécessite une automatisation supplémentaire de la configuration et du déploiement au-delà d’une seule machine virtuelle, par exemple une combinaison de machines virtuelles, de mise en réseau et de ressources de stockage.
- Votre client va gérer la solution lui-même.

L’appel à l’action qu’un utilisateur voit pour ce type d’offre est « Obtenir maintenant ».

## <a name="requirements-for-solution-template-offers"></a>Conditions requises pour les offres de modèles de solution

| **Configuration requise** | **Détails**  |
| ---------------  | -----------  |
|Facturation et mesure    |  Les offres de modèle de solution ne sont pas des offres de transaction, mais peuvent être utilisés pour déployer des offres de machines virtuelles payantes facturées via la place de marché commerciale de Microsoft. Les ressources que le modèle ARM de la solution déploie sont provisionnées dans l’abonnement Azure du client. Les machines virtuelles avec paiement à l’utilisation font l’objet de transactions avec le client par le biais de Microsoft et sont facturées dans le cadre de l’abonnement Azure du client.<br/> Dans le cas de BYOL (apportez votre propre licence), tandis que Microsoft facturera les frais d’infrastructure engagés dans l’abonnement client, vous effectuerez la transaction de vos frais de licence logicielle directement avec le client.   |
|Disque dur virtuel compatible avec Azure  |   Les machines virtuelles doivent être basées sur Windows ou Linux. Pour plus d'informations, consultez les pages suivantes : <ul> <li>[Créer une offre d’application Azure](./partner-center-portal/create-new-azure-apps-offer.md) (pour les disques durs virtuels Windows)</li><li>[Distributions Linux approuvées sur Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (pour les disques durs virtuel Linux).</li></ul> |
| Attribution de l’utilisation de client | L’activation de l’attribution de l’utilisation de client est obligatoire pour tous les modèles de solution publiés dans la Place de marché Azure. Pour plus d’informations sur l’attribution de l’utilisation de client et sur son activation, consultez [Attribution de l’utilisation de client partenaire Azure](./azure-partner-customer-usage-attribution.md).  |
| Utiliser des disques managés | Les [disques managés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) représentent l’option par défaut pour les disques persistants des machines virtuelles IaaS dans Azure. Vous devez utiliser des disques managés dans les modèles de solution. <br> <br> 1. Suivez les [conseils](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) et les [exemples](https://github.com/Azure/azure-quickstart-templates) pour l’utilisation de disques managés dans les modèles Azure Resource Manager pour mettre à jour vos modèles de solution. <br> <br> 2. Suivez les instructions ci-dessous pour importer le disque dur virtuel sous-jacent des disques managés dans un compte de stockage pour publier le disque dur virtuel en tant qu’image sur la Place de marché : <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [INTERFACE DE LIGNE DE COMMANDE](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>Étapes suivantes

- Si vous ne l’avez pas déjà fait, [renseignez-vous](https://azuremarketplace.microsoft.com/sell) au sujet de la Place de marché Azure.
- [Connectez-vous à l’Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- Pour plus d’informations, consultez [Créer une offre d’application Azure](./partner-center-portal/create-new-azure-apps-offer.md).
