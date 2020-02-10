---
title: Accéder à Azure VMware Solutions (AVS) – Portail
description: Décrit comment accéder à Azure VMware Solutions (AVS) à partir du portail Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015948"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Accédez à Azure VMware Solutions (AVS) à partir du portail Azure

L’authentification unique est prise en charge pour l’accès au portail AVS. Une fois connecté au portail Microsoft Azure, vous pouvez accéder au portail AVS sans avoir à vous connecter à nouveau. La première fois que vous accédez au portail AVS, vous êtes invité à autoriser l’application [Autorisation du Service AVS](#consent-to-avs-service-authorization-application). L’autorisation n’est effectuée qu’une seule fois.

## <a name="before-you-begin"></a>Avant de commencer

Les utilisateurs disposant des rôles **Propriétaire** et **Contributeur** peuvent accéder au portail AVS. Les rôles doivent être configurés sur le groupe de ressources dans lequel le service AVS est déployé. Ils peuvent également être configurés sur l’objet de service AVS. Pour en savoir plus sur la vérification de votre rôle, voir l’article [Voir les attributions de rôles](https://docs.microsoft.com/azure/role-based-access-control/check-access). Seuls les utilisateurs disposant de rôles **Propriétaire** et **Contributeur** intégrés peuvent accéder au portail AVS. Les rôles doivent être configurés sur l’abonnement. Pour en savoir plus sur la vérification de votre rôle, voir l’article [Voir les attributions de rôles](https://docs.microsoft.com/azure/role-based-access-control/check-access).

Si vous utilisez un rôle personnalisé, il doit inclure l’une des opérations suivantes sous ```Actions```.  Pour en savoir plus sur les rôles personnalisés, voir [Rôles personnalisés pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Si l’une des opérations fait partie de ```NotActions```, l’utilisateur ne peut pas accéder au portail AVS. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Accéder au portail AVS

1. Sélectionnez **Tous les services**.

2. Recherchez **Services AVS**.

3. Sélectionnez le service AVS sur lequel vous voulez créer votre cloud privé.

4. Sur la page **Vue d’ensemble**, cliquez sur **Go to the AVS Portal** (Accéder au portail AVS). Si vous accédez au portail AVS à partir du portail Azure pour la première fois, vous êtes invité à autoriser l’application [Autorisation du Service AVS](#consent-to-avs-service-authorization-application). 

    ![Lancer le portail AVS](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Si vous sélectionnez une opération de cloud privé (par exemple, la création ou le développement d’un cloud privé) directement depuis le portail Microsoft Azure, le portail AVS s’ouvre à la page indiquée.

Dans le portail AVS, sélectionnez **Accueil** dans le menu latéral pour afficher un récapitulatif des informations concernant votre cloud privé AVS. Les ressources et la capacité de votre cloud privé AVS sont affichées, ainsi que des alertes et les tâches nécessitant votre attention. Pour les tâches courantes, cliquez sur les icônes nommées en haut de la page.

![Page d’accueil](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Consentement pour l’application Autorisation du Service AVS

Si vous démarrez le portail AVS depuis le portail Azure pour la première fois, vous êtes invité à autoriser l’application Autorisation du Service AVS. Sélectionnez **Accepter** pour accorder les autorisations demandées et accéder au portail AVS.

![Consentement pour l’application Autorisation du Service AVS – Administrateurs](media/cloudsimple-azure-consent.png)

Si vous disposez du privilège Administrateur d’entreprise, vous pouvez donner votre consentement au nom de votre organisation. Sélectionnez **Consentement pour le compte de votre organisation**.

![Consentement pour l’application Autorisation du Service AVS – Administrateur général](media/cloudsimple-azure-consent-global-admin.png)

Si les autorisations dont vous disposez n’autorisent pas l’accès au portail AVS, contactez l’administrateur général de votre locataire afin d’obtenir les autorisations requises. Un administrateur d’entreprise peut donner son consentement au nom de votre organisation.

![Consentement pour l’application Autorisation du Service AVS – Nécessite des administrateurs](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un cloud privé](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Apprenez à [configurer un environnement de cloud privé](quickstart-create-private-cloud.md).
