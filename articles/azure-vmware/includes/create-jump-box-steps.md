---
title: Créer la jumpbox Azure VMware Solution
description: Procédure pour créer la jumpbox Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462245"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Dans le groupe de ressources, sélectionnez **+ Ajouter**, recherchez et sélectionnez **Microsoft Windows 10**, puis **Créer**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Ajoutez une nouvelle machine virtuelle Windows 10 pour une jumpbox." border="true":::

1. Entrez les valeurs nécessaires dans les champs, puis sélectionnez **Vérifier + créer**. 

   Pour plus d’informations sur les champs, consultez le tableau suivant.

   | Champ | Valeur |
   | --- | --- |
   | **Abonnement** | Cette valeur est prérenseignée avec l’abonnement appartenant au groupe de ressources. |
   | **Groupe de ressources** | La valeur est préremplie pour le groupe de ressources actuel que vous avez créé dans le tutoriel précédent.  |
   | **Nom de la machine virtuelle** | Entrez un nom unique pour la machine virtuelle. |
   | **Région** | Sélectionnez le lieu géographique de la machine virtuelle. |
   | **Options de disponibilité** | Laissez la valeur par défaut sélectionnée. |
   | **Image** | Sélectionnez l’image de machine virtuelle. |
   | **Taille** | Laissez la valeur de la taille par défaut. |
   | **Type d’authentification**  | Sélectionnez **Mot de passe**. |
   | **Nom d’utilisateur** | Entrez le nom d’utilisateur pour la connexion à la machine virtuelle. |
   | **Mot de passe** | Entrez le mot de passe pour la connexion à la machine virtuelle. |
   | **Confirmer le mot de passe** | Entrez le mot de passe pour la connexion à la machine virtuelle. |
   | **Ports d’entrée publics** | Sélectionnez **Aucun**. Si vous sélectionnez Aucun, vous pouvez utiliser l’[accès JIT](../../security-center/security-center-just-in-time.md#jit-configure) pour contrôler l’accès à la machine virtuelle seulement quand vous voulez y accéder. Vous pouvez également utiliser un [bastion Azure](../../bastion/tutorial-create-host-portal.md) si vous souhaitez accéder au serveur de rebond de façon sécurisée à partir d’Internet sans exposer de port réseau.  |


1. Une fois la validation réussie, sélectionnez **Créer** pour démarrer le processus de création de la machine virtuelle.

