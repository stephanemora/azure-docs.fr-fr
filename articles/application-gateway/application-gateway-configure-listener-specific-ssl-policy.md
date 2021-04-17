---
title: Configurer des stratégies SSL spécifiques à un écouteur sur Azure Application Gateway via le portail
description: Apprenez à configurer des stratégies SSL spécifiques à un écouteur sur Azure Application Gateway via le portail
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230894"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Configurer des stratégies SSL spécifiques à une écouteur sur Application Gateway via le portail (Préversion)

Cet article explique comment utiliser le portail Azure pour configurer des stratégies SSL spécifiques à un écouteur sur votre instance d’Application Gateway. Les stratégies SSL spécifiques à l’écouteur vous permettent de configurer des écouteurs spécifiques pour utiliser différentes stratégies SSL indépendantes les unes des autres. Vous pourrez toujours définir une stratégie SSL par défaut qui sera utilisée par tous les écouteurs, sauf si elles sont remplacées par la stratégie SSL spécifique à un écouteur. 

> [!NOTE]
> Seules les références SKU Standard_v2 et WAF_v2 prennent en charge les stratégies spécifiques à un écouteur, car les stratégies spécifiques à un écouteur font partie des profils SSL et les profils SSL sont uniquement pris en charge sur les passerelles v2. 



Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-new-application-gateway"></a>Créer une passerelle Application Gateway

Commencez par créer une instance d'Application Gateway comme vous le feriez habituellement via le portail. Aucune étape supplémentaire n'est nécessaire lors du processus de création pour configurer les stratégies SSL spécifiques à un écouteur. Pour plus d'informations sur la création d'une instance d'Application Gateway via le portail, consultez le [Tutoriel de démarrage rapide sur le portail](./quick-create-portal.md).

## <a name="set-up-a-listener-specific-ssl-policy"></a>Configurer une stratégie SSL spécifique à un écouteur

Pour configurer une stratégie SSL spécifique à un écouteur, vous devez d’abord accéder à l’onglet **Paramètres SSL (Préversion)** dans le portail, puis créer un nouveau profil SSL. Lorsque vous créez un profil SSL, deux onglets sont disponibles : **Authentification client** et **Stratégie SSL**. L’onglet **Stratégie SSL** permet de configurer une stratégie SSL spécifique à un écouteur. L’onglet **authentification du client** permet de télécharger un ou plusieurs certificats clients pour l’authentification mutuelle. pour plus d’informations, consultez [Configuration d’une authentification mutuelle](./mutual-authentication-portal.md).

> [!NOTE]
> Nous vous recommandons d’utiliser la norme TLS 1.2, car elle sera obligatoire à l’avenir. 

1. Recherchez **Application Gateway** sur le portail, sélectionnez **Passerelles d'application**, puis cliquez sur votre instance existante.

2. Sélectionnez **Paramètres SSL (préversion)** dans le menu de gauche.

3. Cliquez sur le signe plus (+) en regard de **Profils SSL**, en haut, pour créer un nouveau profil SSL.

4. Entrez un nom sous **Nom du profil SSL**. Dans cet exemple, nous nommons notre profil SSL *applicationGatewaySSLProfile*. 

5. Accédez à l’onglet **Stratégie SSL** et cochez la case **Activer la stratégie SSL spécifique à un écouteur**. 

6. Configurez votre stratégie SSL spécifique à un écouteur selon vos besoins. Vous pouvez choisir entre des stratégies SSL prédéfinies et personnaliser votre propre stratégie SSL. Pour plus d’informations sur les stratégies SSL, consultez [Présentation de la stratégie TLS Application Gateway](./application-gateway-ssl-policy-overview.md). Nous vous recommandons d’utiliser la norme TLS 1.2

7. Sélectionnez **Ajouter** pour enregistrer.

    > [!NOTE]
    > Vous n’avez pas besoin de configurer l’authentification du client sur un profil SSL pour l’associer à un écouteur. Vous pouvez configurer uniquement l’authentification du client, ou uniquement la stratégie SSL spécifique à un écouteur, ou configurer les deux dans votre profil SSL.  

    ![Ajouter une stratégie SSL spécifique à un écouteur au profil SSL](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Associer le profil SSL à un écouteur

Maintenant que nous avons créé un profil SSL avec une stratégie SSL spécifique à un écouteur, nous devons associer le profil SSL à l’écouteur pour mettre en action la stratégie spécifique pour cet écouteur. 

1. Accédez à votre instance existante d'Application Gateway. Si vous venez de suivre les étapes ci-dessus, vous n'avez rien à faire ici. 

2. Dans le menu de gauche, sélectionnez **Écouteurs**. 

3. Cliquez sur **Ajouter un écouteur** si vous n'avez pas encore configuré d'écouteur HTTPS. Si vous disposez déjà d'un écouteur HTTPS, cliquez sur celui-ci dans la liste. 

4. Renseignez les champs **Nom de l'écouteur**, **Adresse IP du front-end**, **Port**, **Protocole**, et d'autres **Paramètres HTTPS** en fonction de vos besoins.

5. Cochez la case **Activer le profil SSL** pour pouvoir sélectionner le profil SSL à associer à l'écouteur. 

6. Sélectionnez le profil SSL que vous venez de créer dans la liste déroulante. Dans cet exemple, nous choisissons le profil SSL que nous avons créé lors des étapes précédentes : *applicationGatewaySSLProfile*. 

7. Poursuivez la configuration de l'écouteur en fonction de vos besoins. 

8. Cliquez sur **Ajouter** pour enregistrer votre nouvel écouteur avec le profil SSL qui lui est associé. 

    ![Associer le profil SSL au nouvel écouteur](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)