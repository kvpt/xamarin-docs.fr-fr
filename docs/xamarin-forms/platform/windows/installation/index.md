---
title: ''
description: Xamarin.FormsLes anciennes solutions (ou celles créées sur MacOS) n’ont pas de projets de plateforme Windows universelle. cet article explique donc comment ajouter un nouveau projet UWP à une Xamarin.Forms solution existante.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87ad78e97046eef7fd6c2e062fa9f84d92c11b38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132208"
---
# <a name="setup-windows-projects"></a>Configurer des projets Windows

_Ajout de nouveaux projets Windows à une Xamarin.Forms solution existante_

Xamarin.FormsLes anciennes solutions (ou celles créées sur MacOS) n’ont pas de projets d’application plateforme Windows universelle (UWP). Par conséquent, vous devez ajouter manuellement un projet UWP pour générer une application Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Ajouter une application plateforme Windows universelle

**Visual Studio 2019** sur **Windows 10** est recommandé pour créer des applications UWP. Pour plus d’informations sur la plateforme Windows universelle, consultez [Intro to the plateforme Windows universelle](/windows/uwp/get-started/universal-application-platform-guide/).

UWP est disponible dans Xamarin.Forms 2,1 et versions ultérieures, et Xamarin.Forms . Maps est pris en charge dans Xamarin.Forms 2,2 et versions ultérieures.

Pour obtenir des conseils utiles, consultez la section <a href="#troubleshooting">résolution des problèmes</a> .

Suivez ces instructions pour ajouter une application UWP qui s’exécutera sur les téléphones, tablettes et ordinateurs de bureau Windows 10 :

 1,0. Cliquez avec le bouton droit sur la solution, puis sélectionnez **ajouter > nouveau projet...** et ajouter un projet d' **application vide (Windows universel)** :

  ![](universal-images/add-wu.png "Add New Project Dialog")

 2. Dans la boîte de dialogue **nouveau projet de plateforme Windows universelle** , sélectionnez les versions minimale et cible de Windows 10 sur lesquelles l’application doit s’exécuter :

  ![](universal-images/target-version.png "New Universal Windows Platform Project Dialog")

 1,3. Cliquez avec le bouton droit sur le projet UWP, puis sélectionnez **gérer les packages NuGet...** et ajouter le **Xamarin.Forms** Package. Assurez-vous que les autres projets de la solution sont également mis à jour vers la même version du Xamarin.Forms Package.

 4. Assurez-vous que le nouveau projet UWP sera créé dans la fenêtre de **Configuration Manager de > de build** (cela ne s’est probablement pas produit par défaut). Cochez les cases **générer** et **déployer** pour le projet universel :

  [![](universal-images/configuration-sml.png "Configuration Manager Window")](universal-images/configuration.png#lightbox "Configuration Manager Window")

 5,5. Cliquez avec le bouton droit sur le projet, puis sélectionnez **ajouter > référence** et créer une référence au Xamarin.Forms projet d’application (.NET standard ou projet partagé).

  ![](universal-images/addref-sml.png "Reference Manager Dialog")

 6,3. Dans le projet UWP, modifiez **app.Xaml.cs** pour inclure l' `Init` appel de méthode à l’intérieur de la `OnLaunched` méthode autour de la ligne 52 :

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 Commission(7. Dans le projet UWP, modifiez **MainPage. Xaml** en supprimant le `Grid` contenu de l' `Page` élément.

 version8. Dans **MainPage. Xaml**, ajoutez une nouvelle `xmlns` entrée pour `Xamarin.Forms.Platform.UWP` :

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 0,9. Dans **MainPage. Xaml**, remplacez l' `<Page` élément racine par `<forms:WindowsPage` :

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10. Dans le projet UWP, modifiez **MainPage.Xaml.cs** pour supprimer le `: Page` spécificateur d’héritage pour le nom de la classe (car il hérite désormais de `WindowsPage` la modification apportée à l’étape précédente) :

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11. Dans **MainPage.Xaml.cs**, ajoutez l' `LoadApplication` appel dans le `MainPage` constructeur pour démarrer l' Xamarin.Forms application :

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

> [!NOTE]
> L’argument de la `LoadApplication` méthode est l' `Xamarin.Forms.Application` instance définie dans votre projet .NET standard.

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

douze. Ajoutez des ressources locales (par exemple, fichiers image) à partir des projets de plateforme existants qui sont requis.

## <a name="troubleshooting"></a>Résolution des problèmes

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>« Exception d’appel cible » lors de l’utilisation de « compiler avec .NET Native chaîne d’outils »

Si votre application UWP référence plusieurs assemblys (par exemple des bibliothèques de contrôles tiers, ou si votre application elle-même est fractionnée en plusieurs bibliothèques), Xamarin.Forms peut ne pas pouvoir charger des objets à partir de ces assemblys (tels que des convertisseurs personnalisés).

Cela peut se produire lors de l’utilisation de la **compilation avec .net Native chaîne d’outils** , qui est une option pour les applications UWP dans la fenêtre **propriétés > générer > général** pour le projet.

Vous pouvez résoudre ce problème en utilisant une surcharge spécifique à UWP de l' `Forms.Init` appel dans **app.Xaml.cs** comme indiqué dans le code ci-dessous (vous devez remplacer `ClassInOtherAssembly` par une classe réelle que votre code référence) :

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Ajoutez une entrée pour chaque assembly que vous avez ajouté en tant que référence dans la Explorateur de solutions, via une référence directe ou un NuGet.

#### <a name="dependency-services-and-net-native-compilation"></a>Services de dépendance et compilation de .NET Native

Les versions release utilisant .NET Native compilation peuvent ne pas réussir à résoudre les services de dépendance qui sont définis en dehors de l’exécutable principal de l’application (par exemple, dans un projet ou une bibliothèque distinct).

Utilisez la `DependencyService.Register<T>()` méthode pour inscrire manuellement des classes de service de dépendance. En fonction de l’exemple ci-dessus, ajoutez la méthode Register comme suit :

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
