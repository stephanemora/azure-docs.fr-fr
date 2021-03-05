---
title: Fichier include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102205980"
---
De retour dans la fenêtre de terminal locale, ajoutez un dépôt distant Azure dans votre dépôt Git local. Remplacez *\<deploymentLocalGitUrl-from-create-step>* par l’URL du dépôt Git distant que vous avez enregistrée à partir de [Créer une application web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer votre application à l’aide de la commande suivante. Quand Git Credential Manager vous invite à entrer vos informations d’identification, veillez à entrer celles que vous avez créées dans la section **Configurer un utilisateur de déploiement**, et non pas celles vous permettant de vous connecter au portail Azure.

```bash
git push azure main
```

L’exécution de cette commande peut prendre quelques minutes. Pendant son exécution, des informations semblables à ce qui suit s’affichent :
