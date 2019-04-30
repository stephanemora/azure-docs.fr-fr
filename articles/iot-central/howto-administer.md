---
title: Administrer une application Azure IoT Central | Microsoft Docs
description: En tant qu’administrateur, l’administration de votre application Azure IoT Central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 29ded279e2a76940049c257b954b1dae75f14836
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118899"
---
# <a name="administer-your-iot-central-application"></a>Administrer votre application IoT Central

Après avoir créé une application IoT Central, vous pouvez accéder à la section **Administration** pour effectuer les opérations suivantes :

- Gérer les paramètres d’application
- Gestion des utilisateurs
- Gérer les rôles
- Consulter votre facture
- Convertir votre version d'évaluation en abonnement avec paiement à l’utilisation
- Exporter des données
- Gérer la connexion de l’appareil
- Utiliser des jetons d’accès

Pour accéder à la section **Administration** et l’utiliser, vous devez avoir le rôle **Administrateur** dans l’application Azure IoT Central. Si vous créez une application Azure IoT Central, le rôle **Administrateur** vous est automatiquement attribué pour cette application. La section [Gérer les utilisateurs](#manage-users) de cet article vous explique plus en détail comment attribuer le rôle d’**Administrateur** à d’autres utilisateurs.

## <a name="manage-application-settings"></a>Gérer les paramètres d’application

### <a name="change-application-name-and-url"></a>Modifier le nom et l’URL de l’application
À la page **Paramètres d’application**, vous pouvez modifier le nom et l’URL de votre application, puis sélectionner **enregistrer**.

![Page Paramètres de l’application](media/howto-administer/image0-a.png)

> [!Note]
> Si vous changez votre URL, l’ancienne peut être prise par un autre client Azure IoT Central. Dans ce cas, vous ne pouvez plus l’utiliser. Quand vous changez votre URL, l’ancienne ne fonctionne plus et vous devez indiquer à vos utilisateurs la nouvelle URL à utiliser.

### <a name="prepare-and-upload-image"></a>Préparer et charger des images
Pour modifier l’image de l’application, consultez [Préparer et charger des images dans votre application Azure IoT Central](howto-prepare-images.md).

### <a name="copy-an-application"></a>Copier une application
Vous pouvez créer une copie de n’importe quelle application, à l’exception des instances d’appareils, de l’historique des données d’un appareil et des données utilisateur. La copie est une application de paiement à l’utilisation qui vous est facturée. Impossible de créer une version d’évaluation de cette manière.

Sélectionnez **copie**. Dans la boîte de dialogue, entrez les détails de la nouvelle application avec paiement à l’utilisation. Puis sélectionnez **copie** pour confirmer que vous voulez poursuivre. En savoir plus sur les champs de ce formulaire dans le guide de démarrage rapide [Créer une application](quick-deploy-iot-central.md).

![Page Paramètres de l’application](media/howto-administer/appcopy2.png)

Une fois que l’opération de copie a abouti, vous pouvez accéder à la nouvelle application créée en copiant votre application à l’aide du lien qui s’affiche.

![Page Paramètres de l’application](media/howto-administer/appCopy3.png)

> [!Note]
> La copie d’une application copie également la définition des règles et des actions. Mais comme les utilisateurs ayant accès à votre application initiale ne sont pas transférés à l’application copiée, vous devrez ajouter manuellement des utilisateurs à aux actions nécessitant des utilisateurs, par exemple l’envoi d’un e-mail. En général, il est judicieux de vérifier les règles et les actions pour vous assurer qu’elles sont à jour dans la nouvelle application.

### <a name="delete-an-application"></a>Supprimer une application

> [!Note]
> Pour pouvoir supprimer une application, vous devez aussi disposer des autorisations permettant de supprimer des ressources de l’abonnement Azure que vous avez choisi lors de la création de l’application. Pour en savoir plus, consultez [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources de votre abonnement Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Utilisez le bouton **Supprimer** pour supprimer définitivement votre application IoT Central. Cette opération définitivement supprime toutes les données qui sont associé à cette application.

## <a name="manage-users"></a>Gestion des utilisateurs

### <a name="add-users"></a>Ajouter des utilisateurs

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

### <a name="delete-users"></a>Suppression d’utilisateurs

Pour supprimer des utilisateurs, sélectionnez une ou plusieurs cases à cocher sur la page **Utilisateurs**. Puis sélectionnez **Supprimer**.

## <a name="manage-roles"></a>Gérer les rôles

Les rôles vous permettent de contrôler qui, au sein de votre organisation, peut effectuer différentes tâches dans IoT Central. Vous pouvez attribuer trois rôles différents aux utilisateurs de votre application.

### <a name="administrator"></a>Administrateur

Les utilisateurs dotés du rôle d’**Administrateur** ont accès à toutes les fonctionnalités d’une application.

L’utilisateur qui crée une application reçoit automatiquement le rôle **Administrateur**. Le rôle **Administrateur** doit toujours être attribué à au moins un utilisateur.

### <a name="application-builder"></a>Concepteur d’applications

Les utilisateurs dotés du rôle de **Concepteur d’applications** peuvent tout faire dans une application, sauf l’administrer. Cela signifie que les concepteurs peuvent créer, modifier et supprimer des modèles d’appareils et des appareils, gérer des ensembles d’appareils et exécuter les analyses et les tâches. Les concepteurs n’auront pas accès à la section **Administration** de l’application.

### <a name="application-operator"></a>Opérateur d’application

Les utilisateurs dans le rôle d’**Opérateur d’application** ne peuvent ni apporter des modifications aux modèles d’appareils, ni administrer l’application. Cela signifie que les opérateurs peuvent ajouter et supprimer des appareils, gérer des ensembles d’appareils et exécuter des analyses et des tâches. Les opérateurs n’auront pas accès aux pages **Concepteur d’application** et **Administration**.

## <a name="view-your-bill"></a>Consulter votre facture

Pour consulter votre facture, accédez à la page **Facturation** de la section **Administration**. La page de facturation Azure s’ouvre dans un nouvel onglet où figure la facture de chacune de vos applications Azure IoT Central.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Convertir votre version d'évaluation en abonnement avec paiement à l’utilisation

Vous pouvez convertir votre version d’évaluation de l’application en application avec paiement à l’utilisation. Voici les différences entre ces types d’applications.

- Les applications **d’évaluation** sont gratuites pendant 7 jours avant leur expiration. Elles peuvent être passées en paiement à l’utilisation à tout moment avant leur expiration.
- Les applications avec **paiement à l’utilisation** sont facturées par appareil : les 5 premiers sont gratuits.

Apprenez-en davantage au sujet de la tarification sur la [page de tarification d’Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Pour mener à bien ce processus en libre-service, effectuez ces étapes :

1. Accédez à la page **Facturation** de la section **Administration**.

    ![État de la version d'évaluation](media/howto-administer/freetrialbilling.png)

1. Sélectionnez **convertir en paiement à l’utilisation**.

    ![Convertir la version d'évaluation](media/howto-administer/convert.png)

1. Sélectionnez le service Azure Active Directory approprié, ainsi que l’abonnement Azure à utiliser pour votre application avec paiement à l'utilisation.

1. Une fois que vous sélectionnez **convertir**, votre application est maintenant un paiement à l’utilisation et commencer à être facturé.

## <a name="export-data"></a>Exporter des données

Vous pouvez activer l’**exportation continue de données** pour exporter des mesures, des appareils et des données de modèles d’appareils vers votre compte de stockage blob Azure. En savoir plus sur l'[exportation de vos données](howto-export-data.md).

## <a name="manage-device-connection"></a>Gérer la connexion de l’appareil

Connecter des appareils à l’échelle dans votre application à l’aide de clés et de certificats ici. En savoir plus sur la [connexion d’appareils](concepts-connectivity.md).

## <a name="use-access-tokens"></a>Utiliser des jetons d’accès

Générer des jetons d’accès pour les utiliser dans les outils de développeurs. Actuellement il est un outil de développement disponible qui est l’Explorateur IoT Central pour l’analyse des messages d’appareil et les modifications dans les propriétés et les paramètres. En savoir plus sur l’[explorateur IoT Central](howto-use-iotc-explorer.md).

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Utiliser les kits de développement logiciel (SDK) Azure pour les opérations de plan de contrôle

Des packages de kit de développement logiciel (SDK) Azure Resource Manager d’IoT Central sont disponibles pour Node, Python, C#, Ruby, Java et Go. Ces bibliothèques prennent en charge les opérations de plan de contrôle pour IoT Central, qui vous permettent de créer, répertorier, mettre à jour et supprimer des applications IoT Central. Elles fournissent également des programmes d’assistance pour gérer l’authentification et les erreurs, selon des modalités propres à chaque langage. 

Vous trouverez des exemples montrant comment utiliser les kits de développement logiciel (SDK) Azure Resource Manager à l’adresse [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Pour plus d’informations, consultez ces packages sur GitHub.

| Langage | Référentiel | Package |
| ---------| ---------- | ------- |
| Nœud | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment administrer votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Configurer le modèle d’appareil](howto-set-up-template.md)
