---
title: Conversion du contenu d’un certificat encodé en base 64 - Azure HDInsight
description: Conversion du contenu du certificat du principal de service au format de chaîne encodé en base 64 dans Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944409"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Conversion du contenu du certificat du principal de service au format de chaîne encodé en base 64 Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

Vous recevez un message d’erreur indiquant que l’entrée n’est pas une chaîne en base 64 valide, car elle contient un caractère qui n’est pas en base 64, plus de deux caractères de remplissage ou un caractère autre qu’un espace blanc parmi les caractères de remplissage.

## <a name="cause"></a>Cause

Lorsque vous utilisez PowerShell ou le déploiement d’un modèle Azure pour créer des clusters avec Data Lake en tant que stockage principal ou supplémentaire, le contenu du certificat du principal de service fourni pour accéder au compte de stockage Data Lake est au format de base 64. Une conversion incorrecte du contenu du certificat pfx en chaîne codée en base 64 peut générer cette erreur.

## <a name="resolution"></a>Résolution

Une fois que vous disposez du certificat du principal de service au format pfx (voir [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) pour les étapes de création du principal de service), utilisez la commande PowerShell ou l’extrait de code en C# suivant pour convertir le contenu du certificat au format de base 64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]