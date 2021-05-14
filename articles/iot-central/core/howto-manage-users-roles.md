---
title: Gérer les utilisateurs et rôles dans votre application Azure IoT Central | Microsoft Docs
description: Comment gérer les rôles et utilisateurs de votre application Azure IoT Central en qualité d’administrateur
author: lmasieri
ms.author: lmasieri
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: cff8830d180b0c234e54f7578ed9fafafeb598f0
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719166"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gérer les utilisateurs et rôles dans votre application Azure IoT Central

Cet article explique comment, en tant qu’administrateur, vous pouvez ajouter, modifier et supprimer des utilisateurs dans votre application Azure IoT Central. L’article explique également comment gérer des rôles dans votre application.

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application.

## <a name="add-users"></a>Ajouter des utilisateurs

Chaque utilisateur doit disposer d’un compte d’utilisateur pour pouvoir se connecter à une application et y accéder. IoT Central prend actuellement en charge les comptes Microsoft et les comptes Azure Active Directory, mais pas les groupes Azure Active Directory.

Pour en savoir plus, consultez [Aide sur le compte Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) et [Démarrage rapide : Ajouter ou supprimer des utilisateurs à l’aide d’Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Pour ajouter un utilisateur à une application IoT Central, accédez à la page **Utilisateurs** dans la section **Administration**.

  :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Gérer les utilisateurs":::

1. Pour ajouter un utilisateur, dans la page **Utilisateurs**, choisissez **+ Ajouter un utilisateur**.

1. Choisissez un rôle pour l’utilisateur dans la liste déroulante **Rôle**. En savoir plus sur les rôles dans la section [Gérer les rôles](#manage-roles) de cet article.

  :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Ajouter un utilisateur et sélectionner un rôle.":::

  > [!NOTE]
  > Un utilisateur qui se trouve dans un rôle personnalisé qui lui accorde l’autorisation d’ajouter d’autres utilisateurs peut uniquement ajouter des utilisateurs à un rôle avec des autorisations identiques ou moins nombreuses que son propre rôle.

  > [!NOTE]
  > Si un utilisateur est supprimé d’Azure Active Directory puis rajouté, il ne peut pas se connecter à l’application IoT Central. Pour réactiver l’accès, l’administrateur de l’application doit également supprimer et rajouter l’utilisateur dans l’application.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modifier les rôles attribués aux utilisateurs

Une fois attribués, les rôles ne sont plus modifiables. Pour modifier le rôle attribué à un utilisateur, supprimez cet utilisateur puis rajoutez-le avec un autre rôle.

> [!NOTE]
> Les rôles attribués sont spécifiques à l’application IoT Central et ne peuvent pas être gérés à partir du portail Azure.

## <a name="delete-users"></a>Suppression d’utilisateurs

Pour supprimer des utilisateurs, sélectionnez une ou plusieurs cases à cocher sur la page **Utilisateurs**. Puis sélectionnez **Supprimer**.

## <a name="manage-roles"></a>Gérer les rôles

Les rôles vous permettent de contrôler qui, au sein de votre organisation, peut effectuer différentes tâches dans IoT Central. Vous pouvez attribuer trois rôles intégrés aux utilisateurs de votre application. Vous pouvez également [créer des rôles personnalisés](#create-a-custom-role) si vous avez besoin d’un contrôle plus fin.

> [!div class="mx-imgBorder"]
> ![Gérer la sélection de rôles](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Administrateur

Les utilisateurs du rôle **Administrateur** peuvent gérer et contrôler toutes les parties de l’application, notamment la facturation.

L’utilisateur qui crée une application reçoit automatiquement le rôle **Administrateur**. Le rôle **Administrateur** doit toujours être attribué à au moins un utilisateur.

### <a name="builder"></a>Générateur

Les utilisateurs du rôle **Créateur** peuvent gérer toutes les parties de l’application, mais ne peuvent pas changer les onglets Administration ou Exportation continue des données.

### <a name="operator"></a>Opérateur

Les utilisateurs du rôle **Opérateur** peuvent surveiller l’intégrité et l’état des appareils. Ils ne sont pas autorisés à apporter des modifications aux modèles d’appareils ni à administrer l’application. Les opérateurs peuvent ajouter et supprimer des appareils, gérer des ensembles d’appareils et exécuter des analytiques et des travaux.

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Si votre solution nécessite des contrôles d’accès plus précis, vous pouvez créer des rôles avec des ensembles d’autorisations personnalisés. Pour créer un rôle personnalisé, accédez à la page **Rôle** dans la section **Administration** de votre application. Sélectionnez ensuite **+ Nouveau rôle**, puis ajoutez un nom et une description pour votre rôle. Sélectionnez les autorisations requises par votre rôle, puis choisissez **Enregistrer**.

Vous pouvez ajouter des utilisateurs à votre rôle personnalisé de la même façon que vous ajoutez des utilisateurs à un rôle intégré.

> [!div class="mx-imgBorder"]
> ![Créer un rôle personnalisé](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Options de rôle personnalisé

Lorsque vous définissez un rôle personnalisé, vous choisissez le jeu d’autorisations qu’un utilisateur reçoit s’il est membre du rôle. Certaines autorisations dépendent les unes des autres. Par exemple, si vous ajoutez l’autorisation **Mettre à jour les tableaux de bord personnels** à un rôle, l’autorisation **Afficher les tableaux de bord personnels** est automatiquement ajoutée. Les tableaux suivants résument les autorisations disponibles et leurs dépendances, que vous pouvez utiliser lors de la création de rôles personnalisés.

#### <a name="managing-devices"></a>Gestion des appareils

**Autorisations du modèle d’appareil**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Gérer | Affichage <br/> Autres dépendances : Afficher les instances d’appareil  |
| Contrôle total | Afficher, Gérer <br/> Autres dépendances : Afficher les instances d’appareils |

**Autorisations des instances d’appareils**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Créer | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Exécuter des commandes | Mettre à jour, Afficher <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Voir les données brutes | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer, Exécuter des commandes, Afficher les données brutes <br/> Autres dépendances : Afficher les modèles d’appareils et les groupes d’appareils  |

**Autorisations des groupes d’appareils**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils   |
| Créer | Afficher, Mettre à jour <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils   |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils  |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer <br/> Autres dépendances : Afficher les modèles d’appareils et les instances d’appareils |

**Autorisations de gestion de la connectivité des appareils**

| Nom | Les dépendances |
| ---- | -------- |
| Lire l’instance | None <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les instances d’appareils |
| Gérer l’instance | Lire l’instance <br /> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les instances d’appareils |
| Lire au niveau global | None   |
| Gérer au niveau global | Lire au niveau global |
| Contrôle total | Lire l’instance, Gérer l’instance, Lire au niveau global, Gérer au niveau global <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les instances d’appareils |

**Autorisations de travaux**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| Créer | Afficher, Mettre à jour <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils |
| Execute | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils ; Mettre à jour les instances d’appareils ; Exécuter des commandes sur des instances d’appareils |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer, Exécuter <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils et les groupes d’appareils ; Mettre à jour les instances d’appareils ; Exécuter des commandes sur des instances d’appareils |

**Autorisations de règles**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les modèles d’appareils |
| Update | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils |
| Créer | Afficher, Mettre à jour <br/> Autres dépendances : Afficher les modèles d’appareils |
| DELETE | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer <br/> Autres dépendances : Afficher les modèles d’appareils |

#### <a name="managing-the-app"></a>Gestion de l’application

**Autorisations des paramètres d’application**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Copier | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils, les groupes d’appareils, les tableaux de bord, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Copier, Supprimer <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les tableaux de bord d’application, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |

**Autorisations d’exportation du modèle d’application**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Exporter | Affichage <br/> Autres dépendances : Afficher les modèles d’appareils, les instances d’appareils, les groupes d’appareils, les tableaux de bord, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |
| Contrôle total | Afficher, Exporter <br/> Autres dépendances : Afficher les modèles d’appareils, les groupes d’appareils, les tableaux de bord d’application, l’exportation de données, la personnalisation, les liens d’aide, les rôles personnalisés, les règles |

**Autorisations de chargement de fichier d’appareil**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Gérer | Affichage   |
| Contrôle total | Afficher, Gérer |

**Autorisations de facturation**

| Nom | Les dépendances |
| ---- | -------- |
| Gérer | None     |
| Contrôle total | Gérer |

#### <a name="managing-users-and-roles"></a>Gestion des utilisateurs et des rôles

**Autorisations de rôles personnalisés**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None |
| Update | Affichage |
| Créer | Afficher, Mettre à jour |
| DELETE | Affichage |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations de gestion des utilisateurs**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None <br/> Autres dépendances : Afficher les rôles personnalisés |
| Ajouter | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| DELETE | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| Contrôle total | Afficher, Ajouter, Supprimer <br/> Autres dépendances : Afficher les rôles personnalisés |

> [!NOTE]
> Un utilisateur qui se trouve dans un rôle personnalisé qui lui accorde l’autorisation d’ajouter d’autres utilisateurs peut uniquement ajouter des utilisateurs à un rôle avec des autorisations identiques ou moins nombreuses que son propre rôle.

#### <a name="customizing-the-app"></a>Personnalisation de l’application

**Autorisations des tableaux de bord d’applications**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Créer | Afficher, Mettre à jour |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations des tableaux de bord personnels**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Créer | Afficher, Mettre à jour   |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations Marque, Icône favorite et Couleurs**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Contrôle total | Afficher, Mettre à jour |

**Autorisations de liens d’aide**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Contrôle total | Afficher, Mettre à jour |

#### <a name="extending-the-app"></a>Extension de l’application

**Autorisations d’exportation de données**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None     |
| Update | Affichage   |
| Créer | Afficher, Mettre à jour  |
| DELETE | Affichage   |
| Contrôle total | Afficher, Mettre à jour, Créer, Supprimer |

**Autorisations de jeton d’API**

| Nom | Les dépendances |
| ---- | -------- |
| Affichage | None  <br/> Autres dépendances : Afficher les rôles personnalisés |
| Créer | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| DELETE | Affichage <br/> Autres dépendances : Afficher les rôles personnalisés |
| Contrôle total | Afficher, Créer, Supprimer <br/> Autres dépendances : Afficher les rôles personnalisés |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer les utilisateurs et les rôles dans votre application IoT Central, l’étape suivante suggérée est d’apprendre à [gérer votre facture](howto-view-bill.md).