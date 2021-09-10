---
title: Stratégie de support du runtime de langage Azure Functions
description: En savoir plus sur la stratégie de support du runtime de langage Azure Functions
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: b3b5f7cf108fd18ed450a6837a5dd35ceb83dc60
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303964"
---
# <a name="language-runtime-support-policy"></a>Stratégie de support du runtime de langage

Cet article explique la stratégie de support du runtime de langage Azure Functions. 

## <a name="retirement-process"></a>Processus de mise hors service

Le runtime Azure Functions s’articule autour de divers composants, notamment les systèmes d’exploitation, l’hôte Azure Functions et les Workers spécifiques à un langage. Pour maintenir une couverture de support complète pour les applications de fonction, Azure Functions a recours à une réduction progressive du support, au fur et à mesure que les versions du langage de programmation atteignent leur date de fin de vie. Pour la plupart des versions de langage, la date de mise hors service coïncide avec la date de fin de vie de la communauté. 

### <a name="notification-phase"></a>Phase de notification

Nous enverrons des e-mails de notification aux utilisateurs des applications de fonction pour les informer de la mise hors service prochaine des versions de langage. Les notifications seront envoyées au moins un an avant la date de mise hors service. Lorsque vous recevez la notification, vous devez prévoir une mise à niveau de la version de langage utilisée par vos applications de fonction vers une version prise en charge.

### <a name="retirement-phase"></a>Phase de mise hors service

À partir de la date de fin de vie d’une version de langage, vous ne pouvez plus créer de nouvelles applications de fonction ciblant cette version de langage.

Après la date de fin de vie du langage, les applications de fonction qui utilisent les versions de langage mises hors service ne bénéficieront plus des nouvelles fonctionnalités, des correctifs de sécurité ni des optimisations des performances. Toutefois, ces applications de fonction continueront de s’exécuter sur la plateforme. 

> [!IMPORTANT]
>Il est vivement recommandé de mettre à niveau la version de langage de vos applications de fonction affectées vers une version prise en charge.   
>Si vous exécutez des applications de fonction utilisant une version de langage non prise en charge, vous devrez effectuer la mise à niveau pour pouvoir bénéficier d’un support pour ces applications de fonction.


## <a name="retirement-policy-exceptions"></a>Exceptions de la stratégie de mise hors service

Il existe quelques exceptions à la stratégie de mise hors service décrite ci-dessus. Voici une liste des langages qui approchent ou qui ont atteint leur fin de vie, mais qui continuent d’être pris en charge sur la plateforme jusqu’à nouvel ordre. Lorsque ces versions de langage atteignent leur date de fin de vie, elles ne sont plus mises à jour ni corrigées. C’est la raison pour laquelle nous vous déconseillons de développer et d’exécuter vos applications de fonction sur la base de ces versions de langage.

|Versions linguistiques                        |Date de fin de vie         |Date de retrait|
|-----------------------------------------|-----------------|----------------|
|.NET 5|Février 2022|À confirmer|
|Nœud 6|30 avril 2019|28 février 2022| 
|Node 8|31 décembre 2019|28 février 2022| 
|Node 10|30 avril 2021|30 septembre 2022| 
|PowerShell Core 6| 4 septembre 2020|30 septembre 2022|
|Python 3.6 |23 décembre 2021|30 septembre 2022| 
 

## <a name="language-version-support-timeline"></a>Chronologie de support des versions de langage

Pour en savoir plus sur la chronologie de la stratégie de support des versions de langage spécifiques, consultez les ressources externes suivantes :
* .NET - [dotnet.microsoft.com](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)
* Node - [github.com](https://github.com/nodejs/Release#release-schedule)
* Java - [azul.com](https://www.azul.com/products/azul-support-roadmap/)
* PowerShell - [dotnet.microsoft.com](/powershell/scripting/powershell-support-lifecycle?view=powershell-7.1&preserve-view=true#powershell-releases-end-of-life)
* Python - [devguide.python.org](https://devguide.python.org/#status-of-python-branches)

## <a name="configuring-language-versions"></a>Configuration des versions de langage

|Langage                         | Guides de configuration         |
|-----------------------------------------|-----------------|
|C# (bibliothèque de classes) |[ici](./functions-dotnet-class-library.md#supported-versions).|
|Nœud |[ici](./functions-reference-node.md#setting-the-node-version).|
|PowerShell |[ici](./functions-reference-powershell.md#changing-the-powershell-version).|
|Python |[ici](./functions-reference-python.md#python-version).|
 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la mise à niveau des versions de langage pour vos applications de fonction, consultez les ressources suivantes :


+ [Versions de langage actuellement prises en charge](./supported-languages.md#languages-by-runtime-version)
