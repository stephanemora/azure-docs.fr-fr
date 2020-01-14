---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681025"
---
De retour dans la fenêtre de terminal locale, ajoutez un dépôt distant Azure dans votre dépôt Git local. Remplacez *\<deploymentLocalGitUrl-from-create-step>* par l’URL du Git distant que vous avez enregistrée à la section [Créer une app web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Quand Git Credential Manager vous invite à entrer vos informations d’identification, veillez à entrer celles que vous avez créées dans la section [Configurer un utilisateur de déploiement](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), et non pas celles vous permettant de vous connecter au portail Azure.

```bash
git push azure master
```

L’exécution de cette commande peut prendre quelques minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :
