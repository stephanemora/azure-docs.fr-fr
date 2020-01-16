---
title: Guide pratique pour ajouter un utilisateur en tant que propriétaire de labo dans Azure Lab Services
description: Cet article explique comment un administrateur peut ajouter à un labo un utilisateur en tant que propriétaire.
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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475251"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Guide pratique pour ajouter un utilisateur en tant que propriétaire d’un labo de classe dans Azure Lab Services
Cet article explique comment ajouter un utilisateur en tant que propriétaire d’un labo dans Azure Lab Services.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Ajouter un utilisateur au rôle de lecteur pour le compte Lab
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Tous les services**. Effectuez une recherche sur **Lab Services**, puis sélectionnez-le.
3. Sélectionnez votre **compte Lab** dans la liste. 
2. Dans la page **Compte Lab**, sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche. 
2. Dans la page **Contrôle d’accès (IAM)** , sélectionnez **Ajouter** dans la barre d’outils, puis **Ajouter une attribution de rôle**.

    ![Attribution de rôle pour le compte Lab ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Dans la page **Ajouter une attribution de rôle**, effectuez les étapes suivantes : 
    1. Sélectionnez **Lecteur** pour le **rôle**. 
    2. Sélectionnez l’utilisateur. 
    3. Sélectionnez **Enregistrer**. 

        ![Ajouter un utilisateur au rôle de lecteur pour le compte Lab ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Ajouter un utilisateur au rôle de lecteur pour le labo

1. De retour dans la page **Compte Lab**, sélectionnez **Tous les labs** dans le menu de gauche.
2. Sélectionnez le **labo** auquel vous voulez ajouter l’utilisateur en tant que propriétaire. 
    
    ![Sélectionner le laboratoire ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Dans la page **Lab**, sélectionnez **Contrôle d’accès (IAM)** dans le menu de gauche.
4. Dans la page **Contrôle d’accès (IAM)** , sélectionnez **Ajouter** dans la barre d’outils, puis **Ajouter une attribution de rôle**.
5. Dans la page **Ajouter une attribution de rôle**, effectuez les étapes suivantes : 
    1. Sélectionnez **Propriétaire** pour le **rôle**. 
    2. Sélectionnez l’utilisateur. 
    3. Sélectionnez **Enregistrer**. 

## <a name="next-steps"></a>Étapes suivantes
Vérifiez que l’utilisateur voit le labo quand il se connecte au [portail Lab Services](https://labs.azure.com).