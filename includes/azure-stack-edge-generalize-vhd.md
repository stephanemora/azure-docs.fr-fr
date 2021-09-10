---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 06/18/2021
ms.openlocfilehash: 95604a5b7d0bc62568e0e941e5cf035c956f141b
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968391"
---
1. Dans la machine virtuelle, ouvrez une invite de commandes.

1. Exécutez la commande suivante pour généraliser le disque dur virtuel. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    Pour plus d’informations, consultez [Vue d’ensemble de Sysprep (préparation du système)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

1.  Une fois la commande terminée, la machine virtuelle s’arrête. **Ne redémarrez pas la machine virtuelle**.
