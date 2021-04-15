---
title: Configurer l'authentification mutuelle sur Azure Application Gateway via le portail
description: Apprenez à configurer une instance d'Application Gateway pour bénéficier d'une authentification mutuelle via le portail
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230861"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Configurer l'authentification mutuelle sur Application Gateway via le portail (préversion)

Cet article explique comment utiliser le portail Azure pour configurer l'authentification mutuelle sur votre instance d'Application Gateway. L'authentification mutuelle signifie qu'Application Gateway authentifie le client qui envoie la demande à l'aide du certificat client que vous chargez sur Application Gateway. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer l'authentification mutuelle sur une instance d'Application Gateway, un certificat client doit être chargé sur la passerelle. Le certificat client sera utilisé pour valider le certificat que le client présentera à Application Gateway. Dans le cadre d’un test, vous pouvez utiliser un certificat auto-signé. Toutefois, ces derniers ne sont pas recommandés pour les charges de travail de production, car elles sont plus difficiles à gérer et ne sont pas entièrement sécurisées. 

Pour en savoir plus, en particulier sur le type de certificat client que vous pouvez charger, consultez [Présentation de l'authentification mutuelle sur Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Créer une passerelle Application Gateway

Commencez par créer une instance d'Application Gateway comme vous le feriez habituellement via le portail. Aucune étape supplémentaire n'est nécessaire lors du processus de création pour activer l'authentification mutuelle. Pour plus d'informations sur la création d'une instance d'Application Gateway via le portail, consultez le [Tutoriel de démarrage rapide sur le portail](./quick-create-portal.md).

## <a name="configure-mutual-authentication"></a>Configurer l'authentification mutuelle 

Pour configurer l'authentification mutuelle sur une instance existante d'Application Gateway, vous devez d'abord accéder à l'onglet **Paramètres SSL (préversion)** du portail et créer un nouveau profil SSL. Lorsque vous créez un profil SSL, deux onglets sont disponibles : **Authentification client** et **Stratégie SSL**. L'onglet **Authentification client** vous permet de charger votre certificat client. L'onglet **Stratégie SSL** vous permet de configurer une stratégie SSL spécifique à l'écouteur. Pour plus d'informations, consultez [Configurer une stratégie SSL spécifique à un écouteur](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Veillez à charger l'intégralité de la chaîne de certificats d'autorité de certification clients dans un même fichier, et une seule chaîne par fichier.

1. Recherchez **Application Gateway** sur le portail, sélectionnez **Passerelles d'application**, puis cliquez sur votre instance existante.

2. Sélectionnez **Paramètres SSL (préversion)** dans le menu de gauche.

3. Cliquez sur le signe plus (+) en regard de **Profils SSL**, en haut, pour créer un nouveau profil SSL.

4. Entrez un nom sous **Nom du profil SSL**. Dans cet exemple, nous nommons notre profil SSL *applicationGatewaySSLProfile*. 

5. Restez dans l'onglet **Authentification client**. Chargez le certificat PEM que vous comptez utiliser pour l'authentification mutuelle entre le client et l'instance d'Application Gateway à l'aide du bouton **Charger un nouveau certificat**. 

    Pour plus d'informations sur l'extraction de chaînes de certificats d'autorité de certification clients approuvés, consultez [Extraire des chaînes de certificats d'autorité de certification clients approuvés](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > S'il ne s'agit pas de votre premier profil SSL et que vous avez chargé d'autres certificats clients sur votre instance d'Application Gateway, vous pouvez choisir de réutiliser un certificat existant sur votre passerelle via le menu déroulant. 

6. Ne cochez la case **Vérifier le nom unique de l'émetteur du certificat client** que si vous souhaitez qu'Application Gateway vérifie le nom unique de l'émetteur du certificat client. 

7. Pensez à ajouter une stratégie spécifique à l'écouteur. Pour plus d'informations, consultez [Configurer des stratégies SSL spécifiques de l'écouteur](./application-gateway-configure-listener-specific-ssl-policy.md).

8. Sélectionnez **Ajouter** pour enregistrer.
    > [!div class="mx-imgBorder"]
    > ![Ajouter l'authentification client au profil SSL](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Associer le profil SSL à un écouteur

Maintenant que nous avons créé un profil SSL configuré pour l'authentification mutuelle, nous devons associer le profil SSL à l'écouteur afin de finaliser la configuration de l'authentification mutuelle. 

1. Accédez à votre instance existante d'Application Gateway. Si vous venez de suivre les étapes ci-dessus, vous n'avez rien à faire ici. 

2. Dans le menu de gauche, sélectionnez **Écouteurs**. 

3. Cliquez sur **Ajouter un écouteur** si vous n'avez pas encore configuré d'écouteur HTTPS. Si vous disposez déjà d'un écouteur HTTPS, cliquez sur celui-ci dans la liste. 

4. Renseignez les champs **Nom de l'écouteur**, **Adresse IP du front-end**, **Port**, **Protocole**, et d'autres **Paramètres HTTPS** en fonction de vos besoins.

5. Cochez la case **Activer le profil SSL** pour pouvoir sélectionner le profil SSL à associer à l'écouteur. 

6. Sélectionnez le profil SSL que vous venez de créer dans la liste déroulante. Dans cet exemple, nous choisissons le profil SSL que nous avons créé lors des étapes précédentes : *applicationGatewaySSLProfile*. 

7. Poursuivez la configuration de l'écouteur en fonction de vos besoins. 

8. Cliquez sur **Ajouter** pour enregistrer votre nouvel écouteur avec le profil SSL qui lui est associé. 

    > [!div class="mx-imgBorder"]
    > ![Associer le profil SSL au nouvel écouteur](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Renouveler les certificats d'autorité de certification clients qui ont expiré

Si votre certificat d'autorité de certification client a expiré, vous pouvez le mettre à jour sur votre passerelle en procédant comme suit : 

1. Accédez à votre instance d'Application Gateway, puis à l'onglet **Paramètres SSL (préversion)** dans le menu de gauche. 
 
1. Sélectionnez le ou les profils SSL existants contenant le certificat client qui a expiré. 
 
1. Sélectionnez **Charger un nouveau certificat** sous l'onglet **Authentification client** et chargez votre nouveau certificat client. 
 
1. Sélectionnez l’icône de la corbeille en regard du certificat qui a expiré. Cette opération supprime l'association entre ce certificat et le profil SSL. 

1. Répétez les étapes 2 à 4 ci-dessus avec tout autre profil SSL utilisant le même certificat client obsolète. Vous pourrez choisir le nouveau certificat chargé à l'étape 3 dans le menu déroulant des autres profils SSL.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)