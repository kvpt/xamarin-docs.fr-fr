---
title: Assemblys disponibles
description: Ce document répertorie les assemblys disponibles pour une utilisation dans Xamarin. iOS, Xamarin. Android et Xamarin. Mac. Elle contient également des liens vers la documentation sur les bibliothèques de .NET Standard et les bibliothèques de classes portables.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 3d3524a0f99d78fc356dc7c4bb3e3aca1940a718
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278447"
---
# <a name="available-assemblies"></a>Assemblys disponibles

Xamarin. iOS, Xamarin. Android et Xamarin. Mac sont tous livrés avec plus d’une douzaine d’assemblys. Tout comme Silverlight est un sous-ensemble étendu des assemblys .NET Desktop, les plateformes Xamarin sont également un sous-ensemble étendu de plusieurs assemblys .NET Silverlight et Desktop.

Les plateformes Xamarin ne sont pas compatibles ABI avec les assemblys existants compilés pour un autre profil. Vous devez recompiler votre code source pour générer des assemblys ciblant le profil approprié (tout comme vous devez recompiler le code source pour cibler Silverlight et .NET 3,5 séparément).

Les applications Xamarin. Mac peuvent être compilées en trois modes : une qui utilise le profil mobile organisé de Xamarin, l’infrastructure Xamarin. Mac .NET 4,5 qui vous permet de cibler des assemblys de bureau complets existants et une version non prise en charge qui utilise l’API .NET trouvée dans un mono système. programme. Pour plus d’informations, consultez notre documentation sur les [frameworks cibles](~/mac/platform/target-framework.md) .

## <a name="net-standard-libraries"></a>Bibliothèques .NET Standard

En plus des liaisons iOS, Android et Mac, les projets Xamarin peuvent utiliser des [bibliothèques de .NET standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliothèques de classes portables

Les projets Xamarin peuvent également utiliser des [bibliothèques de classes portables .net](~/cross-platform/app-fundamentals/pcl.md), bien que cette technologie soit dépréciée en faveur de .NET standard.

## <a name="supported-assemblies"></a>Assemblys pris en charge

Il s’agit des assemblys disponibles dans le **Gestionnaire de références > assemblys > Framework** (Visual Studio 2017) et **modifier les références > Packages** (Visual Studio pour Mac) et leur compatibilité avec les plateformes Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilité des API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N. dll|Comprend CJC, MidEast, autre, rare, Ouest|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Fournisseur ADO.NET pour SQLite ; consultez limitations.|✓|✓|✓|
> |Mono.Data.Tds.dll|Prise en charge du protocole TDS ; utilisé pour la prise en charge de [System. Data. SqlClient](xref:System.Data.SqlClient) dans [System. Data](xref:System.Data).|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|API de chiffrement.|✓|✓|✓|
> |monotouch.dll|Cet assembly contient C# la liaison à l’API cocoatouch. Cela est uniquement disponible dans les projets iOS classiques.|✓| | |
> |MonoTouch. &#8203;Dialog-1. dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|Les API orientées objet OpenGL/ouvert, étendues pour fournir une prise en charge des appareils iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), ainsi que les types des espaces de noms suivants :<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx) , avec [certaines fonctionnalités supprimées](~/ios/data-cloud/system.data.md).|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|Client oData complet.|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|La pile WCF comme présente dans [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), ainsi que les types des espaces de noms suivants : <br />Système<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); partie intégrante de la prise en charge de [System. Data](~/ios/data-cloud/system.data.md) .|✓|✓|✓|
> |System.Web.&#8203;Services.dll|Services Web de base à partir du profil .NET 3,5, avec les composants serveur supprimés.|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Cet assembly contient C# la liaison à l’API cocoatouch. Utilisé uniquement dans les projets iOS unifiés.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Pour les writers de compilateur.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|System. Drawing n’est pas pris en charge dans les API unifiée pour les frameworks Xamarin. Mac, .NET 4,5 ou mobile. La prise en charge de System. Drawing peut être ajoutée à iOS et macOS à l’aide de la bibliothèque [sysdrawing-coregraphics](https://github.com/mono/sysdrawing-coregraphics)|✓| |✓|
