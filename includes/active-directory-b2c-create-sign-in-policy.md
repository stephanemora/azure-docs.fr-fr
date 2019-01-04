---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742321"
---
Si vous souhaitez uniquement activer la connexion dans votre application, vous utilisez un flux d’utilisateur de **connexion**. Ce flux d’utilisateur décrit les expériences clients lors de la connexion, ainsi que le contenu des jetons que l’application reçoit en cas de connexion réussie.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
Sous **Gérer**, sélectionnez **Flux d’utilisateurs**.

Cliquez sur +**Nouveau flux utilisateur** en haut du panneau.

Sous **Sélectionner un type de flux utilisateur**, sélectionnez **Tous**, puis sélectionnez la version de **connexion** que vous souhaitez utiliser.

Le **Nom** détermine le nom du flux utilisateur de connexion utilisé par votre application. Par exemple, entrez **SiIn**.

Sous **Fournisseurs d’identité**, sélectionnez une option. Vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés. Cliquez sur **OK**.

Sous **Revendications d'application**, cliquez sur **Afficher plus**.

Dans la colonne **Revendication de retour**, choisissez les revendications à renvoyer à votre application dans les jetons après une expérience de connexion réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal** et **ID d’objet de l’utilisateur**. Cliquez sur **OK**.

Cliquez sur **Créer**. Notez que le flux utilisateur qui vient d’être créé s’affiche sous la forme **B2C_1_SiIn** (le fragment **B2C\_1\_** est automatiquement ajouté).

Cliquez sur **Exécuter le flux d'utilisateur**.

Sélectionnez **Contoso B2C app** dans le menu déroulant **Application** et `https://localhost:44321/` dans le menu déroulant **URL de réponse**.

Cliquez sur **Exécuter le flux d'utilisateur**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous connecter à votre application.

> [!NOTE]
> La création du flux utilisateur et les mises à jour peuvent prendre jusqu’à une minute.
>