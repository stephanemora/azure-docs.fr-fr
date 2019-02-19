---
title: Modèles de connexion des systèmes intégrés Azure Stack | Microsoft Docs
description: Déterminez les décisions relatives à la planification du déploiement pour les systèmes Azure Stack à plusieurs nœuds.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9760e6b9cdcd6f03f4377277f3426189b1fe0a61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182015"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modèles de connexion des systèmes intégrés Azure Stack
Si vous vous intéressez à l’achat d’un système intégré Azure Stack, prenez connaissance de [plusieurs points relatifs à l’intégration des centres de données](azure-stack-datacenter-integration.md) pour le déploiement Azure Stack, afin de prévoir l’intégration entre le système et votre centre de données. En outre, vous devez définir comment vous allez intégrer Azure Stack dans votre environnement de cloud hybride. Cet article fournit une vue d’ensemble de ces décisions, y compris celles relatives à la connexion à Azure, au magasin d’identités et au modèle de facturation.

Si vous décidez d’acheter un système intégré, votre fournisseur de matériel OEM vous guide durant une grande partie du processus de planification pour plus en détails. Ils effectueront également le déploiement réel.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Choisir un modèle de connexion pour le déploiement Azure Stack
Vous pouvez choisir de déployer Azure Stack en le connectant ou non à internet (et à Azure). Pour tirer le meilleur parti de Azure Stack, y compris les scénarios hybrides entre Azure Stack et Azure, vous souhaiterez le connecter à Azure. Ce choix détermine les options disponibles pour votre magasin d’identités (Azure Active Directory ou services de fédération Active Directory (AD FS)) et le modèle de facturation (facturation à l’utilisation ou selon la capacité), tel que résumé dans le schéma et le tableau suivants : 

![Scénarios de facturation et de déploiement Azure Stack](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Ce choix est déterminant. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. Vous ne pourrez pas revenir en arrière sans avoir à redéployer l’intégralité du système.  


|Options|Déploiement connecté à Azure|Déploiement déconnecté d’Azure|
|-----|-----|-----|
|Azure AD|![Pris en charge](media/azure-stack-connection-models/check.png)| |
|AD FS|![Pris en charge](media/azure-stack-connection-models/check.png)|![Pris en charge](media/azure-stack-connection-models/check.png)|
|Facturation à l’utilisation|![Pris en charge](media/azure-stack-connection-models/check.png)| |
|Facturation selon la capacité|![Pris en charge](media/azure-stack-connection-models/check.png)|![Pris en charge](media/azure-stack-connection-models/check.png)|
|Télécharger les packages de mise à jour directement sur Azure Stack|![Pris en charge](media/azure-stack-connection-models/check.png)|  |

Une fois que vous avez choisi le modèle de connexion à Azure à utiliser pour le déploiement Azure Stack, vous devrez prendre d’autres décisions liées au modèle de connexion pour sélectionner le magasin d’identités et le mode de facturation. 

## <a name="next-steps"></a>Étapes suivantes

[Décisions de déploiement Azure Stack connecté à Azure](azure-stack-connected-deployment.md)

[Décisions de déploiement Azure Stack non connecté à Azure](azure-stack-disconnected-deployment.md)
