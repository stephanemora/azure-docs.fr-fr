---
title: Node.js, application Angular utilisant l’API d’Azure Cosmos DB pour MongoB (Partie 1)
description: Découvrez comment créer une application MongoDB avec Angular et Node sur Azure Cosmos DB à l’aide des mêmes API que celles utilisées pour MongoDB en suivant cette série de didacticiels vidéo.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: ec1275027c0a145da59e44017d596229d04e1298
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038186"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb"></a>Créer une application Angular avec l’API Azure Cosmos DB pour MongoDB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Ce tutoriel en plusieurs parties montre comment créer une application écrite en Node.js avec Express et Angular, puis comment la connecter à votre [compte Cosmos configuré avec l’API de Cosmos DB pour MongoDB](mongodb-introduction.md).

Azure Cosmos DB est la base de données NoSQL rapide de Microsoft, avec des API ouvertes pour toute échelle. Elle vous permet de développer des applications modernes, avec une vitesse et une disponibilité assurées par un contrat SLA, une scalabilité automatique et instantanée et des API open source pour de nombreux moteurs NoSQL.

Ce didacticiel en plusieurs parties décrit les tâches suivantes :

> [!div class="checklist"]
> * [Créer une application Node.js Express avec Angular CLI](tutorial-develop-nodejs-part-2.md)
> * [Générer l’interface utilisateur avec Angular](tutorial-develop-nodejs-part-3.md)
> * [Créer un compte Azure Cosmos DB à l’aide d’Azure CLI](tutorial-develop-nodejs-part-4.md) 
> * [Utiliser Mongoose pour se connecter à Azure Cosmos DB](tutorial-develop-nodejs-part-5.md)
> * [Ajouter les fonctions Publier, Placer et Supprimer à l’application](tutorial-develop-nodejs-part-6.md)

Vous souhaitez générer cette même application avec React ? Consultez la [série de didacticiels vidéo pour React](tutorial-develop-mongodb-react.md).

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas

> [!VIDEO https://www.youtube.com/embed/vlZRP0mDabM]

## <a name="finished-project"></a>Projet terminé 

Ce didacticiel vous guide à travers les étapes pour générer l’application pas à pas. Si vous souhaitez télécharger le projet terminé, vous pouvez obtenir l’application complète à partir du [référentiel cosmosdb-angular](https://github.com/Azure-Samples/angular-cosmosdb) sur GitHub.

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du didacticiel, vous avez :

> [!div class="checklist"]
> * Eu une vue d’ensemble des étapes de création d’une application MEAN.js avec Azure Cosmos DB. 

Vous pouvez maintenant passer à la partie suivante du didacticiel afin de créer une application Node.js Express.

> [!div class="nextstepaction"]
> [Créer une application Node.js Express avec Angular CLI](tutorial-develop-nodejs-part-2.md)

Vous tentez d’effectuer une planification de la capacité pour une migration vers Azure Cosmos DB ? Vous pouvez utiliser les informations sur votre cluster de bases de données existantes pour la planification de la capacité.
* Si vous ne connaissez que le nombre de vCore et de serveurs présents dans votre cluster de bases de données existantes, consultez l’article [Estimation des unités de requête à l’aide de vCore ou de processeurs virtuels](../convert-vcore-to-request-unit.md) 
* Si vous connaissez les taux de requêtes typiques de votre charge de travail de base de données actuelle, consultez l’article [Estimation des unités de requête à l’aide du planificateur de capacité Azure Cosmos DB](estimate-ru-capacity-planner.md)
