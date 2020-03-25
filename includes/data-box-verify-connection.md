---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67177730"
---
Suivez ces étapes pour vous connecter au compte de stockage et vérifier la connexion.

1. Dans l’Explorateur Stockage, ouvrez la boîte de dialogue **Connexion au stockage Azure**. Dans la boîte de dialogue **Connexion au stockage Azure**, sélectionnez **Utiliser un nom et une clé de compte de stockage**.

    ![Tableau de bord Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Collez votre **Nom du compte** et **Clé du compte** (valeur de la clé 1 à partir de la page **Connexion et copie** dans l’interface utilisateur web locale). Sélectionnez Domaine des points de terminaison de stockage en tant que **Autre (Entrez ci-dessous)** , puis précisez le point de terminaison du service BLOB, comme indiqué ci-dessous. Cochez l’option **Utiliser HTTP** uniquement si le transfert s’effectue via *http*. Si vous utilisez *https*, laissez l’option désactivée. Sélectionnez **Suivant**.

    ![Tableau de bord Data Box](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Dans la boîte de dialogue **Résumé de la connexion**, vérifiez les informations fournies. Sélectionnez **Connecter**.

    ![Tableau de bord Data Box](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Le compte que vous avez correctement ajouté s’affiche dans le volet gauche de l’Explorateur Stockage avec (Externe, Autre) ajouté à son nom. Cliquez sur **Conteneurs d’objets Blob** pour afficher le conteneur.

    ![Tableau de bord Data Box](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
