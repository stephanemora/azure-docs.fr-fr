---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: cb0bf7926f1bbc310e422867752e1ee1762c979a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731548"
---
Avant de commencer, assurez-vous que :

* Votre abonnement Microsoft Azure est activé pour une ressource Azure Stack Edge. Vérifiez que vous avez utilisé un abonnement pris en charge tel que [Contrat Entreprise (EA) Microsoft](https://azure.microsoft.com/overview/sales-number/), [Fournisseur de solutions cloud (CSP)](/partner-center/azure-plan-lp) ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Vous disposez d’un accès Propriétaire ou Contributeur au niveau du groupe de ressources pour les ressources Azure Stack Edge, IoT Hub et Stockage Azure.

* Pour créer une commande dans Azure Edge Hardware Center, vous devez vous assurer que le fournisseur Microsoft.EdgeOrder est inscrit. Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).   
* Pour créer une ressource Azure Stack Edge, vous devez disposer d’autorisations Contributeur (ou supérieures) au niveau du groupe de ressources. Vous devez également être sûr que le fournisseur `Microsoft.DataBoxEdge` est inscrit. Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pour créer une ressource IoT Hub, vérifiez que le fournisseur Microsoft.Devices est bien inscrit. Pour plus d’informations sur l’inscription, consultez [Inscrire un fournisseur de ressources](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Pour créer une ressource de compte de stockage, là encore, vous avez besoin d’autorisations Contributeur (ou supérieures) au niveau du groupe de ressources. Par défaut, le stockage Azure est un fournisseur de ressources inscrit.
* Vous avez un accès Administrateur ou Utilisateur à l’API Graph Azure Active Directory. Pour plus d’informations, consultez [API Graph Azure Active Directory](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Vous disposez d’un compte de stockage Microsoft Azure doté d’informations d’identification d’accès.