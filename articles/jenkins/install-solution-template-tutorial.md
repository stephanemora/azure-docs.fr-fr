---
title: Tutoriel - Créer un serveur Jenkins sur Azure
description: Dans ce tutoriel, vous installez Jenkins sur une machine virtuelle Linux Azure à partir du modèle de solution Jenkins et vous générez un exemple d’application Java.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274528"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Tutoriel : Créer un serveur Jenkins sur une machine virtuelle Linux Azure 

Ce tutoriel montre comment installer [Jenkins](https://jenkins.io) sur une machine virtuelle Linux Ubuntu avec les outils et les plug-ins configurés pour fonctionner avec Azure. Lorsque vous avez terminé, vous disposez d’un serveur de Jenkins s’exécutant dans Azure qui génère un exemple d’application Java à partir de [Github](https://github.com).

> [!div class="checklist"]
> * Installer et configurer un serveur Jenkins sur Azure
> * Accéder à la console Jenkins en utilisant un tunnel SSH
> * Créer un projet Freestyle
> * Compiler le code et packager l’exemple d’application

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]