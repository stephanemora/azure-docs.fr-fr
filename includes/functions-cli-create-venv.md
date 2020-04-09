---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: 44969063765099d350e21abfbd07792891443911
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673128"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Créer et activer un environnement virtuel

Dans le dossier approprié, exécutez les commandes suivantes pour créer et activer un environnement virtuel nommé `.venv`. Veillez à utiliser Python 3.8, 3.7 ou 3.6, qui sont pris en charge par Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Si Python n’a pas installé le package venv sur votre distribution Linux, exécutez la commande suivante :

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Vous devez exécuter toutes les commandes suivantes dans cet environnement virtuel activé. (Pour quitter l’environnement virtuel, exécutez `deactivate`.)
::: zone-end