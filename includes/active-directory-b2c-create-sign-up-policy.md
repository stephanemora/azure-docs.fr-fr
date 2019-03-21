---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58115693"
---
Si vous souhaitez uniquement activer l’inscription dans votre application, vous utilisez un flux utilisateur **d’inscription**. Ce flux utilisateur décrit les expériences clients lors de l’inscription, ainsi que le contenu des jetons que l’application reçoit en cas de réussite de l’opération.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Sous **Gérer**, sélectionnez **Flux utilisateurs**.

Cliquez sur +**Nouveau flux utilisateur** en haut du panneau.

Sous **Sélectionner un type de flux utilisateur**, sélectionnez **Tous**, puis sélectionnez la version de **l’inscription** que vous souhaitez utiliser.

Le **Nom** détermine le nom du flux utilisateur d'inscription utilisé par votre application. Par exemple, entrez **SiUp**.

Sous **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**. Si vous le souhaitez, vous pouvez également sélectionner des fournisseurs d'identité sociaux, s'ils sont déjà configurés.

Sous **Attributs utilisateur et revendications**, cliquez sur **Afficher plus**.

Dans la colonne **Collecter l'attribut**, choisissez les attributs que vous souhaitez collecter auprès du consommateur lors de l’inscription. Par exemple, sélectionnez **Pays/région**, **Nom d’affichage** et **Code postal**.

Dans la colonne **Revendication de retour**, choisissez les revendications à renvoyer à votre application dans les jetons après une expérience d’inscription réussie. Par exemple, sélectionnez **Nom d’affichage**, **Fournisseur d’identité**, **Code postal**, **Nouvel utilisateur** et **ID d’objet de l’utilisateur**.

Cliquez sur **OK**.

Cliquez sur **Créer**. Le flux utilisateur créé s’affiche sous la forme **B2C_1_SiUp** (le fragment **B2C\_1\_** est automatiquement ajouté).

Cliquez sur **Exécuter le flux utilisateur**.

Sélectionnez **Contoso B2C app** dans le menu déroulant **Application** et `https://localhost:44321/` dans le menu déroulant **URL de réponse**.

Cliquez sur **Exécuter le flux utilisateur**. Un nouvel onglet de navigateur s’ouvre et vous pouvez vivre l’expérience du client consistant à vous inscrire à votre application.

> [!NOTE]
> La création du flux utilisateur et les mises à jour peuvent prendre jusqu’à une minute.
>