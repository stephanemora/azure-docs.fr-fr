---
title: Configurer des comptes de laboratoire dans Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un compte de laboratoire après sa création.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414036"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurer des comptes de laboratoire dans Azure Lab Services 
Dans Azure Lab Services, un compte de laboratoire est un conteneur pour les types de laboratoire gérés tels que des laboratoires de salle de classe. Un administrateur configure un compte de laboratoire avec Azure Lab Services et fournit l’accès à tous les propriétaires de laboratoire qui peuvent alors créer des laboratoires dans leur compte. Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire.

## <a name="connect-with-a-peer-virtual-network"></a>Se connecter avec un réseau virtuel homologue
Pour vous connecter à un réseau virtuel comme un réseau pair à pair au réseau virtuel du laboratoire, procédez comme suit :

1. Sur le **compte Lab** page, sélectionnez **configuration Labs** sur le menu de gauche.

    ![Page de Configuration de laboratoires](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Pour **réseau virtuel homologue**, sélectionnez **activé** ou **désactivé**. La valeur par défaut est **désactivé**. Pour activer le réseau virtuel homologue, procédez comme suit : 
    1. Sélectionnez **Enabled**.
    2. Sélectionnez le **réseau virtuel** dans la liste déroulante. 
3. Sélectionnez **Enregistrer** dans la barre d’outils. 

Les laboratoires créés dans ce compte sont connectés au réseau virtuel sélectionné. Ils peuvent accéder aux ressources dans le réseau virtuel sélectionné. Pour plus d’informations, consultez [connecter le réseau de votre laboratoire avec un réseau virtuel homologue dans Azure Lab Services](how-to-connect-peer-virtual-network.md).

Lorsque vous sélectionnez un réseau virtuel pour le **réseau virtuel homologue** champ, le **créateur de laboratoire autoriser pour choisir un emplacement de laboratoire** option est désactivée. Cela signifie que les laboratoires dans le compte de laboratoire doivent être dans la même région que le compte de laboratoire pour qu’ils puissent se connecter aux ressources dans le réseau virtuel homologue. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Autoriser le créateur de laboratoire choisir l’emplacement pour le laboratoire
Vous pouvez autoriser le créateur de laboratoire créer des laboratoires dans un emplacement autre que l’emplacement du compte de laboratoire en suivant ces étapes : 

1. Sur le **compte Lab** page, sélectionnez **configuration Labs** sur le menu de gauche.
2. Pour le **créateur de laboratoire autoriser pour choisir un emplacement de laboratoire**, sélectionnez **activé** si vous souhaitez que le créateur de laboratoire pour être en mesure de sélectionner un emplacement pour le laboratoire. Si elle est désactivée, les laboratoires sont automatiquement créés dans le même emplacement que celui dans lequel se trouve le compte de laboratoire. 
    
    Ce champ est désactivé lorsque vous sélectionnez un réseau virtuel pour le **réseau virtuel homologue** champ. Cela signifie que les laboratoires dans le compte de laboratoire doivent être dans la même région que le compte de laboratoire pour qu’ils puissent accéder aux ressources dans le réseau virtuel homologue. 
1. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Configurer le paramètre d’emplacement de laboratoire](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Spécifiez une plage d’adresses pour les machines virtuelles dans le laboratoire
La procédure suivante comporte des étapes pour spécifier une plage d’adresses pour les machines virtuelles dans le laboratoire. Si vous mettez à jour la plage que vous avez spécifié précédemment, la plage d’adresses modifié s’applique uniquement aux machines virtuelles qui sont créées après que la modification a été effectuée. 

Voici certaines restrictions lors de la spécification de la plage d’adresses que vous devez prendre en compte. 

- Le préfixe doit être inférieure ou égale à 23. 
- Si un réseau virtuel est homologué avec le compte de laboratoire, la plage d’adresses fournie ne peut pas se chevaucher avec une plage d’adresses de réseau virtuel homologué.

1. Sur le **compte Lab** page, sélectionnez **configuration Labs** sur le menu de gauche.
2. Pour le **plage d’adresses** champ, spécifiez la plage d’adresses pour les machines virtuelles qui seront créés dans le laboratoire. La plage d’adresses doit être dans la notation inter-domain routing (CIDR) (exemple : 10.20.0.0/23). Les machines virtuelles dans le laboratoire sera créées dans cette plage d’adresses.
3. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Configurer la plage d’adresses](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour configurer un laboratoire de classe dans un compte de laboratoire, l’utilisateur doit être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer le compte de laboratoire est automatiquement ajouté à ce rôle. Si vous envisagez d’utiliser le même compte d’utilisateur pour créer un laboratoire de classe, vous pouvez ignorer cette étape. Pour utiliser un autre compte d’utilisateur pour créer un laboratoire de classe, procédez comme suit : 

Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle **Créateur de laboratoire** :

1. Dans la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **+ Ajouter une attribution de rôle** dans la barre d’outils. 

    ![Contrôle d’accès -> bouton Ajouter une attribution de rôle](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Dans la page **Ajouter une attribution de rôle**, sélectionnez **Créateur lab** pour **Rôle**, sélectionnez l’utilisateur à ajouter au rôle Créateur lab, puis sélectionnez **Enregistrer**. 

    ![Ajouter un créateur lab](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Spécifier les images de la Place de marché accessibles aux créateurs lab
En tant que propriétaire d’un compte de laboratoire, vous pouvez spécifier les images de la place de marché que les créateurs de laboratoire peuvent utiliser pour créer des laboratoires dans le compte de laboratoire. 

1. Sélectionnez **Images de la Place de marché** à gauche du menu. Par défaut, la liste complète des images (activées et désactivées) s’affiche. Vous pouvez filtrer la liste pour afficher uniquement les images activées/désactivées en sélectionnant l’option **Enabled only (Activées uniquement)**/**Disabled only (Désactivées uniquement)** dans la liste déroulante en haut. 
    
    ![Page des images de la Place de marché](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Les images de la place de marché affichées dans la liste sont uniquement celles qui correspondent aux conditions suivantes :
        
    - Crée une seule machine virtuelle.
    - Utilise Azure Resource Manager pour approvisionner des machines virtuelles
    - Ne nécessite pas l’achat d’un plan de licences supplémentaire
2. Pour **désactiver** une image de la Place de marché qui a été activée, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Disable image (Désactiver l’image)**. 

        ![Désactiver une image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)**. 

        ![Désactiver plusieurs images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De même, pour **activer** une image de la Place de marché, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Enable image (Désactiver l’image)**. 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)**. 




## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
