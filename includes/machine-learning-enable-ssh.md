---
author: sdgilley
ms.service: machine-learning
ms.topic: include
ms.date: 07/30/2021
ms.author: sgilley
ms.openlocfilehash: bd6760d152edeb3f8e50fa50a5b72476fc46150f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532026"
---
Une fois que vous avez sélectionné **Suivant : Paramètres avancés** :

1. Activez **Activer l’accès SSH**.
1. Dans la **source de clé publique SSH**, sélectionnez l’une des options de la liste déroulante :
    * Si vous  **Générez une nouvelle paire de clés** :
        1. Tapez un nom pour la clé dans **Nom de la paire de clés**.
        1. Sélectionnez **Create** (Créer).
        1. Sélectionnez **Télécharger la clé privée et créer une capacité de calcul**.  La clé est généralement téléchargée dans le dossier **Téléchargements**.  
    * Si vous sélectionnez **Utiliser la clé publique existante stockée dans Azure**, recherchez et sélectionnez la clé dans **Clé stockée**.
    * Si vous sélectionnez **Utiliser une clé publique existante**, fournissez une clé publique RSA au format à une seule ligne (à partir de « SSH-RSA ») ou au format PEM multiligne. Vous pouvez générer des clés SSH à l’aide de ssh-keygen sur Linux et macOS, ou à l’aide de PuTTYGen sur Windows.