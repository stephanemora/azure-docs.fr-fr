---
title: Administrer une application Azure IoT Central | Microsoft Docs
description: Comment administrer votre application Azure IoT Central en qualité d’administrateur
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a43febf1e78f80451b6aeed19e095b2c313d3216
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284851"
---
# <a name="how-to-administer-your-application"></a>Guide pratique pour administrer votre application

Après avoir créé une application Microsoft Azure IoT Central, vous pouvez utiliser la section **Administration** de l’interface utilisateur Azure IoT Central pour l’administrer. Pour accéder à la section **Administration**, choisissez **Administration** dans le menu de navigation gauche.

La section **Administration** vous permet de :

- Gestion des utilisateurs

- Gérer les rôles

- Consulter les informations de facturation

- Gérer les paramètres d’application

- Prolonger un essai gratuit

Dans la section **Administration**, le menu de navigation secondaire contient des liens qui donnent accès aux diverses tâches d’administration.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application. La section *Gestion des utilisateurs* de cet article vous explique plus en détail comment attribuer le rôle **Administrateur** à d’autres utilisateurs.

## <a name="change-application-name"></a>Changer le nom de l’application

Pour changer le nom de votre application, utilisez le menu de navigation secondaire pour accéder à la page **Paramètres de l’application** dans la section **Administration**.

Dans la page **Paramètres de l’application**, entrez le nom de votre choix dans le champ **Nom de l’application**, puis choisissez **Enregistrer**.

## <a name="change-the-application-url"></a>Changer l’URL de l’application

Pour changer l’URL de votre application, utilisez le menu de navigation secondaire pour accéder à la page **Paramètres de l’application** dans la section **Administration**.

![Page Paramètres de l’application](media\howto-administer\image0-a.png)

Dans la page **Paramètres de l’application**, entrez l’URL de votre choix dans le champ **URL**, puis choisissez **Enregistrer**. La longueur de votre URL ne doit pas dépasser 200 caractères. Si l’URL n’est pas disponible, vous obtenez une erreur de validation.

> [!Note]
> Si vous changez votre URL, l’ancienne peut être prise par un autre client Azure IoT Central. Dans ce cas, vous ne pouvez plus l’utiliser. Quand vous changez votre URL, l’ancienne ne fonctionne plus et vous devez indiquer à vos utilisateurs la nouvelle URL à utiliser.

## <a name="change-the-application-image"></a>Changer l’image de l’application

Pour plus d’informations sur l’utilisation d’images dans une application Azure IoT Central, consultez [Préparer des images et les charger dans votre application Azure IoT Central](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copier une application

Vous pouvez créer une copie de n’importe quelle application, à l’exception des instances d’appareils, de l’historique des données d’un appareil et des données utilisateur. Cette copie est une application payante qui vous sera facturée. Vous ne pouvez pas créer une application d’essai en copiant une autre application.

Pour copier une application, accédez à la page **Paramètres d’application**, puis cliquez sur le bouton **Copier**.

![Page Paramètres d’application](media\howto-administer\appCopy1.png)

En cliquant sur le bouton **Copier**, vous ouvrez une boîte de dialogue dans laquelle vous pouvez sélectionner le nom, l’URL, le répertoire AAD, l’abonnement et la région Azure de la nouvelle application qui sera créée en copiant votre application. Sélectionnez les valeurs de chacun de ces champs, puis cliquez sur le bouton **Copier** pour confirmer que vous voulez poursuivre. Plus d’informations sur les valeurs à saisir, consultez l’article expliquant [comment créer une application](howto-create-application.md).

![Page Paramètres d’application](media\howto-administer\appCopy2.png)

Une fois l’opération de copie terminée, vous pourrez accéder à la nouvelle application créée à partir de la copie de votre application en cliquant sur le lien qui apparaît sur la page **Paramètres d’application**.

![Page Paramètres d’application](media\howto-administer\appCopy3.png)

> [!Note]
> La copie d’une application copie la définition des règles ou des actions. Toutefois, étant donné que les utilisateurs ayant accès à votre application d’origine ne sont pas transférés vers l’application copiée, vous devrez ajouter manuellement des utilisateurs à des actions nécessitant des utilisateurs, par exemple l’envoi d’un e-mail.

## <a name="delete-an-application"></a>Supprimer une application

Pour supprimer votre application, utilisez le menu de navigation secondaire pour accéder à la page **Paramètres de l’application** de la section **Administration**.

Choisissez **Supprimer**.

> [!Note]
> La suppression d’une application supprime définitivement toutes les données associées à l’application. Pour pouvoir supprimer une application, vous devez aussi disposer des droits nécessaires à la suppression des ressources de l’abonnement Azure que vous avez choisi au moment de créer l’application. Pour en savoir plus, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Rôles dans Azure IoT Central

Les rôles vous permettent de contrôler qui, au sein de votre organisation, peut effectuer les diverses tâches Azure IoT Central. Dans Azure IoT Central, vous pouvez attribuer trois rôles différents aux utilisateurs de votre application. Les rôles sont attribués au niveau de chaque application. Un même utilisateur peut avoir des rôles différents d’une application à une autre. Vous pouvez lui attribuer plusieurs rôles au sein d’une même application.

### <a name="administrator"></a>Administrateur

Les utilisateurs dotés du rôle **Administrateur** ont accès à toutes les fonctionnalités d’une application Azure IoT Central.

L’utilisateur qui crée une application se voit automatiquement attribuer le rôle **Administrateur**. Le rôle **Administrateur** doit toujours être attribué à au moins un utilisateur.

### <a name="application-builder"></a>Concepteur d’applications

Les utilisateurs dotés du rôle **Concepteur d’applications** peuvent tout faire dans un application Azure IoT Central hormis administrer l’application.

### <a name="application-operator"></a>Opérateur d’application

Les utilisateurs dotés du rôle **Opérateur d’applications** n’ont pas accès à la page **Concepteur d’applications**. Ils ne peuvent pas administrer l’application.

## <a name="manage-users"></a>Gestion des utilisateurs

Les administrateurs d’une application peuvent attribuer des rôles à ses utilisateurs.

### <a name="add-users"></a>Ajouter des utilisateurs

Chaque utilisateur doit avoir un compte d’utilisateur avant de pouvoir se connecter et accéder à une application Azure IoT Central. Les comptes Microsoft (MSA) et les comptes Azure Active Directory (AAD) sont pris en charge dans Azure IoT Central. Les groupes Azure Active Directory ne sont actuellement pas pris en charge dans Azure IoT Central.

Pour en savoir plus, consultez [Aide de Compte Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) et [Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Pour ajouter un compte d’utilisateur à une application Azure IoT Central, utilisez le menu de navigation secondaire pour accéder à la page **Utilisateurs** dans la section **Administration**.

    ![Liste des utilisateurs](media\howto-administer\image1.png)

1. Dans la page **Utilisateurs**, choisissez **+ Ajouter un utilisateur** pour ajouter un utilisateur.

    ![Ajouter un utilisateur](media\howto-administer\image2.png)

1. Au moment d’ajouter un utilisateur à votre application Azure IoT Central, choisissez un rôle pour l’utilisateur dans la liste déroulante **Rôle**. Découvrez plus en détail les rôles dans la section *Rôles dans Azure IoT Central* de cet article.

    ![Sélection de rôle](media\howto-administer\image3.png)

    > [!NOTE]
    >  Pour ajouter des utilisateurs en bloc, entrez les ID d’utilisateur de tous les utilisateurs à ajouter en les séparant par des points-virgules. Choisissez un rôle dans la liste déroulante **Rôle**, puis **Enregistrer**.

1. Une fois qu’un utilisateur est ajouté, son entrée apparaît dans la page **Utilisateurs**.

    ![Liste d’utilisateurs](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Modifier les rôles attribués aux utilisateurs

Une fois attribués, les rôles ne peuvent plus être modifiés. Pour modifier le rôle attribué à un utilisateur, supprimez l’utilisateur et rajoutez-le avec un autre rôle.

### <a name="delete-users"></a>Suppression d’utilisateurs

Pour supprimer des utilisateurs, cochez une ou plusieurs cases dans la page **Utilisateurs**, puis choisissez **Supprimer**.

## <a name="view-your-bill"></a>Consulter votre facture

Pour consulter votre facture, accédez à la page **Facturation** de la section **Administration**, puis choisissez **Facturation**. La page de facturation Azure s’ouvre dans un nouvel onglet où figure la facture correspondant à chacune de vos applications Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Convertir votre application en version d’essai en application payante

Une fois que vous avez évalué IoT Central, vous pouvez convertir votre application en version d’essai en application payante. Pour mener à bien ce processus en libre-service, effectuez ces étapes :

1. Utilisez le menu de navigation secondaire pour accéder à la page **Facturation** dans la section **Administration**. Si vous n’avez pas prolongé votre version d’essai, la page se présente comme suit :

    ![État d’essai gratuit](media/howto-administer/freetrial.png)

2. Cliquez sur **Convertir en application payante**. Si vous n’avez pas prolongé votre version d’essai, la fenêtre contextuelle se présente comme suit :

    Dans la fenêtre contextuelle, sélectionnez le locataire Azure Active Directory approprié, ainsi que l’abonnement Azure que vous souhaitez utiliser pour votre application IoT Central.

    ![Prolonger un essai gratuit](media/howto-administer/extend.png)

3. Une fois que vous avez cliqué sur **Convertir**, votre version d’essai est convertie en application payante et vous commencez à être facturé.

## <a name="extend-your-free-trial"></a>Prolonger votre essai gratuit

Par défaut, tous les essais gratuits sont disponibles pendant 7 jours. Si vous voulez porter votre essai à 30 jours, procédez comme suit :

1. Utilisez le menu de navigation secondaire pour accéder à la page **Facturation** dans la section **Administration** :

1. Cliquez sur **Prolonger l’essai**. Dans la fenêtre contextuelle, sélectionnez le locataire Azure Active Directory approprié, ainsi que l’abonnement Azure à utiliser pour votre application IoT Central :

1. Cliquez ensuite sur **Prolonger**. Votre version d’essai est maintenant valide pendant 30 jours.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment administrer votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Configurer le modèle d’appareil](howto-set-up-template.md)