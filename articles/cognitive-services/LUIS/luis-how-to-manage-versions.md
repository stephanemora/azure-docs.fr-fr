---
title: Gérer les versions dans les applications LUIS dans Azure | Microsoft Docs
description: Découvrez comment gérer les versions dans des applications Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226592"
---
# <a name="manage-versions"></a>Gérer les versions

Chaque fois que vous travaillez sur le modèle, créez une [version](luis-concept-version.md) différente de l’application. 

## <a name="set-active-version"></a>Définir la version active
Pour travailler avec ces versions, ouvrez votre application en sélectionnant son nom dans la page **My apps**, puis sélectionnez **Paramètres** dans la barre supérieure.

![Page Versions](./media/luis-how-to-manage-versions/settings.png)

La page **Paramètres** permet de configurer les paramètres de l’application entière, y compris les versions et les collaborateurs. 

## <a name="clone-a-version"></a>Cloner une version
1. Dans la page **Paramètres**, après les sections Paramètres de l’application et Collaborateurs, recherchez la ligne avec la version que vous souhaitez cloner. Sélectionnez les points de suspension (***...***) tout à fait à droite. 

    ![Propriétés de la ligne de la version](./media/luis-how-to-manage-versions/version-section.png)

2. Sélectionnez **Cloner** dans la liste.

    ![Choix des propriétés de la ligne de la version](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. Dans la boîte de dialogue **Cloner la version**, tapez un nom pour la nouvelle version, tel que « 0.2 ».

   ![Boîte de dialogue Cloner la version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > L’ID de la version doit être composé uniquement de caractères, chiffres ou de « . » et ne peut pas contenir plus de 10 caractères.
 
 Une nouvelle version avec le nom spécifié est créée et définie en tant que version active.
 
  ![La version est créée et ajoutée à la liste](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > Comme illustré dans l’image précédente, une version publiée est associée à une marque de couleur indiquant le type d’emplacement où elle a été publiée : Production (vert), Préparation (rouge) ou les deux (noir). Les dates de formation et de publication sont affichées pour chaque version publiée.

## <a name="set-active-version"></a>Définir la version active
1. Dans la page **Paramètres**, dans la liste **Versions**, sélectionnez les points de suspension (***...***) tout à fait à droite.

2. Dans la liste contextuelle, sélectionnez **Définir comme active**.

    ![Définir la version active](./media/luis-how-to-manage-versions/set-active-version.png)

    La version active est mise en surbrillance en bleu clair, comme indiqué dans la capture d’écran suivante :

    ![Version active](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>Importer une version
Vous pouvez importer une version à partir d’un fichier JSON. Une fois que vous importez une version, cette nouvelle version devient la version active.

**Importer une version :**

1. Dans la page **Paramètres**, cliquez sur le bouton **Importer une nouvelle version**.

    ![Bouton Importer](./media/luis-how-to-manage-versions/import-version.png)

2. Cliquez sur **Parcourir**, puis sélectionnez le fichier JSON.

    ![Boîte de dialogue Importer une version](./media/luis-how-to-manage-versions/import-version-dialog.png)

Vous n’avez qu’à définir un ID de version si la version dans le fichier JSON existe déjà dans l’application.

## <a name="export-version"></a>Exporter une version
Vous pouvez exporter une version dans un fichier JSON.

**Exporter une version :**

1. Dans la page **Paramètres**, dans la liste **Versions**, sélectionnez les points de suspension (***...***) tout à fait à droite.

2. Sélectionnez **Exporter** dans la liste contextuelle des actions et sélectionnez l’emplacement où vous souhaitez enregistrer le fichier.

## <a name="delete-a-version"></a>Supprimer une version
Vous pouvez supprimer des versions, mais vous devez conserver au moins une version de l’application. Vous pouvez supprimer toutes les versions à l’exception de la version active. 

1. Dans la page **Paramètres**, dans la liste **Versions**, sélectionnez les points de suspension (***...***) tout à fait à droite.

2. Sélectionnez **Supprimer** dans la liste contextuelle des actions et sélectionnez l’emplacement où vous souhaitez enregistrer le fichier.

    ![Confirmation de la suppression d’une version](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>Renommer une version
Vous pouvez renommer les versions tant que le nom de la version n’est pas déjà utilisé.  

1. Dans la page **Paramètres**, dans la liste **Versions**, sélectionnez les points de suspension (***...***) tout à fait à droite.

2. Sélectionnez **Renommer** dans la liste contextuelle des actions.

3. Entrez le nouveau nom de la version et sélectionnez **Terminé**.

    ![Confirmation du renommage d’une version](./media/luis-how-to-manage-versions/rename-popup.png) 
