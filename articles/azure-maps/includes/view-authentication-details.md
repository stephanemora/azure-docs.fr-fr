---
title: Afficher les détails de l’authentification de Microsoft Azure Maps
description: Afficher le Portail Azure pour afficher les détails de l’authentification d’Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: ccc0e8c43001f161648032be7fbb83aec43cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803484"
---
Pour afficher les détails de l’authentification de votre compte Azure Maps dans le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez au menu du portail Azure. Sélectionnez **Toutes les ressources**, puis votre compte Azure Maps.

3. Sous **Paramètres** dans le volet gauche, sélectionnez **Authentification**.

    :::image type="content" border="true" source="../media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Informations sur l’authentification.":::

Trois valeurs sont créées lors de la création du compte Azure Maps. Elles sont utilisées pour prendre en charge deux types d’authentification dans Azure Maps :
- **Authentification Azure Active Directory** : Le`Client ID` représente le compte devant être utilisé pour les requêtes API REST. La valeur `Client ID` doit être stockée dans la configuration de l’application, puis être récupérée avant de formuler des requêtes HTTP Azure Maps qui utilisent l’authentification Azure AD.
- **Authentification par clé partagée** : Le `Primary Key` et `Secondary Key`sont utilisés en tant que clé d’abonnement pour l’authentification par Clé partagée. L’authentification par Clé partagée s’appuie sur la transmission des clés générées par les comptes Azure Maps lors de chaque requête envoyée à Azure Maps. Nous vous recommandons de régénérer régulièrement vos clés. Pour maintenir les connexions en cours lors de la régénération, deux clés sont fournies. Une clé peut être utilisée, tout en régénérant l’autre. Lorsque vous regénérez vos clés, vous devez mettre à jour toutes les applications qui accèdent à ce compte pour qu’elles utilisent les nouvelles clés. Pour plus d’informations, voir [Authentification avec Azure Maps](../azure-maps-authentication.md)
