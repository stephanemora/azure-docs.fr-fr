---
title: Résoudre les problèmes relatifs à l’outil Copier des données dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés à l’outil Copier des données dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388298"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Résoudre les problèmes relatifs à l’outil Copier des données dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés à l’outil Copier des données dans Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erreurs et messages courants

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Code d’erreur : Impossible de valider dans l’outil Copier des données

- **Symptômes** : Dans la première étape de l’outil Copier des données, vous rencontrez le message d’avertissement « Impossible de valider ».
- **Causes** : Cela peut se produire lorsque tous les cookies tiers sont désactivés.
- **Résolution** : 
    - Utilisez le navigateur Internet Explorer ou Microsoft Edge.
    - Si vous utilisez le navigateur Chrome, suivez les instructions ci-dessous pour ajouter l’exception des cookies pour *microsoftonline.com* et *windows.net*.
        1.  Ouvrez le navigateur Chrome.
        2.  Cliquez sur la clé à molette ou les trois lignes à droite (personnaliser et contrôler Google Chrome).
        3.  Cliquez sur **Settings**.
        4.  Recherchez **Cookies** ou accédez à **Confidentialité** sous Paramètres avancés.
        5.  Sélectionnez **Paramètres de contenu**.    
        6.  Les cookies doivent être définis sur **Autoriser la définition des données locales (recommandé)** .
        7.  Cliquez sur **Gérer les exceptions**. Sous **Modèle de nom d’hôte**, entrez ce qui suit et assurez-vous que le comportement est défini sur **Autoriser**.
            - login.microsoftonline.com
            - login.windows.net
        8.  Fermez le navigateur et relancez-le.
    - Si vous utilisez le navigateur Firefox, suivez les instructions ci-dessous pour ajouter l’exception des cookies.
        1. Dans le menu Firefox, accédez à **Outils** > **Options**.
        2. Sous **Confidentialité** > **Historique**, vous pouvez voir que le paramètre actuel est **Utiliser les paramètres personnalisés pour l’historique**.
        3. Dans **Accepter les cookies tiers**, votre paramètre actuel est peut-être **Jamais**. Vous devez cliquer sur **Exceptions** sur la droite pour ajouter les sites suivants.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Fermez le navigateur et relancez-le. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Code d’erreur : Impossible d’ouvrir la page de connexion et d’entrer le mot de passe

- **Symptômes** : L’outil Copier des données vous redirige vers la page de connexion, mais la page de connexion ne s’affiche pas correctement.
- **Causes** : Ce problème peut se produire si vous avez modifié l’environnement réseau, du réseau d’entreprise en réseau domestique. Les navigateurs contiennent certains caches. 
- **Résolution** : 
    1.  Fermez le navigateur, puis réessayez. Passez à l’étape suivante si le problème persiste.   
    2.  Si vous utilisez le navigateur Internet Explorer, essayez de l’ouvrir en mode privé (appuyez sur Ctrl+Maj+P). Si vous utilisez le navigateur Chrome, essayez de l’ouvrir en mode Incognito (appuyez sur Ctrl+Maj+N). Passez à l’étape suivante si le problème persiste. 
    3.  Essayez d’utiliser un autre navigateur. 


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&R](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances du mappage de flux de données ADF](concepts-data-flow-performance.md)
