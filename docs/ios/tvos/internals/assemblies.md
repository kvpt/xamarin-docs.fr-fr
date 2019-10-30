---
title: Assemblys pris en charge par Xamarin pour tvOS
description: Afin d’aider à clarifier les fonctionnalités disponibles pour les applications tvOS, ce document fournit une liste d’assemblys pris en charge par Xamarin pour le développement tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 371440f2e1ab28e802bf2d184b3e17d073a0c774
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030682"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Assemblys pris en charge par Xamarin pour tvOS

## <a name="supported-assemblies"></a>Assemblys pris en charge

Il s’agit de la liste des assemblys pris en charge par Xamarin pour vos applications Xamarin. tvOS. La liste détaillée de ces informations est indiquée ci-dessous.  Certaines omissions notable incluent `System.EnterpriseServices`, la pile ASP.NET et Windows. Forms.

|Assembly|Added|Compatibilité des API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Pour les writers de compilateur.|
|Mono.Data.Sqlite.dll|1.2|Fournisseur ADO.NET pour SQLite ; consultez [limitations](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Prise en charge du protocole TDS ; utilisé pour la prise en charge de [System. Data. SqlClient](xref:System.Data.SqlClient) dans [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|API de chiffrement.|
|monotouch.dll|1.0|Cet assembly contient la [ C# liaison à l’API cocoatouch](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|Les API orientées objet OpenGL/ouvert, [étendues pour fournir une prise en charge des appareils iPhone](xref:OpenGLES).|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), ainsi que les types des espaces de noms suivants : <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System. ComponentModel. Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System .net. cache</li> <li>System.Net.Mail</li> <li>System .net. MIME</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System. Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx), [avec certaines fonctionnalités supprimées](~/ios/data-cloud/system.data.md).|
|System. Data. service. client. dll|3|Client oData complet.|
|System.Drawing|1.0|API System. Drawing-API classique uniquement.<br />_System. Drawing n’est pas pris en charge dans le API unifiée pour les frameworks Xamarin. Mac .NET 4,5 ou mobile._|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|La pile [WCF](https://docs.microsoft.com/xamarin/cross-platform/data-cloud/web-services/) comme présente dans [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), ainsi que les types des espaces de noms suivants : <ul><li>Système</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); partie intégrante de la prise en charge de [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) .|
|System.Web.Services|1.1|[Services Web de base](https://docs.microsoft.com/xamarin/cross-platform/data-cloud/web-services/) à partir du profil .net 3,5, avec les composants serveur supprimés.|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliothèques de classes portables

En plus des liaisons Mac, Xamarin. tvOS peut utiliser des [bibliothèques de classes portables .net](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Liens associés

- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
