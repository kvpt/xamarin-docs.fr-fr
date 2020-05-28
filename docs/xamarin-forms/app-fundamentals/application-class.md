---
title: Xamarin.FormsClasse d’application
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2f4b568809f949d813de86ae88cbe621cfa3161c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129686"
---
# <a name="xamarinforms-app-class"></a>Xamarin.FormsClasse d’application

La classe de base `Application` offre les fonctionnalités suivantes, qui sont exposées dans la sous-classe `App` par défaut de votre sous-projet :

* Une propriété `MainPage`,dans laquelle définir la page initiale de l’application.
* [ `Properties` Dictionnaire](#Properties_Dictionary) persistant pour stocker des valeurs simples entre les modifications de l’état du cycle de vie.
* Une propriété `Current` qui contient une référence à l’objet d’application actuel.

Il expose également des [méthodes de cycle de vie](~/xamarin-forms/app-fundamentals/app-lifecycle.md) telles que, et, ainsi que des événements de `OnStart` `OnSleep` `OnResume` navigation modaux.

Selon le modèle que vous avez choisi, la classe `App` peut être définie de deux manières :

* **C#**
* **XAML et C#**

Pour créer une classe **App** en XAML, vous devez remplacer la classe **App** par défaut par une classe **App** XAML et par le code-behind associé, comme indiqué dans l’exemple de code suivant :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Photos.App">

</Application>
```

L’exemple de code suivant montre le code-behind associé :

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

Outre la définition de la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriété, le code-behind doit également appeler la `InitializeComponent` méthode pour charger et analyser le XAML associé.

## <a name="mainpage-property"></a>Propriété MainPage

La propriété `MainPage` de la classe `Application` définit la page racine de l’application.

Par exemple, vous pouvez créer une logique dans votre classe `App` pour afficher une page différente selon que l’utilisateur est connecté ou non.

La propriété `MainPage` doit être définie dans le constructeur `App`.

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

<a name="Properties_Dictionary" />

## <a name="properties-dictionary"></a>Dictionnaire de propriétés

La sous-classe `Application` comprend un dictionnaire `Properties` statique qui peut être utilisé pour stocker des données, en particulier pour une utilisation dans les méthodes `OnStart`, `OnSleep` et `OnResume`. Vous pouvez y accéder à n’importe quel endroit de votre Xamarin.Forms code à l’aide de `Application.Current.Properties` .

Le dictionnaire `Properties` utilise une clé `string` et stocke une valeur `object`.

Par exemple, vous pouvez définir une propriété `"id"` persistante n’importe où dans votre code (lorsqu’un élément est sélectionné, dans la méthode `OnDisappearing` d’une page, ou dans la méthode `OnSleep`) de la façon suivante :

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

Dans les méthodes `OnStart` ou `OnResume`, vous pouvez utiliser cette valeur pour recréer l’expérience utilisateur d’une certaine façon. Le dictionnaire `Properties` stocke des `object`. Vous devez donc caster sa valeur avant de l’utiliser.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Pour éviter toute erreur inattendue, vérifiez toujours la présence de la clé avant d’y accéder.

> [!NOTE]
> Le dictionnaire `Properties` peut sérialiser uniquement les types primitifs de stockage. Les tentatives de stockage d’autres types (tels que `List<string>`) peuvent échouer silencieusement.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistance

Le dictionnaire `Properties` est automatiquement enregistré sur l’appareil.
Les données ajoutées au dictionnaire seront disponibles lors du retour de l’application de l’arrière-plan ou après son redémarrage.

Xamarin.Forms1,4 a introduit une méthode supplémentaire sur la `Application` classe, `SavePropertiesAsync()` qui peut être appelée pour rendre le dictionnaire persistant de manière proactive `Properties` . Cela vous permet d’enregistrer les propriétés après les mises à jour importantes, plutôt que de risquer qu’elles ne soient pas sérialisées à cause d’un plantage ou parce qu’elles ont été tuées par le système d’exploitation.

Vous pouvez trouver des références à l’utilisation du `Properties` dictionnaire dans la **Mobile Apps Xamarin.Forms Creating with** Book Chapters [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf)et [20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf)et dans les [exemples](https://github.com/xamarin/xamarin-forms-book-preview-2)associés.

## <a name="the-application-class"></a>Classe d’application

L’implémentation complète de la classe `Application` est indiquée ci-dessous pour référence :

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}
```

Cette classe est ensuite instanciée dans chaque projet spécifique à la plateforme, puis passée à la méthode `LoadApplication`, où le `MainPage` est chargé et affiché à l’utilisateur.
Le code adapté à chaque plateforme est indiqué dans les sections suivantes. Les Xamarin.Forms modèles de solution les plus récents contiennent déjà tout ce code, préconfiguré pour votre application.

### <a name="ios-project"></a>Projet iOS

La classe `AppDelegate` iOS hérite de `FormsApplicationDelegate`. Elle doit :

* Appeler `LoadApplication` avec une instance de la classe `App`

* Toujours retourner `base.FinishedLaunching (app, options);`

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Projet Android

Le `MainActivity` Android hérite de `FormsAppCompatActivity`. Dans le `OnCreate`, remplacez la méthode `LoadApplication` qui est appelée par une instance de la classe `App`.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>Projet Windows universel (UWP) pour Windows 10

La page principale du projet UWP doit hériter de `WindowsPage` :

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

La construction de code-behind C# doit appeler `LoadApplication` pour créer une instance de votre Xamarin.Forms `App` . Notez qu’il est recommandé d’utiliser explicitement l’espace de noms de l’application pour qualifier le `App` , car les applications UWP ont également leur propre `App` classe non liée à Xamarin.Forms .

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

Notez que `Forms.Init()` doit être appelé à partir d’**App.xaml.cs** dans le projet UWP.

Pour plus d’informations, consultez [configurer des projets Windows](~/xamarin-forms/platform/windows/installation/index.md), qui comprend les étapes permettant d’ajouter un projet UWP à une Xamarin.Forms solution existante qui ne cible pas UWP.

## <a name="related-video"></a>Vidéo connexe

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
