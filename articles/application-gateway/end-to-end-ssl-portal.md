---
title: Guide de démarrage rapide - configurer le chiffrement SSL de bout en bout avec Azure Application Gateway - portail Azure | Microsoft Docs
description: Découvrez comment utiliser le portail Azure pour créer une passerelle d’Application Azure avec chiffrement SSL de bout en bout.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831949"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurer SSL de bout en bout à l’aide de la passerelle d’Application avec le portail

Cet article vous montre comment utiliser le portail Azure pour configurer le chiffrement SSL de bout en bout avec une passerelle d’application v1 référence (SKU).  

> [!NOTE]
> V2 référence SKU de la passerelle application requiert des certificats racine approuvés pour la configuration de bout en bout pour l’activation. Support concernant le portail pour ajouter des certificats racines de confiance n’est pas encore disponible. Par conséquent, en cas de référence (SKU) V2 consultez [configurer SSL de bout en bout à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer un chiffrement SSL de bout en bout avec une passerelle d’application, un certificat est requis pour la passerelle et des certificats sont requis pour les serveurs principaux. Le certificat de passerelle est utilisé pour dériver une clé symétrique conformément aux spécifications du protocole SSL. La clé symétrique est ensuite utilisée pour chiffrer et déchiffrer le trafic envoyé à la passerelle. Pour le chiffrement SSL de bout en bout, le serveur principal doit figurer sur la liste approuvée par la passerelle d’application. Pour ce faire, téléchargez le certificat public des serveurs principaux, également appelés certificats d’authentification, à la passerelle d’application. L’ajout du certificat permet à la passerelle d’application de communiquer uniquement avec des instances de serveur principal connues. Il sécurise la communication de bout en bout.

Pour plus d’informations, consultez [SSL arrêt et SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Créer une passerelle d’application avec SSL de bout en bout

Pour créer une passerelle d’application avec chiffrement SSL de bout en bout, vous devez tout d’abord activer l’arrêt de SSL lors de la création d’une passerelle d’application. Cela active le chiffrement SSL pour la communication entre le client et l’application de passerelle. Ensuite, vous devrez les certificats d’autorisation pour les serveurs principaux dans les paramètres HTTP pour activer le chiffrement SSL pour la communication entre les serveurs de passerelle et backend application, par conséquent, pour accomplir le chiffrement SSL de bout en bout.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Activer l’arrêt SSL lors de la création d’une passerelle d’application

Consultez cet article pour comprendre comment [activer l’arrêt SSL lors de la création d’une passerelle d’application](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Liste verte des certificats pour les serveurs principaux

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **paramètres HTTP** dans le menu de gauche. Azure a automatiquement créé un paramètre HTTP par défaut, **appGatewayBackendHttpSettings**, lorsque vous avez créé la passerelle d’application. 

3. Sélectionnez **appGatewayBackendHttpSettings**.

4. Sous **protocole**, sélectionnez **HTTPS**. Un volet pour **certificats d’authentification Backend** s’affiche. 

5. Sous **certificats d’authentification de serveur principal**, choisissez **créer un nouveau**.

6. Entrez approprié **nom**.

7. Télécharger le certificat en utilisant le **certificat CER télécharger** boîte.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Le certificat fourni dans cette étape doit être la clé publique du certificat .pfx présent sur le serveur principal. Exportez le certificat (pas le certificat racine) installé sur le serveur principal au format .CER (Claim, Evidence, and Reasoning) et utilisez-le dans cette étape. Cette étape permet d’ajouter le serveur principal à la liste approuvée par la passerelle d’application.

8. Sélectionnez **Enregistrer**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Activer le protocole SSL de bout en bout pour la passerelle d’application existante

Pour configurer une passerelle d’application existante avec chiffrement SSL de bout en bout, vous devrez première activer un arrêt SSL dans l’écouteur. Cela active le chiffrement SSL pour la communication entre le client et l’application de passerelle. Ensuite, vous devrez les certificats d’autorisation pour les serveurs principaux dans les paramètres HTTP pour activer le chiffrement SSL pour la communication entre les serveurs de passerelle et backend application, par conséquent, pour accomplir le chiffrement SSL de bout en bout.

Vous devez utiliser un écouteur avec le protocole HTTPS et le certificat pour l’activation d’une terminaison SSL. Vous ne pouvez pas modifier le protocole d’un écouteur existant. Par conséquent, vous pouvez choisir d’utiliser un écouteur existant avec le certificat et le protocole HTTPS ou créer un écouteur. Si vous choisissez l’ancienne base de données, vous pouvez ignorer la comme suit pour **arrêt activer SSL dans la passerelle d’application existante** et passer directement à **liste verte des certificats pour les serveurs principaux** section. Si vous choisissez la dernière option, procédez comme suit. 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Activer l’arrêt SSL dans la passerelle d’application existante

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **écouteurs** dans le menu de gauche.

3. Choisissez entre **base** et **multisite** écouteur selon vos besoins.

4. Sous **protocole**, sélectionnez **HTTPS**. Un volet pour **certificat** s’affiche.

5. Téléchargez le certificat PFX que vous souhaitez utiliser pour un arrêt SSL entre la passerelle client et l’application.

   > [!NOTE]
   > À des fins de test, vous pouvez utiliser un certificat auto-signé. Vous ne devez pas utiliser un certificat auto-signé pour les charges de travail de production. Découvrez comment [créer un certificat auto-signé](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Ajouter d’autres paramètres requis pour le **écouteur** selon vos besoins.

7. Sélectionnez **OK** pour enregistrer.

### <a name="whitelist-certificates-for-backend-servers"></a>Liste verte des certificats pour les serveurs principaux

1. Sélectionnez **Toutes les ressources**, puis **myAppGateway**.

2. Sélectionnez **paramètres HTTP** dans le menu de gauche. Vous pouvez soit les certificats d’autorisation dans un serveur principal HTTP définissant ou en créer un nouveau paramètre HTTP. Dans la procédure suivante, nous allons certificat d’autorisation pour le paramètre HTTP par défaut, **appGatewayBackendHttpSettings**.

3. Sélectionnez **appGatewayBackendHttpSettings**.

4. Sous **protocole**, sélectionnez **HTTPS**. Un volet pour **certificats d’authentification Backend** s’affiche. 

5. Sous **certificats d’authentification de serveur principal**, choisissez **créer un nouveau**.

6. Entrez approprié **nom**.

7. Télécharger le certificat en utilisant le **certificat CER télécharger** boîte.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > Le certificat fourni dans cette étape doit être la clé publique du certificat .pfx présent sur le serveur principal. Exportez le certificat (pas le certificat racine) installé sur le serveur principal au format .CER (Claim, Evidence, and Reasoning) et utilisez-le dans cette étape. Cette étape permet d’ajouter le serveur principal à la liste approuvée par la passerelle d’application.

8. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)
