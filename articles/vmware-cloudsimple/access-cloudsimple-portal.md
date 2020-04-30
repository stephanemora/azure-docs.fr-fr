---
title: Accès Azure VMware Solution by CloudSimple - Portail
description: Explique comment accéder au portail Azure VMware Solution by CloudSimple, à partir du portail Microsoft Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869329"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Accès au portail VMware Solution by CloudSimple à partir du portail Azure

L’authentification unique est prise en charge pour l’accès au portail CloudSimple. Une fois connecté au portail Microsoft Azure, vous pouvez accéder au portail CloudSimple sans avoir à vous connecter à nouveau. La première fois que vous accédez au portail CloudSimple, vous êtes invité à autoriser l’application [Autorisation du Service CloudSimple](#consent-to-cloudsimple-service-authorization-application).  L’autorisation n’est effectuée qu’une seule fois.

## <a name="before-you-begin"></a>Avant de commencer

Les utilisateurs disposant des rôles **Propriétaire** et **Contributeur** peuvent accéder au portail CloudSimple.  Les rôles doivent être configurés sur le groupe de ressources dans lequel le service CloudSimple est déployé.  Ils peuvent également être configurés sur l’objet de service CloudSimple.  Pour en savoir plus sur la vérification de votre rôle, consultez l’article [Voir les attributions de rôles](https://docs.microsoft.com/azure/role-based-access-control/check-access). Seuls les utilisateurs disposant de rôles **Propriétaire** et **Contributeur** intégrés peuvent accéder au portail CloudSimple.  Les rôles doivent être configurés sur l’abonnement.  Pour en savoir plus sur la vérification de votre rôle, consultez l’article [Voir les attributions de rôles](https://docs.microsoft.com/azure/role-based-access-control/check-access).

Si vous utilisez un rôle personnalisé, il doit inclure l’une des opérations suivantes sous ```Actions```.  Pour en savoir plus sur les rôles personnalisés, voir [Rôles personnalisés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Si l’une des opérations ne fait pas partie de ```NotActions```, l’utilisateur ne peut pas accéder au portail CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accéder au portail CloudSimple

1. Sélectionnez **Tous les services**.

2. Recherchez **Services CloudSimple**.

3. Sélectionnez le service CloudSimple où vous voulez créer votre cloud privé.

4. Sur la page **Vue d’ensemble**, cliquez sur **Go to the CloudSimple Portal** (Accéder au portail CloudSimple).  Si vous accédez au portail CloudSimple depuis le portail Microsoft Azure pour la première fois, vous êtes invité à autoriser l’application [Autorisation du Service CloudSimple](#consent-to-cloudsimple-service-authorization-application). 

    ![Lancer le portail CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Si vous sélectionnez une opération de cloud privé (par exemple, la création ou le développement d’un cloud privé) directement depuis le portail Microsoft Azure, le portail CloudSimple s’ouvre à la page indiquée.

Dans le portail CloudSimple, sélectionnez **Accueil** dans le menu latéral pour afficher un récapitulatif des informations concernant vos Clouds privés. Les ressources et la capacité de vos Clouds privés sont affichées, ainsi que des alertes et les tâches qui nécessitent votre attention. Pour les tâches courantes, cliquez sur les icônes nommées en haut de la page.

![Page d’accueil](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentement pour l’application Autorisation du Service CloudSimple

Si vous démarrez le portail CloudSimple depuis le portail Microsoft Azure pour la première fois, vous êtes invité à autoriser l’application Autorisation du Service CloudSimple.  Sélectionnez **Accepter** pour accorder les autorisations demandées et accéder au portail CloudSimple.

![Consentement pour l’application Autorisation du Service CloudSimple - Administrateurs](media/cloudsimple-azure-consent.png)

Si vous disposez du privilège Administrateur d’entreprise, vous pouvez donner votre consentement au nom de votre organisation.  Sélectionnez **Consentement pour le compte de votre organisation**.

![Consentement pour l’application Autorisation du Service CloudSimple - Administrateur d’entreprise](media/cloudsimple-azure-consent-global-admin.png)

Si les autorisations dont vous disposez n’autorisent pas l’accès au portail CloudSimple, contactez l’administrateur d’entreprise de votre locataire afin d’obtenir les autorisations requises.  Un administrateur d’entreprise peut donner son consentement au nom de votre organisation.

![Consentement pour l’application Autorisation du Service CloudSimple - Nécessite des administrateurs](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/).
* Apprenez à [configurer un environnement de cloud privé](quickstart-create-private-cloud.md).
