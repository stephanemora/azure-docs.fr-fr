---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "88944480"
---
## <a name="configure-your-local-environment"></a>Configurer votre environnement local

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 ou version 2.x ultérieure.
::: zone-end  
::: zone pivot="programming-language-python"
+ La version Azure Functions Core Tools qui correspond à la version de Python installée :

   | Version Python | Version Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [version 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Version 2.7.1846 ou ultérieure](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS et Maintenance LTS (versions 8.11.1 et 10.14.1 recommandées).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bits)](https://www.python.org/downloads/release/python-368/), qui sont pris en charge par Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Kit SDK .NET Core 3.1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit (JDK)](https://aka.ms/azure-jdks) version 8. 

    > [!IMPORTANT]
    > + La prise en charge des fonctions pour Java 11 est actuellement en préversion et l’archétype Maven crée un déploiement Java 8 par défaut. Si vous souhaitez exécuter à la place votre application de fonction sur Java 11, vous devez mettre à jour manuellement le fichier pom.xml avec les valeurs Java 11. Pour plus d’informations, consultez les [versions Java](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + La variable d’environnement `JAVA_HOME` doit être définie sur l’emplacement d’installation de la version appropriée du JDK pour effectuer ce démarrage rapide.

+ [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.

::: zone-end
