---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3929ffa81a8b558d8cb0dbd74135acc9cf1ea47b
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875998"
---
## <a name="configure-your-local-environment"></a>Configurer votre environnement local

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

+ Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 ou version 2.x ultérieure.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 et 3.7 nécessitent [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) version 2.7.1846 (ou une version 2.x ultérieure). Python 3.8 nécessite une [version 3.x](../articles/azure-functions/functions-run-local.md#v2) des outils Core Tools.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 ou ultérieure. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS et Maintenance LTS (versions 8.11.1 et 10.14.1 recommandées).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bits)](https://www.python.org/downloads/release/python-368/), qui sont pris en charge par Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Kit SDK .NET Core 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit (JDK)](https://aka.ms/azure-jdks) version 8.

+ [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.

> [!IMPORTANT]
> Pour pouvoir effectuer ce démarrage rapide, vous devez définir la variable d’environnement JAVA_HOME sur l’emplacement d’installation du JDK.
::: zone-end
