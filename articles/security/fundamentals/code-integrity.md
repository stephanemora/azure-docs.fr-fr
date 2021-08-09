---
title: Intégrité du code de la plateforme – Sécurité Azure
description: Découvrez la façon dont Microsoft assure l’exécution des logiciels autorisés uniquement.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/10/2021
ms.openlocfilehash: e9a58bf83181352adb3894fb159fd212f0f1b7ca
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112063648"
---
# <a name="platform-code-integrity"></a>Intégrité du code de la plateforme

Un défi majeur dans le fonctionnement d’un système complexe tel que Microsoft Azure consiste à garantir que seuls les logiciels autorisés s’exécuteront dans le système. Les logiciels non autorisés présentent différents risques pour les entreprises :

- Risques de sécurité, tels que les outils d’attaque dédiés, les programmes malveillants personnalisés et les logiciels tiers présentant des vulnérabilités connues
- Risques de conformité lorsque le processus de gestion des modifications approuvées n’est pas utilisé pour intégrer un nouveau logiciel
- Risque de qualité provenant de logiciels développés en externe, qui peuvent ne pas répondre aux exigences opérationnelles de l’entreprise

Dans Azure, nous sommes confrontés au même défi, et à niveau de complexité inextricable. Nos serveurs se chiffrent par milliers pour exécuter des logiciels développés et gérés par des milliers de techniciens. C’est une surface d’attaque importante qui ne peut pas être gérée uniquement par des processus métier.

## <a name="adding-an-authorization-gate"></a>Ajout d’un contrôle d’autorisation

Azure utilise un processus d’ingénierie perfectionné qui implémente des contrôles au niveau de la sécurité, de la conformité et de la qualité des logiciels que nous déployons. Ce processus comprend le contrôle d’accès au code source, la conduite de revues du code de pair, la réalisation d’une analyse statique des vulnérabilités de sécurité, qui suit le processus [SDL (Security Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/) de Microsoft et l’exécution de tests fonctionnels et de qualité. Nous devons nous assurer que les logiciels que nous déployons sont passés par ce processus. L’intégrité du code nous permet d’obtenir cette garantie.

## <a name="code-integrity-as-an-authorization-gate"></a>Intégrité du code en tant que niveau d’autorisation

L’intégrité du code est un service au niveau du noyau qui est devenu disponible à partir de Windows Server 2016. L’intégrité du code peut appliquer une stratégie stricte de contrôle d’exécution chaque fois qu’un pilote ou une bibliothèque de liens dynamiques (DLL) est chargé, qu’un fichier binaire exécutable est exécuté ou qu’un script est lancé. Des systèmes similaires, tels que [DM-Verity](https://www.kernel.org/doc/html/latest/admin-guide/device-mapper/verity.html), existent pour Linux. Une stratégie d’intégrité du code se compose d’un ensemble d’indicateurs d’autorisation (soit des certificats de signature de code, soit des hachages de fichier [SHA256](https://en.wikipedia.org/wiki/Secure_Hash_Algorithms)) que le noyau met en correspondance avant de charger ou d’exécuter un fichier binaire ou un script.

L’intégrité du code permet à un administrateur système de définir une stratégie autorisant uniquement les fichiers binaires et les scripts qui ont été signés par des certificats particuliers, ou qui correspondent à des hachages SHA256 spécifiés. Le noyau applique cette stratégie en bloquant l’exécution de tout ce qui ne se conforme pas à la stratégie définie.

Une stratégie d’intégrité du code pose un problème, car à moins d’être parfaitement correcte, elle peut bloquer des logiciels critiques en production et provoquer une panne. De ce fait, vous pouvez vous demander pourquoi il n’est pas suffisant d’utiliser la supervision de la sécurité pour détecter le moment où des logiciels non autorisés seront exécutés. L’intégrité du code présente un mode d’audit qui, au lieu d’empêcher l’exécution, peut avertir de l’exécution de logiciels non autorisés. Les alertes peuvent certainement ajouter une grande valeur au traitement des risques de conformité, mais pour ce qui est des risques de sécurité, tels que les ransomware ou les programmes malveillants personnalisés, le retardement de la réponse, même de quelques secondes, peut faire la différence entre la protection et la propagation durable d’un adversaire dans votre flotte. Dans Azure, nous avons beaucoup investi pour gérer tout risque d’intégrité du code qui participe aux pannes impactant le client.

## <a name="build-process"></a>Processus de génération

Comme indiqué ci-dessus, le système de génération d’Azure bénéficie d’un ensemble complet de tests pour s’assurer que les modifications apportées aux logiciels sont sûres et conformes. Dès lors qu’une build a progressé jusqu’au stade de la validation, le système de génération la signe au moyen d’un certificat de build Azure. Le certificat indique que la build a passé toutes les étapes du processus de gestion des modifications avec succès. Le dernier test passé par la build est nommé Signature de validation du code (CSV, Code Signature Validation). Le test CSV confirme que les fichiers binaires nouvellement générés respectent la stratégie d’intégrité du code avant que nous ne les déployions en production. Nous avons ainsi la certitude que nous ne provoquons aucune panne impactant le client du fait de fichiers binaires incorrectement signés. Si le test CSV détecte un problème, la build s’arrête et une notification par radiomessagerie est envoyée aux techniciens concernés pour qu’ils examinent et résolvent le problème.

## <a name="safety-during-deployment"></a>Sécurité pendant le déploiement

Même si nous effectuons un test CSV pour chaque build, il existe toujours un risque qu’une modification ou qu’une incohérence en production puisse entraîner une panne liée à l’intégrité du code. Par exemple, une machine peut exécuter une ancienne version de la stratégie d’intégrité du code ou se trouver dans un état défectueux qui produit des faux positifs dans l’intégrité du code. (À l’échelle d’Azure, nous avons vu bien des choses !) Ainsi, nous devons poursuivre notre protection contre le risque de panne au cours du déploiement.

Toutes les modifications apportées dans Azure doivent se déployer selon une série d’étapes. La première d’entre elles est composée d’instances de tests Azure en interne. La phase suivante est utilisée uniquement pour répondre aux autres équipes de produits Microsoft. La dernière étape concerne les clients tiers. Lorsqu’une modification est déployée, elle passe successivement d’une étape à l’autre, et s’interrompt pour mesurer l’intégrité de la phase. Si la modification examinée ne présente aucun impact négatif, elle passe à l’étape suivante. Si nous apportons une modification incorrecte à une stratégie d’intégrité du code, la modification est détectée au cours de ce déploiement par étapes, et annulée.

## <a name="incident-response"></a>Réponse aux incidents

Même avec cette protection en couches, il est toujours possible que certains serveurs de la flotte puissent bloquer les logiciels correctement autorisés et qu’ils entraînent un problème client ; c’est là un de nos pires scénarios. Notre dernière couche de défense est l’investigation humaine. Chaque fois que l’intégrité du code bloque un fichier, il déclenche une alerte que les techniciens appelés doivent examiner. L’alerte nous permet de lancer des enquêtes de sécurité et d’intervenir, que le problème soit un indicateur d’attaque réelle, un faux positif ou une autre situation impactant le client. Le temps d’atténuation des problèmes liés à l’intégrité du code s’en trouve abrégé.  

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [Windows 10](/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control) utilise l’intégrité du code configurable.

Pour plus d’informations sur ce que nous faisons pour assurer l’intégrité et la sécurité de la plateforme, consultez :

- [Sécurité de microprogramme](firmware.md)
- [Démarrage sécurisé](secure-boot.md)
- [Démarrage mesuré et attestation de l’hôte](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Chiffrement au repos](encryption-atrest.md)
- [Sécurité de l’hyperviseur](hypervisor.md)