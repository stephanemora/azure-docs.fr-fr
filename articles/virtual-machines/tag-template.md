---
title: Comment baliser une machine virtuelle à l’aide d’un modèle
description: En savoir plus sur le balisage d’une machine virtuelle à l’aide d’un modèle.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897372"
---
# <a name="tagging-a-vm-using-a-template"></a>Balisage d’une machine virtuelle à l’aide d’un modèle

Cet article explique comment baliser une machine virtuelle dans Azure à l’aide d’un modèle Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 50 étiquettes par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante.

[Ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) place des balises sur les ressources suivantes : Calcul (Machine virtuelle), Stockage (Compte de stockage) et Réseau (Adresse IP publique, Réseau virtuel et Interface réseau). Ce modèle est destiné à une machine virtuelle Windows, mais il peut être Aaapté pour les machines virtuelles Linux.

Cliquez sur le bouton **Déployer sur Azure** à partir du [lien du modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Ceci permet d'accéder au [portail Azure](https://portal.azure.com/) , où vous pouvez déployer ce modèle.

![Déploiement simple avec des balises](./media/tag/deploy-to-azure-tags.png)

Ce modèle inclut les balises suivantes : *Service*, *Application* et *Créé par*. Vous pouvez ajouter/modifier ces balises directement dans le modèle si vous voulez d’autres noms de balises.

![Balises Azure dans un modèle](./media/tag/azure-tags-in-a-template.png)

Comme vous pouvez le voir, les balises sont définies en tant que paires clé/valeur, séparées par un signe deux-points (:). Les balises doivent être définies dans ce format :

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Enregistrez le fichier de modèle après l’avoir modifié avec les balises de votre choix.

Ensuite, dans la section **Modifier les paramètres** , vous pouvez entrer les valeurs de vos balises.

![Modifier des balises dans le portail Azure](./media/tag/edit-tags-in-azure-portal.png)

Cliquez sur **Créer** pour déployer ce modèle avec vos valeurs pour les balises.

### <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le balisage de vos ressources Azure, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/management/overview.md) et [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/management/tag-resources.md).
- Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure](../cost-management-billing/understand/review-individual-bill.md).
