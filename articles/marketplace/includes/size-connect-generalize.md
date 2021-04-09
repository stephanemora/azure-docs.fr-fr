---
title: Fichier include
description: fichier
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c60d2a9b13cce9251ff0f730081a9d677206770d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630104"
---
## <a name="generalize-the-image"></a>Généraliser l’image

Toutes les images dans Azure Marketplace doivent être réutilisables de façon générale. Pour autoriser cela, le VHD du système d’exploitation doit être généralisé. Cette opération consiste à supprimer d’une machine virtuelle tous les pilotes logiciels et identificateurs propres à une instance.

### <a name="for-windows"></a>Pour Windows

Les disques de système d’exploitation Windows sont généralisés à l’aide de l’outil [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Si vous mettez à jour ou reconfigurez le système d’exploitation par la suite, vous devrez réexécuter Sysprep.

> [!WARNING]
> Après l’exécution de Sysprep, éteignez la machine virtuelle jusqu’à son déploiement, car les mises à jour peuvent s’exécuter automatiquement. Cet arrêt évite que des mises à jour ultérieures apportent des modifications propres à une instance au système d’exploitation ou aux services installés. Pour plus d’informations sur l’exécution de sysprep, consultez [Étapes de généralisation d’un disque dur virtuel](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Pour Linux

Le processus suivant généralise une machine virtuelle Linux et la redéploie sous la forme d’une machine virtuelle distincte. Pour plus d’informations, consultez [Guide pratique pour créer une image de machine virtuelle ou de disque dur virtuel](../../virtual-machines/linux/capture-image.md). Vous pouvez arrêter quand vous atteignez la section intitulée « Créer une machine virtuelle à partir de l’image capturée ».

1. Supprimez l’agent Linux Azure.
    1. Connectez-vous à votre machine virtuelle Linux en utilisant un client SSH.
    2. Dans la fenêtre SSH, saisissez la commande suivante : `sudo waagent –deprovision+user`.
    3. Tapez Y pour continuer (vous pouvez ajouter le paramètre -force à la commande précédente pour éviter l’étape de confirmation).
    4. Une fois la commande exécutée, entrez **Exit** pour fermer le client SSH.
2. Arrêtez la machine virtuelle.
    1. Dans le portail Azure, sélectionnez votre groupe de ressources et désallouez la machine virtuelle.
    2. Votre machine virtuelle est désormais généralisée, et vous pouvez créer une autre machine virtuelle à l’aide de ce disque de machine virtuelle.

### <a name="capture-image"></a>Capturer l’image

Une fois que votre machine virtuelle est prête, vous pouvez la capturer dans une galerie d’images partagées Azure. Suivez les étapes ci-dessous pour la capturer :

1. Dans [Portail Azure](https://ms.portal.azure.com/), accédez à la page de votre machine virtuelle.
2. Sélectionnez **Capturer**.
3. Sous **Partager l’image dans Shared Image Gallery**, sélectionnez **Oui, la partager dans une galerie en tant que version d’image**.
4. Sous **État du système d’exploitation**, sélectionnez Généralisé.
5. Sélectionnez une galerie d’images cible ou **créez-en une nouvelle**.
6. Sélectionnez une définition d’image cible ou **créez-en une nouvelle**.
7. Indiquez un **numéro de version** pour l’image.
8. Sélectionnez **Vérifier + créer** pour passer en revue vos choix.
9. Une fois la validation réussie, sélectionnez **Créer**.

Pour publier, le compte de l’éditeur doit disposer d’un accès Propriétaire à la galerie d’images partagées. Pour accorder l’accès :

1. Accédez à la galerie d’images partagées.
2. Sélectionnez **Contrôle d’accès** (IAM) dans le volet gauche.
3. Sélectionnez **Ajouter** et **Ajouter une attribution de rôle**.
4. Sélectionnez un **rôle** ou un **propriétaire**.
5. Sous **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal de service**.
6. Sélectionnez l’adresse e-mail Azure de la personne qui publiera l’image.
7. Sélectionnez **Enregistrer**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Affiche la fenêtre Ajouter une attribution de rôle.":::

> [!NOTE]
> Vous n’avez pas besoin de générer d’URI de SAP, car vous pouvez désormais publier une image provenant d’une galerie d’images partagées sur Espace partenaires. Toutefois, si vous avez encore besoin de vous référer aux étapes de génération d’un URI de SAP, consultez [Comment générer un URI SAS pour une image de machine virtuelle](../azure-vm-get-sas-uri.md).
