---
title: Gérer les utilisateurs et rôles dans votre application Azure IoT Central | Microsoft Docs
description: Comment gérer les rôles et utilisateurs de votre application Azure IoT Central en qualité d’administrateur
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 937c4a4dbf976564cbf8dc562bdec3b328fc2bc0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72941454"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gérer les utilisateurs et rôles dans votre application Azure IoT Central

Cet article explique comment, en tant qu’administrateur, vous pouvez ajouter, modifier et supprimer des utilisateurs dans votre application Azure IoT Central et comment gérer des rôles dans votre application Azure IoT Central.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application.


## <a name="add-users"></a>Ajouter des utilisateurs

Chaque utilisateur doit avoir un compte d’utilisateur avant de pouvoir se connecter et accéder à une application Azure IoT Central. Les comptes Microsoft (MSA) et les comptes Azure Active Directory (Azure AD) sont pris en charge dans Azure IoT Central. Actuellement, les groupes Azure Active Directory ne sont pas pris en charge dans Azure IoT Central.

Pour plus d’informations, consultez les sections [Aide sur le compte Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) et [Démarrage rapide : Ajouter de nouveaux utilisateurs à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Pour ajouter un utilisateur à une application IoT Central, accédez à la page **Utilisateurs** dans la section **Administration**.

    ![Liste des utilisateurs](media/howto-administer/image1.png)

1. Pour ajouter un utilisateur, dans la page **Utilisateurs**, choisissez **+ Ajouter un utilisateur**.

1. Choisissez un rôle pour l’utilisateur dans la liste déroulante **Rôle**. En savoir plus sur les rôles dans la section [Gérer les rôles](#manage-roles) de cet article.

    ![Sélection de rôle](media/howto-administer/image3.png)

    > [!NOTE]
    >  Pour ajouter des utilisateurs en nombre, entrez les ID de tous les utilisateurs à ajouter en les séparant par des points-virgules. Choisissez un rôle dans le menu déroulant **Rôle**. Ensuite, sélectionnez **Enregistrer**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modifier les rôles attribués aux utilisateurs

Une fois attribués, les rôles ne sont plus modifiables. Pour modifier le rôle attribué à un utilisateur, supprimez cet utilisateur puis rajoutez-le avec un autre rôle.

> [!NOTE]
> Les rôles attribués sont spécifiques de l’application IoT Central et ne peuvent pas être gérés à partir du portail Azure.

## <a name="delete-users"></a>Suppression d’utilisateurs

Pour supprimer des utilisateurs, sélectionnez une ou plusieurs cases à cocher sur la page **Utilisateurs**. Puis sélectionnez **Supprimer**.

## <a name="manage-roles"></a>Gérer les rôles

Les rôles vous permettent de contrôler qui, au sein de votre organisation, peut effectuer différentes tâches dans IoT Central. Vous pouvez attribuer trois rôles différents aux utilisateurs de votre application.

### <a name="administrator"></a>Administrateur

Les utilisateurs dotés du rôle d’**Administrateur** ont accès à toutes les fonctionnalités d’une application.

L’utilisateur qui crée une application reçoit automatiquement le rôle **Administrateur**. Le rôle **Administrateur** doit toujours être attribué à au moins un utilisateur.

### <a name="application-builder"></a>Concepteur d’applications

Les utilisateurs dotés du rôle de **Concepteur d’applications** peuvent tout faire dans une application, sauf l’administrer. Les concepteurs peuvent créer, modifier et supprimer des modèles d’appareils et des appareils, gérer des ensembles d’appareils et exécuter les analyses et les tâches. Les concepteurs n’auront pas accès à la section **Administration** de l’application.

### <a name="application-operator"></a>Opérateur d’application

Les utilisateurs dans le rôle d’**Opérateur d’application** ne peuvent ni apporter des modifications aux modèles d’appareils, ni administrer l’application. Les opérateurs peuvent ajouter et supprimer des appareils, gérer des ensembles d’appareils et exécuter des analytiques et des travaux. Les opérateurs n’auront pas accès aux pages **Concepteur d’application** et **Administration**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les utilisateurs et les rôles dans Azure IoT Central, l’étape suivante suggérée consiste à en savoir plus sur l’[affichage de votre facture](howto-view-bill.md) dans Azure IoT Central.
