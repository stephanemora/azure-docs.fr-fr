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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443417"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Configurer des comptes de laboratoire dans Azure Lab Services 
Dans Azure Lab Services, un compte de laboratoire est un conteneur pour les types de laboratoires gérés tels que les laboratoires de classe. Un administrateur configure un compte de laboratoire avec Azure Lab Services et fournit l’accès à tous les propriétaires de laboratoire qui peuvent alors créer des laboratoires dans leur compte. 

Cet article décrit comment exécuter les tâches suivantes : 

- Spécifier une plage d’adresses pour les machines virtuelles du laboratoire
- Configurer l’arrêt automatique des machines virtuelles lors de la déconnexion

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Spécifier une plage d’adresses pour les machines virtuelles du laboratoire
La procédure suivante comporte des étapes pour spécifier une plage d’adresses pour les machines virtuelles dans le laboratoire. Si vous mettez à jour la plage que vous avez spécifiée précédemment, la plage d’adresses modifiée s’applique uniquement aux machines virtuelles qui sont créées après que la modification a été effectuée. 

Voici quelques restrictions à garder à l’esprit lors de la spécification de la plage d’adresses. 

- Le préfixe doit être inférieur ou égal à 23. 
- Si un réseau virtuel est associé au compte de laboratoire, la plage d’adresses fournie ne peut pas chevaucher une plage d’adresses de réseau virtuel associé.

1. Dans la page **Compte lab**, sélectionnez **Paramètres lab** dans le menu de gauche.
2. Pour le champ **Plage d’adresses**, spécifiez la plage d’adresses pour les machines virtuelles qui seront créées dans le laboratoire. La plage d’adresses doit être mentionnée dans la notation CIDR (Classless Inter-Domain Routing), par exemple : 10.20.0.0/23). Les machines virtuelles du laboratoire seront créées dans cette plage d’adresses.
3. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Configurer la plage d’adresses](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Arrêt automatique des machines virtuelles lors de la déconnexion
Vous pouvez activer ou désactiver l’arrêt automatique des machines virtuelles Lab Windows (modèle ou étudiant) après la déconnexion d’une connexion Bureau à distance. Vous pouvez également spécifier la durée pendant laquelle les machines virtuelles doivent attendre que l’utilisateur se reconnecte avant de s’arrêter automatiquement.

![Paramètre d’arrêt automatique au niveau du compte Lab](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Ce paramètre s’applique à tous les laboratoires créés dans le compte Lab. Un propriétaire de laboratoire peut remplacer ce paramètre au niveau du laboratoire. La modification apportée à ce paramètre au niveau du compte Lab n’affecte que les laboratoires créés après la modification.

Pour en savoir plus sur la façon dont un propriétaire de laboratoire peut configurer ce paramètre au niveau du laboratoire, consultez [cet article](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [Autoriser un créateur de labo à choisir l’emplacement du labo](allow-lab-creator-pick-lab-location.md)
- [Connecter un réseau lab avec un réseau virtuel pair](how-to-connect-peer-virtual-network.md)
- [Attacher une galerie d’images partagées à un lab](how-to-attach-detach-shared-image-gallery.md)
- [Ajouter un utilisateur comme propriétaire de labo](how-to-add-user-lab-owner.md)
- [Afficher les paramètres de pare-feu pour un labo](how-to-configure-firewall-settings.md)
