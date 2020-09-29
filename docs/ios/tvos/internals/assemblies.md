---
title: Assemblys pris en charge par Xamarin pour tvOS
description: Afin d’aider à clarifier les fonctionnalités disponibles pour les applications tvOS, ce document fournit une liste d’assemblys pris en charge par Xamarin pour le développement tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 138b65d51c7069f04433f5b4aee6f8d2b743ccdb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435018"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Assemblys pris en charge par Xamarin pour tvOS

## <a name="supported-assemblies"></a>Assemblys pris en charge

Il s’agit de la liste des assemblys pris en charge par Xamarin pour vos applications Xamarin. tvOS. La liste détaillée de ces informations est indiquée ci-dessous.  Certaines omissions notable incluent `System.EnterpriseServices` , la pile ASP.net et Windows. Forms.

|Assembly|Ajout|Compatibilité des API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Pour les writers de compilateur.|
|Mono.Data.Sqlite.dll|1.2|Fournisseur ADO.NET pour SQLite ; consultez [limitations](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Prise en charge du protocole TDS ; utilisé pour la prise en charge de [System. Data. SqlClient](xref:System.Data.SqlClient) dans [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|API de chiffrement.|
|monotouch.dll|1.0|Cet assembly contient la [liaison C# avec l’API cocoatouch](/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|OpenTK.dll|1.0|Les API orientées objet OpenGL/ouvert, [étendues pour fournir une prise en charge des appareils iPhone](xref:OpenGLES).|
|System.dll|1.0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95)), ainsi que les types des espaces de noms suivants : <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System. ComponentModel. Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System .net. cache</li> <li>System.Net.Mail</li> <li>System .net. MIME</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System. Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.Data.dll|1.2|[.Net 3,5](/previous-versions/ms229335(v=vs.100)), [avec certaines fonctionnalités supprimées](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3.x|Client oData complet.|
|System.Drawing|1.0|API System. Drawing-API classique uniquement.<br />_System. Drawing n’est pas pris en charge dans le API unifiée pour les frameworks Xamarin. Mac .NET 4,5 ou mobile._|
|System.Json.dll|1.1|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.Runtime.Serialization.dll|?|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.ServiceModel.dll|1.1|La pile [WCF](../../../cross-platform/data-cloud/web-services/index.md) comme présente dans [Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95))|
|System.ServiceModel.Web.dll|?|[Silverlight](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838194(v=vs.95)), ainsi que les types des espaces de noms suivants : <ul><li>Système</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3,5](/previous-versions/ms229335(v=vs.100)); partie intégrante de la prise en charge de [System. Data](../../data-cloud/system.data.md) .|
|System.Web.Services|1.1|[Services Web de base](../../../cross-platform/data-cloud/web-services/index.md) à partir du profil .net 3,5, avec les composants serveur supprimés.|
|System.Xml.dll|1.0|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|
|System.Xml.Linq.dll|1.0|[.NET 3.5](/previous-versions/ms229335(v=vs.100))|

<a name="Summary"></a>

## <a name="portable-class-libraries"></a>Bibliothèques de classes portables

En plus des liaisons Mac, Xamarin. tvOS peut utiliser des [bibliothèques de classes portables .net](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Liens associés

- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)