---
title: Partie 1. Bien démarrer avec XAML
description: Dans une application Xamarin.Forms, XAML est principalement utilisé pour définir le contenu visuel d’une page et le fonctionne avec un fichier code-behind.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: a35a3750957073168bca4903e1cef79ed53a3de9
ms.sourcegitcommit: bb6a103de620386187a08151a4cdc29a4dc53a10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679973"
---
# <a name="part-1-getting-started-with-xaml"></a>Partie 1. Bien démarrer avec XAML

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Dans une application Xamarin.Forms, XAML est principalement utilisé pour définir le contenu visuel d’une page et fonctionne en association avec un C# fichier code-behind._

Le fichier code-behind prend en charge de code pour le balisage. Ensemble, ces deux fichiers contribuent à une définition de classe qui inclut les vues enfants et l’initialisation des propriétés. Dans le fichier XAML, classes et propriétés sont référencées avec les éléments et attributs XML et des liens entre le balisage et le code sont établies.

## <a name="creating-the-solution"></a>Création de la solution

Pour commencer à modifier votre premier fichier XAML, utilisez Visual Studio ou Visual Studio pour Mac pour créer une solution Xamarin.Forms. (Sélectionnez l’onglet ci-dessous correspondant à votre environnement.)

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Dans Windows, lancez Visual Studio 2019, puis dans la fenêtre Démarrer, cliquez sur **créer un nouveau projet** pour créer un nouveau projet :

![Nouvelle fenêtre de solution](get-started-with-xaml-images/win/new-solution-2019.png)

Dans la fenêtre **créer un nouveau projet** , sélectionnez **mobile** dans la liste déroulante **type de projet** , sélectionnez le modèle **application mobile (Xamarin. Forms)** , puis cliquez sur le bouton **suivant** :

![Fenêtre nouveau projet](get-started-with-xaml-images/win/new-project-2019.png)

Dans la fenêtre **configurer votre nouveau projet** , définissez le **nom du projet** sur **XamlSamples** (ou sur votre choix), puis cliquez sur le bouton **créer** .

Dans la boîte de dialogue **nouvelle application multiplateforme** , cliquez sur **vide**, puis cliquez sur le bouton **OK** :

![Boîte de dialogue nouvelle application](get-started-with-xaml-images/win/new-cross-platform-app.png)

Quatre projets sont créés dans la solution : le **XamlSamples** bibliothèque .NET Standard, **XamlSamples.Android**, **XamlSamples.iOS**et la plateforme Windows universelle solution, **XamlSamples.UWP**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, sélectionnez **fichier > Nouvelle Solution** dans le menu. Dans le **nouveau projet** boîte de dialogue, sélectionnez **multiplateforme > application** à gauche, et **application Forms vide** (*pas* **application Forms** ) à partir de la liste des modèles :

![Boîte de dialogue Nouveau projet 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Appuyez sur **suivant**.

Dans la boîte de dialogue suivante, nommez le projet de **XamlSamples** (ou ce que vous préférez). Assurez-vous que le **utilisez .NET Standard** case d’option est sélectionnée :

![Boîte de dialogue Nouveau projet 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Appuyez sur **suivant**.

Dans la boîte de dialogue suivante, vous pouvez sélectionner un emplacement pour le projet :

![Boîte de dialogue Nouveau projet 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Appuyez sur **créer**

Trois projets sont créés dans la solution : le **XamlSamples** bibliothèque .NET Standard, **XamlSamples.Android**, et **XamlSamples.iOS**.

-----

Après avoir créé le **XamlSamples** solution, vous souhaiterez peut-être tester votre environnement de développement en sélectionnant les divers projets de plateforme en tant que projet de démarrage de la solution et générer et déployer l’application simple créé par le modèle de projet sur les émulateurs de téléphone ou des appareils réels.

Sauf si vous avez besoin d’écrire du code spécifique à la plateforme, l’élément partagé **XamlSamples** projet de bibliothèque .NET Standard est où vous allez passer pratiquement tout votre temps de programmation. Ces articles ne seront pas s’aventurent en dehors de ce projet.

### <a name="anatomy-of-a-xaml-file"></a>Anatomie d’un fichier XAML

Dans le **XamlSamples** bibliothèque .NET Standard sont une paire de fichiers avec les noms suivants :

- **App.XAML**, le fichier XAML ; et
- **App.Xaml.cs**, un C# *code-behind* fichier associé au fichier XAML.

Vous devrez cliquez sur la flèche en regard **App.xaml** pour afficher le fichier code-behind.

Les deux **App.xaml** et **App.xaml.cs** contribuent à une classe nommée `App` qui dérive de `Application`. La plupart des autres classes avec les fichiers XAML contribuent à une classe qui dérive de `ContentPage`; ces fichiers utilisent XAML pour définir le contenu visuel d’une page entière. Cela est vrai pour les deux autres fichiers dans le **XamlSamples** projet :

- **MainPage.xaml**, le fichier XAML ; et
- **MainPage.xaml.cs**, le C# fichier code-behind.

Le **MainPage.xaml** fichier ressemble à ceci (bien que la mise en forme peut être un peu différent) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

Les deux déclarations de l'`xmlns`espace de noms XML () font référence aux URI, la première apparemment sur le site Web de Xamarin et la seconde sur le site Microsoft. Nul besoin de vérifier quel point des ces URI à. Il est vide. Elles sont simplement des URI détenus par Xamarin et Microsoft, et ils fonctionnent essentiellement en tant qu’identificateurs de version.

La première déclaration d’espace de noms XML signifie que les balises définies dans le fichier XAML sans préfixe font référence aux classes dans Xamarin.Forms, par exemple `ContentPage`. La seconde déclaration d’espace de noms définit un préfixe `x`. Il est utilisé pour plusieurs éléments et attributs qui font partie intégrante XAML lui-même et qui sont pris en charge par les autres implémentations de XAML. Toutefois, ces éléments et attributs sont légèrement différents selon l’année incorporée dans l’URI. Xamarin.Forms prend en charge la spécification de XAML 2009, mais pas entièrement.

Le `local` déclaration d’espace de noms permet d’accéder aux autres classes à partir du projet de bibliothèque .NET Standard.

À la fin de cette première balise, le `x` préfixe est utilisé pour un attribut nommé `Class`. Étant donné que l’utilisation de ce `x` préfixe est presque universel pour l’espace de noms XAML, les attributs XAML tel que `Class` sont presque toujours appelés `x:Class`.

Le `x:Class` attribut spécifie un nom qualifié complet de la classe .NET : le `MainPage` classe dans le `XamlSamples` espace de noms. Cela signifie que ce fichier XAML définit une nouvelle classe nommée `MainPage` dans le `XamlSamples` espace de noms qui dérive de `ContentPage`: la balise dans lequel le `x:Class` attribut apparaît.

Le `x:Class` attribut peut uniquement apparaître dans l’élément racine d’un fichier XAML pour définir une dérivée C# classe. Il s’agit de la classe seulement les nouvelles définie dans le fichier XAML. Tout ce qui apparaît dans le fichier XAML est à la place simplement instancié à partir de classes existantes et initialisé.

Le **MainPage.xaml.cs** fichier ressemble à ceci (à part inutilisée `using` directives) :

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

Le `MainPage` dérive de la classe `ContentPage`, mais notez le `partial` définition de classe. Cela suggère qu’il doit y avoir une autre définition de classe partielle pour `MainPage`, mais où est-il ? Et ce qui est qui `InitializeComponent` méthode ?

Lorsque Visual Studio génère le projet, il analyse le fichier XAML pour générer un C# fichier de code. Si vous regardez le **XamlSamples\XamlSamples\obj\Debug** répertoire, vous trouverez un fichier nommé **XamlSamples.MainPage.xaml.g.cs**. Le « g » signifie généré. Voici la définition de classe partielle de `MainPage` qui contient la définition de la `InitializeComponent` méthode appelée à partir de la `MainPage` constructeur. Ces deux partielle `MainPage` définitions de classe peuvent ensuite être compilées ensemble. Selon que le XAML est compilé ou non, le fichier XAML ou une forme binaire du fichier XAML est incorporée dans le fichier exécutable.

Lors de l’exécution de code dans les appels de projet de plateforme en particulier un `LoadApplication` méthode, en lui transmettant une nouvelle instance de la `App` classe dans la bibliothèque .NET Standard. Le `App` instancie du constructeur de classe `MainPage`. Le constructeur de cette classe appelle `InitializeComponent`, qui appelle ensuite la `LoadFromXaml` méthode qui extrait le fichier XAML (ou son fichier binaire compilé) à partir de la bibliothèque .NET Standard. `LoadFromXaml` Initialise tous les objets définis dans le fichier XAML, les connecter ensemble dans des relations parent-enfant, attache les gestionnaires d’événements définis dans le code aux événements dans le fichier XAML et définit l’arborescence résultante des objets en tant que le contenu de la page.

Bien que normalement ne pas devoir passer trop de temps avec les fichiers de code générés, parfois exceptions runtime sont déclenchées sur le code dans les fichiers générés, donc vous devez être familiarisé avec eux.

Lorsque vous compilez et exécutez ce programme, le `Label` élément apparaît dans le centre de la page, comme le suggère le XAML :

[![Affichage Xamarin. Forms par défaut](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Pour les éléments visuels plus intéressants, il vous suffit est plus intéressant de XAML.

## <a name="adding-new-xaml-pages"></a>Ajout de nouvelles Pages XAML

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour ajouter d’autres classes @no__t XAML-0 à votre projet, sélectionnez le projet de bibliothèque **XamlSamples** .NET standard, cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouvel élément...** . Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez  **C# éléments visuels > Xamarin. Forms > page de contenu** (et non pas la page de **contenu (C#)** , qui crée une page de code uniquement, ou un affichage de **contenu**, qui n’est pas une page). Nommez la page, par exemple, **HelloXamlPage**:

![Boîte de dialogue Ajouter un nouvel élément](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour ajouter d’autres XAML en fonction du `ContentPage` classes à votre projet, sélectionnez le **XamlSamples** bibliothèque .NET Standard de projet et d’appeler le **fichier > nouveau fichier** élément de menu. À gauche de la **nouveau fichier** boîte de dialogue, sélectionnez **Forms** à gauche, et **ContentPage Xaml formulaires** (pas **ContentPage formulaires**, qui Crée une page de code uniquement, ou **affichage du contenu**, qui n’est pas une page). Nommez la page, par exemple, **HelloXamlPage**:

![Boîte de dialogue nouveau fichier](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Deux fichiers sont ajoutés au projet, **HelloXamlPage.xaml** et le fichier code-behind **HelloXamlPage.xaml.cs**.

## <a name="setting-page-content"></a>Définition du contenu de Page

Modifier le **HelloXamlPage.xaml** de fichiers afin que les balises uniquement sont celles pour `ContentPage` et `ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

Le `ContentPage.Content` balises font partie de la syntaxe unique de XAML. Dans un premier temps, ils peuvent sembler XML non valide, mais elles sont autorisées. La période n’est pas un caractère spécial dans XML.

Le `ContentPage.Content` balises sont appelés *élément property* balises. `Content` est une propriété de `ContentPage`et est généralement définie pour une vue unique ou une mise en page avec les vues enfants. Normalement propriétés deviennent des attributs dans XAML, mais il serait difficile de définir un `Content` d’attribut à un objet complexe. Pour cette raison, la propriété est exprimée comme un élément XML comprenant le nom de classe et le nom de propriété séparés par un point. Maintenant le `Content` propriété peut être définie entre la `ContentPage.Content` balises, comme suit :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

Notez également qu’un `Title` attribut a été défini sur la balise racine.

À ce stade, la relation entre les classes, les propriétés et le XML doit être évidente : Une classe Xamarin. Forms (telle `ContentPage` que `Label`ou) apparaît dans le fichier XAML sous la forme d’un élément XML. Propriétés de la classe, y compris `Title` sur `ContentPage` et sept propriétés de `Label`— apparaissent généralement sous la forme d’attributs XML.

Il existe de nombreux raccourcis pour définir les valeurs de ces propriétés. Certaines propriétés sont des types de données de base : Par exemple, les `Title` propriétés `Text` et sont de type `String`, `Rotation` est de type `Double`, et `IsVisible` (qui est `true` par défaut et qui est défini ici uniquement pour l’illustration) est de type `Boolean`.

Le `HorizontalTextAlignment` propriété est de type `TextAlignment`, qui est une énumération. Pour une propriété de n’importe quel type d’énumération, il vous suffit d’approvisionnement est un nom de membre.

Pour les propriétés de types plus complexes, toutefois, les convertisseurs sont utilisés pour l’analyse du XAML. Il s’agit de classes dans Xamarin.Forms qui dérivent `TypeConverter`. La plupart sont des classes publiques, mais certains ne sont pas. Pour ce fichier XAML particulier, plusieurs de ces classes jouent un rôle dans les coulisses :

- `LayoutOptionsConverter` pour le `VerticalOptions` propriété
- `FontSizeConverter` pour le `FontSize` propriété
- `ColorTypeConverter` pour le `TextColor` propriété

Ces convertisseurs régissent la syntaxe autorisée des paramètres de propriété.

Le `ThicknessTypeConverter` peut gérer une, deux ou quatre nombres séparés par des virgules. Si un nombre est fourni, elle s’applique à toutes les quatre côtés. Avec deux nombres, la première est left et right remplissage et la seconde est supérieure et inférieure. Quatre nombres sont dans l’ordre gauche, haut, droite et en bas.

Le `LayoutOptionsConverter` peut convertir les noms des champs statiques publics de la `LayoutOptions` structure aux valeurs de type `LayoutOptions`.

Le `FontSizeConverter` peut gérer un `NamedSize` membre ou une taille de police numérique.

Le `ColorTypeConverter` accepte les noms des champs statiques publics de la `Color` structure ou valeurs RVB hexadécimales, avec ou sans un canal alpha, précédé par un signe dièse (#). Voici la syntaxe sans un canal alpha :

 `TextColor="#rrggbb"`

Chacune des lettres peu est un chiffre hexadécimal. Voici comment un canal alpha est inclus :

 `TextColor="#aarrggbb">`

Pour le canal alpha, n’oubliez pas que FF est complètement opaque et 00 est entièrement transparent.

Deux autres formats vous autorise à spécifier uniquement un chiffre hexadécimal unique pour chaque canal :

 `TextColor="#rgb"` `TextColor="#argb"`

Dans ce cas, le chiffre est répété pour former la valeur. Par exemple, #CF3 est la couleur RVB CC-FF-33.

## <a name="page-navigation"></a>Navigation entre les pages

Lorsque vous exécutez le **XamlSamples** programme, le `MainPage` s’affiche. Pour afficher les nouvelles `HelloXamlPage` vous pouvez soit définir qui, comme le démarrage de nouvelle page dans le **App.xaml.cs** de fichiers, ou accédez à la nouvelle page à partir de `MainPage`.

Pour implémenter la navigation, tout d’abord modifier le code dans le **App.xaml.cs** constructeur afin qu’un `NavigationPage` objet est créé :

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

Dans le **MainPage.xaml.cs** constructeur, vous pouvez créer une simple `Button` et utilisez le Gestionnaire d’événements pour accéder à `HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

Définition de la `Content` propriété de la page remplace le paramètre de la `Content` propriété dans le fichier XAML. Lorsque vous compilez et déployez la nouvelle version de ce programme, un bouton apparaît sur l’écran. En appuyant sur elle accède à `HelloXamlPage`. Voici la page résultante sur iPhone, Android et UWP :

[![Texte de l’étiquette pivoté](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

Vous pouvez naviguer jusqu'à `MainPage` à l’aide de la **< retour** bouton sur iOS, à l’aide de la flèche gauche en haut de la page ou du bas du téléphone sur Android, ou à l’aide de la flèche gauche en haut de la page sur Windows 10.

N’hésitez pas à faire des essais avec le XAML pour les différentes façons de rendre le `Label`. Si vous avez besoin d’incorporer tous les caractères Unicode dans le texte, vous pouvez utiliser la syntaxe XML standard. Par exemple, pour mettre le message d’accueil dans des guillemets, utilisez :

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Voici à quoi elle ressemble :

[![Texte d’étiquette pivoté avec des caractères Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML et les Interactions du Code

Le **HelloXamlPage** exemple contient une seule `Label` dans la page, mais cela est très rare. La plupart des `ContentPage` dérivés ensemble la `Content` trier de propriété à une disposition de certaines, comme un `StackLayout`. Le `Children` propriété de la `StackLayout` est défini comme étant de type `IList<View>` , mais il est en fait un objet de type `ElementCollection<View>`, et que la collection peut être remplie avec plusieurs vues ou d’autres présentations. Dans XAML, ces relations parent-enfant sont établies avec la hiérarchie XML classique. Voici un fichier XAML pour une page nommée **XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Ce fichier XAML est syntaxiquement complète, et Voici à quoi elle ressemble :

[![Plusieurs contrôles sur une page](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

Toutefois, vous êtes susceptible de prendre en compte ce programme fonctionnellement insuffisantes. Peut-être le `Slider` est censée pour entraîner le `Label` pour afficher la valeur actuelle et le `Button` est probablement destiné à faire quelque chose dans le programme.

Comme vous le verrez dans [partie 4. Notions de base de données liaison](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md), le travail d’affichage une `Slider` à l’aide de la valeur un `Label` peut être géré entièrement dans XAML avec une liaison de données. Mais il est utile de consulter tout d’abord de la solution de code. Même dans ce cas, la gestion du `Button` cliquez sans aucun doute nécessite du code. Cela signifie que le fichier code-behind pour `XamlPlusCodePage` doit contenir des gestionnaires pour le `ValueChanged` événements de la `Slider` et le `Clicked` événements de la `Button`. Nous allons ajouter les :

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

Ces gestionnaires d’événements n’avez pas besoin être public.

Dans le fichier XAML, le `Slider` et `Button` balises besoin d’inclure des attributs pour le `ValueChanged` et `Clicked` les événements qui font référence à ces gestionnaires :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

Notez que l’affectation d’un gestionnaire à un événement a la même syntaxe que l’attribution d’une valeur à une propriété.

Si le gestionnaire pour le `ValueChanged` événements de la `Slider` utilisera le `Label` pour afficher la valeur actuelle, le gestionnaire doit référencer cet objet à partir du code. Le `Label` a besoin d’un nom, qui est spécifié avec la `x:Name` attribut.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

Le `x` préfixe de le `x:Name` attribut indique que cet attribut est intrinsèque à XAML.

Le nom que vous affectez à la `x:Name` attribut a les mêmes règles que C# les noms de variables. Par exemple, il doit commencer par une lettre ou un trait de soulignement et contenir aucun espace incorporé.

Maintenant le `ValueChanged` Gestionnaire d’événements peut définir le `Label` pour afficher la nouvelle `Slider` valeur. La nouvelle valeur est disponible à partir des arguments d’événement :

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

Ou, le gestionnaire peut obtenir le `Slider` objet qui génère cet événement à partir de la `sender` argument et obtenir le `Value` propriété à partir de qui :

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Lorsque vous exécutez tout d’abord le programme, le `Label` n’affiche pas le `Slider` valeur, car le `ValueChanged` événement n’a pas encore déclenché. Mais toute manipulation de la `Slider` provoque la valeur à afficher :

[![Valeur de curseur affichée](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Passons maintenant à la `Button`. Nous allons simuler une réponse à une `Clicked` événement en affichant une alerte avec le `Text` du bouton. Le Gestionnaire d’événements peut sans risque un cast du `sender` argument à un `Button` , puis accédez à ses propriétés :

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

La méthode est définie en tant que `async` , car le `DisplayAlert` méthode est asynchrone et doit faire précéder le `await` opérateur, qui renvoie à l’issue de la méthode. Étant donné que cette méthode obtient le `Button` déclenchant l’événement à partir de la `sender` argument, le même gestionnaire peut être utilisé pour plusieurs boutons.

Vous avez vu qu’un objet défini en XAML peut déclencher un événement qui est géré dans le fichier code-behind, et que le fichier code-behind peut accéder à un objet défini en XAML en utilisant le nom qui lui est assigné avec la `x:Name` attribut. Il s’agit de deux façons fondamentales qui interagissent de code et XAML.

Des informations supplémentaires sur comment XAML works peuvent être collectés en examinant nouvellement généré **XamlPlusCode.xaml.g.cs fichier**, qui comprend désormais n’importe quel nom assigné à aucun `x:Name` attribut sous la forme d’un champ privé. Voici une version simplifiée de ce fichier :

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La déclaration de ce champ permet à la variable à librement utiliser n’importe où dans le `XamlPlusCodePage` fichier de classe partielle dans votre juridiction. Lors de l’exécution, le champ est assigné une fois que le XAML a été analysé. Cela signifie que le `valueLabel` champ est `null` lorsque le `XamlPlusCodePage` constructeur commence mais valide après `InitializeComponent` est appelée.

Après avoir `InitializeComponent` rend le contrôle au constructeur, les éléments visuels de la page ont été construits comme s’ils avaient été instanciés et initialisés dans le code. Le fichier XAML ne peut plus lire de n’importe quel rôle dans la classe. Vous pouvez manipuler ces objets dans la page en aucune façon souhaitée, par exemple, en ajoutant des vues pour la `StackLayout`, ou paramètre la `Content` propriété de la page pour autre chose entièrement. Vous pouvez « parcourir l’arborescence » en examinant le `Content` propriété de la page et les éléments dans le `Children` collections de dispositions. Vous pouvez définir des propriétés sur les vues accédés de cette manière, ou leur affecter dynamiquement des gestionnaires d’événements.

N’hésitez pas. Il s’agit votre page et XAML est uniquement un outil permettant de créer son contenu.

## <a name="summary"></a>Récapitulatif

Avec cette présentation, vous avez vu comment un fichier XAML et le fichier de code contribuent à une définition de classe, et comment les fichiers XAML et code interagissent. Mais XAML possède également ses propres fonctionnalités syntaxiques uniques qui lui permettent d’être utilisé de manière très souple. Vous pouvez commencer à Explorer dans [partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Liens associés

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Notions de base sur la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. À partir de la liaison de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
