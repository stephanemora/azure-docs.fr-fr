---
title: Démarrage rapide - Configurer le chiffrement SSL de bout en bout avec Azure Application Gateway - Portail Azure | Microsoft Docs
description: Découvrez comment utiliser le portail Azure pour créer une Azure Application Gateway avec chiffrement SSL de bout en bout.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: bd165f81b45e3ae0c121fb8876ed88e68d493195
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64946793"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurer le chiffrement SSL de bout en bout avec Application Gateway et le portail

Cet article vous montre comment utiliser le portail Azure pour configurer le chiffrement SSL de bout en bout avec une passerelle d’application v1.  

> [!NOTE]
> Application Gateway v2 requiert des certificats racine approuvés pour permettre la configuration de bout en bout. Le portail ne peut pas encore ajouter de certificats racines approuvés. Par conséquent, en cas de SKU v2, consultez [Configurer le protocole SSL de bout en bout avec PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer un chiffrement SSL de bout en bout avec une passerelle d’application, un certificat est requis pour la passerelle et des certificats sont requis pour les serveurs principaux. Le certificat de passerelle est utilisé pour dériver une clé symétrique conformément aux spécifications du protocole SSL. La clé symétrique est ensuite utilisée pour chiffrer et déchiffrer le trafic envoyé à la passerelle. Pour le chiffrement SSL de bout en bout, le serveur principal doit figurer sur la liste approuvée par la passerelle d’application. Pour ce faire, chargez le certificat public des serveurs principaux, aussi appelés certificats d’authentification, sur la passerelle d’application. L’ajout du certificat permet à la passerelle d’application de communiquer uniquement avec des instances de serveur principal connues. Il sécurise la communication de bout en bout.

Pour plus d’information, consultez [Arrêt SSL et SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Créer une passerelle d’application avec SSL de bout en bout

Pour créer une passerelle d’application avec chiffrement SSL de bout en bout, vous devez d’abord activer un arrêt SSL lors de la création d’une passerelle d’application. Cette opération active le chiffrement SSL pour la communication entre le client et l’application de passerelle. Ensuite, vous devez autoriser les certificats pour les serveurs principaux dans les paramètres HTTP afin d’activer le chiffrement SSL pour la communication entre les serveurs de passerelle et les serveurs principaux, pour obtenir un chiffrement SSL de bout en bout.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Activer l’arrêt SSL lors de la création d’une passerelle d’application

Consultez cet article pour comprendre comment [activer l’arrêt SSL lors de la création d’une passerelle d’application](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Autorisation des certificats pour les serveurs principaux

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Paramètres HTTP** dans le menu de gauche. Azure a automatiquement créé un paramètre HTTP par défaut, appelé **appGatewayBackendHttpSettings**, quand vous avez créé la passerelle d’application. 

3. Sélectionnez **appGatewayBackendHttpSettings**.

4. Sous **Protocole**, sélectionnez **HTTPS**. Un volet **Certificats d’authentification de serveur principal** s’affiche. 

5. Sous **Certificats d’authentification de serveur principal**, choisissez **Créer**.

6. Entrez un **Nom**.

7. Télécharger le certificat à l’aide de **Charger le certificat CER** box.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Le certificat fourni dans cette étape doit être la clé publique du certificat .pfx présent sur le serveur principal. Exportez le certificat (pas le certificat racine) installé sur le serveur principal au format .CER (Claim, Evidence, and Reasoning) et utilisez-le dans cette étape. Cette étape permet d’ajouter le serveur principal à la liste approuvée par la passerelle d’application.

8. Sélectionnez **Enregistrer**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Activer le protocole SSL de bout en bout pour la passerelle d’application existante

Pour configurer une passerelle d’application existante avec le chiffrement SSL de bout en bout, vous devez d’abord activer un arrêt SSL sur l’écouteur. Cette opération active le chiffrement SSL pour la communication entre le client et l’application de passerelle. Ensuite, vous devez autoriser les certificats pour les serveurs principaux dans les paramètres HTTP afin d’activer le chiffrement SSL pour la communication entre les serveurs de passerelle et les serveurs principaux, pour obtenir un chiffrement SSL de bout en bout.

Vous devez utiliser un écouteur avec le protocole et un certificat HTTPS pour activer la terminaison SSL. Vous ne pouvez pas modifier le protocole d’un écouteur existant. Par conséquent, vous pouvez choisir d’utiliser un écouteur existant avec le certificat et le protocole HTTPS ou bien créer un écouteur. Si vous choisissez la première solution, vous pouvez ignorer les étapes qui suivent pour **activer l’arrêt SSL dans la passerelle d’application existante** et passer directement à la section **Autorisation des certificats pour les serveurs principaux**. Si vous choisissez la dernière option, suivez ces étapes.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Activer l’arrêt SSL sur une passerelle d’application existante

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Dans le menu de gauche, sélectionnez **Écouteurs**.

3. Choisissez un écouteur de **base** ou **multisite** en fonction de vos besoins.

4. Sous **Protocole**, sélectionnez **HTTPS**. Un volet de **certificat** s’affiche.

5. Chargez le certificat PFX que vous souhaitez utiliser pour l’arrêt SSL entre le client et la passerelle d’application.

   > [!NOTE]
   > Dans le cadre d’un test, vous pouvez utiliser un certificat auto-signé. N’utilisez pas de certificat auto-signé pour les charges de travail de production. Découvrez comment [créer un certificat auto-signé](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Ajoutez les autres paramètres requis pour l’**écouteur** en fonction de vos besoins.

7. Sélectionnez **OK** pour enregistrer.

### <a name="whitelist-certificates-for-backend-servers"></a>Autorisation des certificats pour les serveurs principaux

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **Paramètres HTTP** dans le menu de gauche. Vous pouvez soit autoriser les certificats dans un paramètre de serveur principal HTTP, soit créer un paramètre HTTP. Dans la procédure suivante, nous allons autoriser le certificat pour le paramètre HTTP par défaut, **appGatewayBackendHttpSettings**.

3. Sélectionnez **appGatewayBackendHttpSettings**.

4. Sous **Protocole**, sélectionnez **HTTPS**. Un volet **Certificats d’authentification de serveur principal** s’affiche. 

5. Sous **Certificats d’authentification de serveur principal**, choisissez **Créer**.

6. Entrez un **Nom**.

7. Télécharger le certificat à l’aide de **Charger le certificat CER** box.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Le certificat fourni dans cette étape doit être la clé publique du certificat .pfx présent sur le serveur principal. Exportez le certificat (pas le certificat racine) installé sur le serveur principal au format .CER (Claim, Evidence, and Reasoning) et utilisez-le dans cette étape. Cette étape permet d’ajouter le serveur principal à la liste approuvée par la passerelle d’application.

8. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)
