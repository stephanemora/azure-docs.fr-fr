---
title: Configurer l’authentification avec Amazon Web Services
description: Cet article décrit comment créer et valider des informations d’identification AWS pour les runbooks dans Azure Automation gérant des ressources AWS.
keywords: authentification AWS, configurer aws
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9bca59efc374fedf04600ae165f04215016dd4af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850939"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Authentification des Runbooks avec Amazon Web Services

Il est possible d’automatiser les tâches courantes avec les ressources Amazon Web Services (AWS) à l’aide des runbooks Automation dans Azure. Vous pouvez automatiser de nombreuses tâches dans AWS à l’aide des runbooks Automation, tout comme avec les ressources dans Azure. Pour ce faire, deux choses sont nécessaires :

* Un abonnement AWS et un ensemble d’informations d’identification. Plus précisément, votre clé d’accès AWS et votre clé secrète. Pour plus d’informations, consultez l’article [Utilisation des informations d’identification AWS](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Un abonnement Azure et un compte Automation.

Pour vous authentifier avec AWS, vous devez spécifier un ensemble d’informations d’identification AWS pour authentifier vos runbooks en cours d’exécution à partir d’Azure Automation. Si vous avez déjà un compte Automation que vous souhaitez utiliser pour vous authentifier auprès d’AWS, suivez les étapes décrites dans la section suivante : Pour dédier un compte aux runbooks ciblant les ressources AWS, vous devez créer un [compte Automation](automation-offering-get-started.md) (ignorez l’option de création d’un principal de service) avant d’effectuer les étapes ci-dessous :

## <a name="configure-automation-account"></a>Configuration d’un compte Automation

Pour qu’Azure Automation communique avec AWS, vous devez d’abord récupérer vos informations d’identification AWS et les stocker en tant que ressources dans Azure Automation. Exécutez les opérations décrites dans le document AWS [Managing Access Keys for your AWS Account](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) (Gestion des clés d’accès de votre compte AWS) pour créer une clé d’accès et copiez **l’ID de clé d’accès** et la **clé d’accès secrète** (vous pouvez également télécharger votre fichier de clés pour le stocker dans un endroit sûr).

Une fois que vous avez créé et copié vos clés de sécurité AWS, vous devez créer une ressource Informations d’identification avec un compte Azure Automation pour les stocker en toute sécurité et les référencer avec vos Runbooks. Suivez les étapes décrites dans la section : **Pour créer une ressource d’informations d’identification** de l’article [Ressources d’informations d’identification dans Azure Automation](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal), et entrez les informations suivantes :

1. Dans la zone **Nom**, entrez **AWScred** ou une valeur appropriée, suivant vos normes d’affectation de noms.
2. Dans la zone **Nom d’utilisateur**, entrez votre **ID de clé d’accès** et votre **clé d’accès secrète** dans les zones **Mot de passe** et **Confirmer le mot de passe**.

## <a name="next-steps"></a>Étapes suivantes

* Consultez l’article de la solution [Automatisation du déploiement d’une machine virtuelle dans Amazon Web Services](automation-scenario-aws-deployment.md) pour apprendre à créer des Runbooks afin d’automatiser les tâches dans AWS.
