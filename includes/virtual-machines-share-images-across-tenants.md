---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cb7cf932433927d4ead853952ff74d55c55b9cf7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160019"
---
Galeries d’images partagé vous permettent d’images de partage à l’aide de RBAC. Vous pouvez utiliser RBAC pour partager des images au sein de votre client et même aux personnes en dehors de votre client. Toutefois, si vous souhaitez partager des images en dehors de votre client Azure, à grande échelle, vous devez créer une inscription d’application pour faciliter le partage.  À l’aide d’une inscription d’application peut activer des scénarios de partage plus complexes, telles que : 

* La gestion d’images partagées lors de l’acquisition d’un autre, et l’infrastructure Azure est réparti entre les locataires séparés. 
* Les partenaires Azure gérer l’infrastructure Azure pour le compte de leurs clients. Personnalisation d’images s’effectue au sein du client de partenaires, mais les déploiements d’infrastructure se produira dans le client. 


## <a name="create-the-app-registration"></a>Créer l’inscription d’application

Créer une inscription d’application qui servira par les deux clients de partager les ressources de galerie d’image.
1. Ouvrez le [inscriptions d’application (version préliminaire) dans le portail Azure](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Sélectionnez **nouvelle inscription** dans le menu en haut de la page.
1. Dans **nom**, type *myGalleryApp*.
1. Dans **pris en charge les types de comptes**, sélectionnez **comptes dans n’importe quel annuaire d’organisation et les comptes Microsoft personnels**.
1. Dans **URI de redirection**, type *https://www.microsoft.com* , puis sélectionnez **inscrire**. Une fois l’inscription d’application a été créée, la page de vue d’ensemble s’ouvre.
1. Dans la page Vue d’ensemble, copiez la **ID d’Application (client)** et l’enregistrer pour une utilisation ultérieure.   
1. Sélectionnez **certificats et clés secrètes**, puis sélectionnez **nouvelle clé secrète client**.
1. Dans **Description**, type *partagé image galerie inter-clients secret de l’application*.
1. Dans **expiration**, conservez la valeur par défaut **dans 1 an** , puis sélectionnez **ajouter**.
1. Copiez la valeur de la clé secrète et enregistrez-le dans un endroit sûr. Vous ne pouvez pas récupérer une fois que vous quittez la page.


Donner l’autorisation de l’inscription d’application à utiliser la galerie d’images partagé.
1. Dans le portail Azure, sélectionnez la galerie d’images partagé que vous souhaitez partager avec un autre client.
1. Sélectionnez **sélectionnez contrôle d’accès (IAM)**, puis, sous **ajouter une attribution de rôle** sélectionnez *ajouter*. 
1. Sous **rôle**, sélectionnez **lecteur**.
1. Sous **attribuer l’accès à :**, laissez-la en **utilisateur, groupe ou principal du service Azure AD**.
1. Sous **sélectionnez**, type *myGalleryApp* et sélectionnez-le lorsqu’il s’affiche dans la liste. Lorsque vous avez terminé, sélectionnez **enregistrer**.


Créer un principal de service pour 2 client à utiliser pour accéder à l’application en demandant une connexion à l’aide d’un navigateur. Remplacez *<Tenant2 ID>* avec l’ID de client pour le locataire que vous aimeriez partager avec votre galerie d’images. Remplacez *< ID d’Application (client) >* avec l’ID d’application de l’inscription d’application que vous avez créé. Lorsque vous avez terminé effectue les remplacements, collez l’URL dans un navigateur et suivez les invites de connexion pour se connecter à un locataire 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Dans le [Azure portal](https://portal.azure.com) connectez-vous en tant que client 2 et donner l’accès de l’inscription d’application au groupe de ressources dans lequel vous souhaitez créer la machine virtuelle.

1. Sélectionnez le groupe de ressources, puis **contrôle d’accès (IAM)**. Sous **ajouter une attribution de rôle** sélectionnez **ajouter**. 
1. Sous **rôle**, type **contributeur**.
1. Sous **attribuer l’accès à :**, laissez-la en **utilisateur, groupe ou principal du service Azure AD**.
1. Sous **sélectionnez** type *myGalleryApp* puis sélectionnez-la lorsqu’elle s’affiche dans la liste. Lorsque vous avez terminé, sélectionnez **enregistrer**.

> [!NOTE]
> Vous devez attendre que la version de l’image se termine complètement les cours de génération et de réplication avant de pouvoir utiliser la même image managée pour créer une autre version de l’image.

