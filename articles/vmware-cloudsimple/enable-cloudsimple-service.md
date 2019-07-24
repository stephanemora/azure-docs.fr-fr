---
title: Activer la solution VMware du service CloudSimple
description: Décrit comment activer le service CloudSimple sur un abonnement Azure avant d’inscrire le fournisseur de ressources CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2553aa95d5028c510b4e1a1b7f51a9f410bcea51
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154866"
---
# <a name="register-the-microsoftvmwarecloudsimple-resource-provider-on-your-azure-subscription"></a>Sélectionner le fournisseur de ressources Microsoft.VMwareCloudSimple sur votre abonnement Azure

Le service CloudSimple vous permet d’utiliser la Solution Azure VMware de CloudSimple. Vous pouvez inscrire le service Microsoft.VMwareCloudSimple en tant que fournisseur de ressources.

## <a name="register-the-resource-provider"></a>Inscrire le fournisseur de ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Tous les services**.

3. Recherchez et sélectionnez des **abonnements**.

    ![Sélectionner des abonnements](media/cloudsimple-service-select-subscriptions.png)

4. Sélectionnez l’abonnement sur lequel activer le service CloudSimple.

5. Cliquez sur **Fournisseurs de ressources** pour l’abonnement.

6. Utilisez **Microsoft.VMwareCloudSimple** pour filtrer le fournisseur de ressources.

7. Sélectionnez le fournisseur de ressources puis cliquez sur **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/cloudsimple-service-enable-resource-provider.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un service CloudSimple](create-cloudsimple-service.md).
* Apprenez à [configurer un environnement de cloud privé](quickstart-create-private-cloud.md).
