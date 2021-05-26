---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 43eda9f0f5cdb108c2949205d72433c9d7176571
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371860"
---
## <a name="create-an-app-service-kubernetes-environment"></a>Créer un environnement App Service Kubernetes

Avant de commencer, vous devez [créer un environnement App Service Kubernetes](../articles/app-service/manage-create-arc-environment.md) pour un cluster Kubernetes avec Azure Arc. 

> [!NOTE] 
> Lorsque vous créez l’environnement, veillez à noter le nom de l’emplacement personnalisé et le nom du groupe de ressources contenant l’emplacement personnalisé. Vous pourrez les utiliser pour trouver l’ID d’emplacement personnalisé dont vous aurez besoin lors de la création de votre application de fonction dans l’environnement. 
>
> Si vous n’avez pas créé l’environnement, contactez l’administrateur de votre cluster.