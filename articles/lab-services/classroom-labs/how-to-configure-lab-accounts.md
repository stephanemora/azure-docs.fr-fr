---
title: Configurer des comptes de laboratoire dans Azure Lab Services | Microsoft Docs
description: Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire dans Azure Lab Services.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 597bbfdc35c556b5c75968084c01ac222fc468f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718088"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurer des comptes de laboratoire dans Azure Lab Services 
Dans Azure Lab Services, un compte de laboratoire est un conteneur pour les types de laboratoires gérés tels que les laboratoires de classe. Un administrateur configure un compte de laboratoire avec Azure Lab Services et fournit l’accès à tous les propriétaires de laboratoire qui peuvent alors créer des laboratoires dans leur compte. Cet article explique comment créer un compte de laboratoire, voir tous les comptes de laboratoire et supprimer un compte de laboratoire.

## <a name="connect-with-a-peer-virtual-network"></a>Se connecter à un réseau virtuel de pair
Pour vous connecter à un réseau virtuel en tant que réseau virtuel de pair au réseau virtuel du laboratoire, procédez comme suit :

1. Sur la page **Compte lab**, sélectionnez **Configuration des labs** dans le menu de gauche.

    ![Page de configuration des labs](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Pour **Appairer un réseau virtuel**, sélectionnez **Activé** ou **Désactivé**. La valeur par défaut est **Désactivé**. Pour activer le réseau virtuel de pair, procédez comme suit : 
    1. Sélectionnez **Enabled**.
    2. Sélectionnez le **réseau virtuel** dans la liste déroulante. 
3. Sélectionnez **Enregistrer** dans la barre d’outils. 

Les laboratoires créés dans ce compte sont connectés au réseau virtuel sélectionné. Ils peuvent accéder aux ressources dans le réseau virtuel sélectionné. Pour plus d’informations, consultez [Connect your lab’s network with a peer virtual network in Azure Lab Services](how-to-connect-peer-virtual-network.md) (Connecter le réseau de votre laboratoire à un réseau virtuel de pair dans Azure Lab Services).

Lorsque vous sélectionnez un réseau virtuel pour le champ **Appairer un réseau virtuel**, l’option **Autoriser le créateur du lab à choisir l’emplacement du lab** est désactivée. Cela s’explique par le fait que les laboratoires dans le compte de laboratoire doivent être dans la même région que le compte de laboratoire pour pouvoir se connecter aux ressources dans le réseau virtuel de pair. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Autoriser le créateur du laboratoire à choisir l’emplacement du laboratoire
Vous pouvez autoriser le créateur du laboratoire à créer des laboratoires dans un emplacement autre que l’emplacement du compte de laboratoire en suivant ces étapes : 

1. Sur la page **Compte lab**, sélectionnez **Configuration des labs** dans le menu de gauche.
2. Pour **Autoriser le créateur du lab à choisir l’emplacement du lab**, sélectionnez **Activé** si vous souhaitez que le créateur de laboratoire soit en mesure de sélectionner un emplacement pour le laboratoire. Si l’option est désactivée, les laboratoires sont automatiquement créés dans le même emplacement que celui dans lequel se trouve le compte de laboratoire. 
    
    Ce champ est désactivé lorsque vous sélectionnez un réseau virtuel pour le champ **Appairer un réseau virtuel**. Cela s’explique par le fait que les laboratoires dans le compte de laboratoire doivent être dans la même région que le compte de laboratoire pour pouvoir accéder aux ressources dans le réseau virtuel de pair. 
1. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Configurer le paramètre d’emplacement de laboratoire](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Spécifier une plage d’adresses pour les machines virtuelles du laboratoire
La procédure suivante comporte des étapes pour spécifier une plage d’adresses pour les machines virtuelles dans le laboratoire. Si vous mettez à jour la plage que vous avez spécifiée précédemment, la plage d’adresses modifiée s’applique uniquement aux machines virtuelles qui sont créées après que la modification a été effectuée. 

Voici quelques restrictions à garder à l’esprit lors de la spécification de la plage d’adresses. 

- Le préfixe doit être inférieur ou égal à 23. 
- Si un réseau virtuel est associé au compte de laboratoire, la plage d’adresses fournie ne peut pas chevaucher une plage d’adresses de réseau virtuel associé.

1. Sur la page **Compte lab**, sélectionnez **Configuration des labs** dans le menu de gauche.
2. Pour le champ **Plage d’adresses**, spécifiez la plage d’adresses pour les machines virtuelles qui seront créées dans le laboratoire. La plage d’adresses doit être mentionnée dans la notation CIDR (Classless Inter-Domain Routing), par exemple : 10.20.0.0/23). Les machines virtuelles du laboratoire seront créées dans cette plage d’adresses.
3. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Configurer la plage d’adresses](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Ajouter un utilisateur au rôle Créateur de laboratoire
Pour configurer un laboratoire de classe dans un compte de laboratoire, l’utilisateur doit être membre du rôle **Créateur de laboratoire** dans le compte de laboratoire. Le compte utilisé pour créer le compte de laboratoire est automatiquement ajouté à ce rôle. Si vous envisagez d’utiliser le même compte d’utilisateur pour créer un laboratoire de classe, vous pouvez ignorer cette étape. Pour utiliser un autre compte d’utilisateur pour créer un laboratoire de classe, procédez comme suit : 

Pour donner aux formateurs l’autorisation de créer des laboratoires pour leurs classes, ajoutez-les au rôle **Créateur de laboratoire** :

1. Dans la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)** , puis cliquez sur **+ Ajouter une attribution de rôle** dans la barre d’outils. 

    ![Contrôle d’accès -> bouton Ajouter une attribution de rôle](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Dans la page **Ajouter une attribution de rôle**, sélectionnez **Créateur lab** pour **Rôle**, sélectionnez l’utilisateur à ajouter au rôle Créateur lab, puis sélectionnez **Enregistrer**. 

    ![Ajouter un créateur lab](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Si vous ajoutez un utilisateur de compte non Microsoft en tant que créateur de laboratoire, consultez la section [Ajouter un utilisateur de compte non Microsoft en tant que créateur de laboratoire](#add-a-non-microsoft-account-user-as-a-lab-creator). 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Spécifier les images de la Place de marché accessibles aux créateurs lab
En tant que propriétaire d’un compte de laboratoire, vous pouvez spécifier les images de la place de marché que les créateurs de laboratoire peuvent utiliser pour créer des laboratoires dans le compte de laboratoire. 

1. Sélectionnez **Images de la Place de marché** à gauche du menu. Par défaut, la liste complète des images (activées et désactivées) s’affiche. Vous pouvez filtrer la liste pour afficher uniquement les images activées/désactivées en sélectionnant l’option **Enabled only (Activées uniquement)** /**Disabled only (Désactivées uniquement)** dans la liste déroulante en haut. 
    
    ![Page des images de la Place de marché](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    Les images de la place de marché affichées dans la liste sont uniquement celles qui correspondent aux conditions suivantes :
        
    - Crée une seule machine virtuelle.
    - Utilise Azure Resource Manager pour approvisionner des machines virtuelles
    - Ne nécessite pas l’achat d’un plan de licences supplémentaire
2. Pour **désactiver** une image de la Place de marché qui a été activée, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Disable image (Désactiver l’image)** . 

        ![Désactiver une image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)** . 

        ![Désactiver plusieurs images](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De même, pour **activer** une image de la Place de marché, effectuez l’une des actions suivantes : 
    1. Sélectionnez **... (points de suspension)** dans la dernière colonne, puis sélectionnez **Enable image (Désactiver l’image)** . 
    2. Sélectionnez une ou plusieurs images dans la liste en cochant la case à côté du nom de l’image, puis sélectionnez **Disable selected images (Désactiver les images sélectionnées)** . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Ajouter un utilisateur de compte non Microsoft en tant que créateur de laboratoire
Pour ajouter un utilisateur en tant que créateur de laboratoire, utilisez ses comptes de messagerie. Les types de comptes de messagerie suivants peuvent être utilisés :

- Un compte de messagerie fourni par l’Office 365 Azure Active Directory (AAD) de votre université. 
- Un compte de messagerie Microsoft, tel que `@outlook.com`, `@hotmail.com`, `@msn.com` ou `@live.com`.
- Un compte de messagerie non Microsoft, tel qu’un compte fourni par Yahoo ou Google. Cependant, ces types de comptes doivent être liés à un compte Microsoft.
- Un compte GitHub. Ce compte doit être lié à un compte Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Utiliser un compte de messagerie non Microsoft
Les créateurs de laboratoire et formateurs peuvent utiliser des comptes de messagerie non Microsoft pour s’inscrire et se connecter à un labo de classe.  Toutefois, la connexion au portail Lab Services nécessite que les formateurs créent d’abord un compte Microsoft lié à leur adresse e-mail non Microsoft.

De nombreux formateurs ont peut-être déjà un compte Microsoft lié à leurs adresses e-mail non Microsoft. Par exemple, les formateurs ont déjà un compte Microsoft s’ils ont utilisé leur adresse e-mail avec d’autres produits ou services Microsoft, tels que Office, Skype, OneDrive ou Windows.  

Lorsque les formateurs se connectent au portail Lab Services, ils sont invités à entrer leur adresse e-mail et leur mot de passe. Si le formateur tente de se connecter avec un compte non Microsoft qui n’a pas de compte Microsoft lié, le message d’erreur suivant s’affiche : 

![Message d’erreur](../media/how-to-configure-student-usage/cant-find-account.png)

Pour s’inscrire à un compte Microsoft, les formateurs doivent accéder à la page [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Utiliser un compte GitHub
Les formateurs peuvent également utiliser un compte GitHub existant pour s’inscrire et se connecter à un labo de classe. Si le formateur a déjà un compte Microsoft lié à son compte GitHub, il peut se connecter et fournir son mot de passe, comme indiqué dans la section précédente. S’il n’a pas encore lié son compte GitHub à un compte Microsoft, il doit sélectionner **Options de connexion** :

![Lien Options de connexion](../media/how-to-configure-student-usage/signin-options.png)

Dans la page **Options de connexion**, sélectionnez **Se connecter avec GitHub**.

![Lien Se connecter avec GitHub](../media/how-to-configure-student-usage/signin-github.png)

Enfin, il est invité à créer un compte Microsoft qui est lié à son compte GitHub. Cela se produit automatiquement lorsque le formateur sélectionne **Suivant**.  Le formateur est alors immédiatement inscrit et connecté au labo de classe.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Arrêt automatique des machines virtuelles lors de la déconnexion
Vous pouvez activer ou désactiver l’arrêt automatique des machines virtuelles Lab Windows (modèle ou étudiant) après la déconnexion d’une connexion Bureau à distance. Vous pouvez également spécifier la durée pendant laquelle les machines virtuelles doivent attendre que l’utilisateur se reconnecte avant de s’arrêter automatiquement.

![Paramètre d’arrêt automatique au niveau du compte Lab](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Ce paramètre s’applique à tous les laboratoires créés dans le compte Lab. Un propriétaire de laboratoire peut remplacer ce paramètre au niveau du laboratoire. La modification apportée à ce paramètre au niveau du compte Lab n’affecte que les laboratoires créés après la modification.

Pour en savoir plus sur la façon dont un propriétaire de laboratoire peut configurer ce paramètre au niveau du laboratoire, consultez [cet article](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant que propriétaire de labo, configurer et contrôler l’utilisation d’un labo](how-to-configure-student-usage.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)
