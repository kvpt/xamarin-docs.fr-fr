---
title: Partie 1. Bien démarrer avec XAML
description: Dans une Xamarin.Forms application, XAML est principalement utilisé pour définir le contenu visuel d’une page et fonctionne avec un fichier code-behind.
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8992f2a649b1d3998a24a11cdf7e51d768a3cd09
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371337"
---
# <a name="part-1-getting-started-with-xaml"></a>Partie 1. Bien démarrer avec XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Dans une Xamarin.Forms application, XAML est principalement utilisé pour définir le contenu visuel d’une page et fonctionne avec un fichier code-behind C#._

Le fichier code-behind fournit la prise en charge du code pour le balisage. Ensemble, ces deux fichiers contribuent à une nouvelle définition de classe qui comprend des vues enfants et l’initialisation des propriétés. Dans le fichier XAML, les classes et les propriétés sont référencées avec des éléments et des attributs XML, et les liens entre le balisage et le code sont établis.

## <a name="creating-the-solution"></a>Création de la solution

Pour commencer à modifier votre premier fichier XAML, utilisez Visual Studio ou Visual Studio pour Mac pour créer une nouvelle Xamarin.Forms solution. (Sélectionnez l’onglet ci-dessous correspondant à votre environnement.)

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Dans Windows, lancez Visual Studio 2019, puis dans la fenêtre Démarrer, cliquez sur **créer un nouveau projet** pour créer un nouveau projet :

![Nouvelle fenêtre de solution](get-started-with-xaml-images/win/new-solution-2019.png)

Dans la fenêtre **créer un nouveau projet** , sélectionnez **mobile** dans la liste déroulante **type de projet** , sélectionnez le modèle **application mobile ( Xamarin.Forms )** , puis cliquez sur le bouton **suivant** :

![Fenêtre nouveau projet](get-started-with-xaml-images/win/new-project-2019.png)

Dans la fenêtre **configurer votre nouveau projet** , définissez le **nom du projet** sur **XamlSamples** (ou sur votre choix), puis cliquez sur le bouton **créer** .

Dans la boîte de dialogue **nouvelle application multiplateforme** , cliquez sur **vide** , puis cliquez sur le bouton **OK** :

![Boîte de dialogue nouvelle application](get-started-with-xaml-images/win/new-cross-platform-app.png)

Quatre projets sont créés dans la solution : la bibliothèque **XamlSamples** .NET standard, **XamlSamples. Android** , **XamlSamples. iOS** et la solution plateforme Windows universelle, **XamlSamples. UWP**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Dans Visual Studio pour Mac, sélectionnez **fichier > nouvelle solution** dans le menu. Dans la boîte de dialogue **nouveau projet** , sélectionnez **application multiplateforme > application** à gauche, et **application Formulaires vide** (application *non* **Forms** ) dans la liste des modèles :

![Boîte de dialogue Nouveau projet 1](get-started-with-xaml-images/mac/newprojectdialog1.png)

Appuyez sur **suivant**.

Dans la boîte de dialogue suivante, donnez au projet le nom **XamlSamples** (ou tout ce que vous préférez). Assurez-vous que la case d’option **utiliser .NET standard** est sélectionnée :

![Boîte de dialogue Nouveau projet 2](get-started-with-xaml-images/mac/newprojectdialog2.png)

Appuyez sur **suivant**.

Dans la boîte de dialogue suivante, vous pouvez sélectionner un emplacement pour le projet :

![Boîte de dialogue Nouveau projet 3](get-started-with-xaml-images/mac/newprojectdialog3.png)

Appuyez sur **créer**

Trois projets sont créés dans la solution : **XamlSamples** .NET standard Library, **XamlSamples. Android** et **XamlSamples. iOS**.

-----

Après avoir créé la solution **XamlSamples** , vous pouvez tester votre environnement de développement en sélectionnant les différents projets de plateforme en tant que projet de démarrage de la solution, et en générant et en déployant l’application simple créée par le modèle de projet sur les émulateurs de téléphone ou les appareils réels.

À moins que vous n’ayez besoin d’écrire du code spécifique à la plateforme, le projet de bibliothèque **XamlSamples** .NET standard partagé est l’endroit où vous allez consacrer presque tout votre temps de programmation. Ces articles ne seront pas en dehors de ce projet.

### <a name="anatomy-of-a-xaml-file"></a>Anatomie d’un fichier XAML

Dans la bibliothèque de .NET Standard **XamlSamples** se trouvent une paire de fichiers avec les noms suivants :

- **App. Xaml** , le fichier XAML ; les
- **App.Xaml.cs** , fichier *code-behind* C# associé au fichier XAML.

Vous devez cliquer sur la flèche en regard de **app. Xaml** pour afficher le fichier code-behind.

**App. Xaml** et **app.Xaml.cs** contribuent à une classe nommée `App` qui dérive de `Application` . La plupart des autres classes avec des fichiers XAML contribuent à une classe qui dérive de `ContentPage` ; ces fichiers utilisent XAML pour définir le contenu visuel d’une page entière. Cela est vrai pour les deux autres fichiers dans le projet **XamlSamples** :

- **MainPage. Xaml** , le fichier XAML ; les
- **MainPage.Xaml.cs** , le fichier code-behind C#.

Le fichier **MainPage. Xaml** ressemble à ceci (même si la mise en forme peut être un peu différente) :

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

Les deux déclarations de l’espace de noms XML ( `xmlns` ) font référence aux URI, la première apparemment sur le site Web de Xamarin et la seconde sur le site Microsoft. Ne vous inquiétez pas de vérifier à quoi ces URI pointent. Il n’y a rien. Il s’agit simplement d’URI appartenant à Xamarin et Microsoft, qui fonctionnent en tant qu’identificateurs de version.

La première déclaration d’espace de noms XML signifie que les balises définies dans le fichier XAML sans préfixe font référence aux classes dans Xamarin.Forms , par exemple `ContentPage` . La deuxième déclaration d’espace de noms définit un préfixe de `x` . Cela est utilisé pour plusieurs éléments et attributs qui sont intrinsèques au XAML lui-même et qui sont pris en charge par d’autres implémentations de XAML. Toutefois, ces éléments et attributs sont légèrement différents selon l’année incorporée dans l’URI. Xamarin.Forms prend en charge la spécification XAML 2009, mais pas la totalité.

La `local` déclaration d’espace de noms vous permet d’accéder à d’autres classes à partir du projet de bibliothèque .NET standard.

À la fin de cette première balise, le `x` préfixe est utilisé pour un attribut nommé `Class` . Étant donné que l’utilisation de ce `x` préfixe est pratiquement universelle pour l’espace de noms XAML, les attributs XAML, tels que, `Class` sont presque toujours connus sous le nom de `x:Class` .

L' `x:Class` attribut spécifie un nom de classe .net complet : la `MainPage` classe de l' `XamlSamples` espace de noms. Cela signifie que ce fichier XAML définit une nouvelle classe nommée `MainPage` dans l' `XamlSamples` espace de noms qui dérive de `ContentPage` , la balise dans laquelle l' `x:Class` attribut apparaît.

L' `x:Class` attribut peut uniquement apparaître dans l’élément racine d’un fichier XAML pour définir une classe C# dérivée. Il s’agit de la seule nouvelle classe définie dans le fichier XAML. Tout le reste qui apparaît dans le fichier XAML est simplement instancié à partir des classes existantes et initialisé.

Le fichier **MainPage.Xaml.cs** ressemble à ce qui suit (à part les directives inutilisées `using` ) :

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

La `MainPage` classe dérive de `ContentPage` , mais notez la `partial` définition de classe. Cela suggère qu’il doit y avoir une autre définition de classe partielle pour `MainPage` , mais où est-ce ? Et qu’est-ce que cette `InitializeComponent` méthode ?

Lorsque Visual Studio génère le projet, il analyse le fichier XAML pour générer un fichier de code C#. Si vous regardez dans le répertoire **XamlSamples\XamlSamples\obj\Debug** , vous trouverez un fichier nommé **XamlSamples.MainPage.Xaml.g.cs**. Le « g » signifie généré. Il s’agit de l’autre définition de classe partielle de `MainPage` qui contient la définition de la `InitializeComponent` méthode appelée à partir du `MainPage` constructeur. Ces deux définitions de classe partielles `MainPage` peuvent ensuite être compilées ensemble. Selon que le XAML est compilé ou non, le fichier XAML ou une forme binaire du fichier XAML est incorporé dans l’exécutable.

Lors de l’exécution, le code dans le projet de plateforme particulier appelle une `LoadApplication` méthode, en lui transmettant une nouvelle instance de la `App` classe dans la bibliothèque de .NET standard. Le `App` constructeur de classe instancie `MainPage` . Le constructeur de cette classe appelle `InitializeComponent` , qui appelle ensuite la `LoadFromXaml` méthode qui extrait le fichier XAML (ou son fichier binaire compilé) de la bibliothèque de .NET standard. `LoadFromXaml` Initialise tous les objets définis dans le fichier XAML, les connecte tous ensemble dans les relations parent-enfant, attache les gestionnaires d’événements définis dans le code aux événements définis dans le fichier XAML et définit l’arborescence résultante d’objets en tant que contenu de la page.

Même si vous n’avez généralement pas besoin de consacrer beaucoup de temps aux fichiers de code générés, des exceptions Runtime sont parfois générées sur le code dans les fichiers générés. vous devez donc être familiarisé avec eux.

Quand vous compilez et exécutez ce programme, l' `Label` élément apparaît au centre de la page comme le suggère le XAML :

[![Default ::: No-Loc (Xamarin. Forms) ::: Display](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

Pour les visuels plus intéressants, il vous suffit d’un XAML plus intéressant.

## <a name="adding-new-xaml-pages"></a>Ajout de nouvelles pages XAML

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour ajouter d’autres classes basées sur XAML `ContentPage` à votre projet, sélectionnez le projet de bibliothèque **XamlSamples** .NET standard, cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouvel élément...**. Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **éléments Visual C# > Xamarin.Forms > page de contenu** (et non pas la **page de contenu (C#)** , qui crée une page de code uniquement, ou un **affichage de contenu** , qui n’est pas une page). Donnez un nom à la page, par exemple **HelloXamlPage** :

![Boîte de dialogue Ajouter un nouvel élément](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour ajouter d’autres classes basées sur XAML `ContentPage` à votre projet, sélectionnez le projet de bibliothèque **XamlSamples** .NET standard et appelez le **fichier > élément de menu nouveau fichier** . À gauche de la boîte de dialogue **nouveau fichier** , sélectionnez **formulaires** à gauche, et **Forms ContentPage XAML** (et non pas **Forms ContentPage** , qui crée une page de code uniquement, ou **affichage de contenu** , qui n’est pas une page). Donnez un nom à la page, par exemple **HelloXamlPage** :

![Boîte de dialogue nouveau fichier](get-started-with-xaml-images/mac/newfiledialog.png)

-----

Deux fichiers sont ajoutés au projet, **HelloXamlPage. Xaml** et le fichier code-behind **HelloXamlPage.Xaml.cs**.

## <a name="setting-page-content"></a>Définir le contenu de la page

Modifiez le fichier **HelloXamlPage. Xaml** afin que les seules balises soient celles `ContentPage` pour `ContentPage.Content` et :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

Les `ContentPage.Content` balises font partie de la syntaxe unique de XAML. Dans un premier temps, ils peuvent sembler être du code XML non valide, mais ils sont légaux. Le point n’est pas un caractère spécial en XML.

Les `ContentPage.Content` balises sont appelées balises d' *élément de propriété* . `Content` est une propriété de `ContentPage` , et est généralement défini sur une vue unique ou une disposition avec des vues enfants. Normalement, les propriétés deviennent des attributs en XAML, mais il serait difficile de définir un `Content` attribut sur un objet complexe. Pour cette raison, la propriété est exprimée sous la forme d’un élément XML constitué du nom de la classe et du nom de la propriété, séparés par un point. À présent `Content` , la propriété peut être définie entre les `ContentPage.Content` balises, comme suit :

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

À ce stade, la relation entre les classes, les propriétés et le XML doit être évidente : une Xamarin.Forms classe (telle que `ContentPage` ou `Label` ) apparaît dans le fichier XAML sous la forme d’un élément XML. Les propriétés de cette classe, y compris les `Title` `ContentPage` Propriétés on et sept de `Label` , apparaissent généralement sous la forme d’attributs XML.

De nombreux raccourcis existent pour définir les valeurs de ces propriétés. Certaines propriétés sont des types de données de base : par exemple, les `Title` `Text` Propriétés et sont de type `String` , `Rotation` est de type `Double` , et `IsVisible` (qui est `true` par défaut et qui est défini ici uniquement pour l’illustration) est de type `Boolean` .

La `HorizontalTextAlignment` propriété est de type `TextAlignment` , qui est une énumération. Pour une propriété d’un type énumération, il vous suffit de fournir un nom de membre.

Toutefois, pour les propriétés de types plus complexes, les convertisseurs sont utilisés pour l’analyse du code XAML. Il s’agit de classes dans Xamarin.Forms qui dérivent de `TypeConverter` . De nombreux sont des classes publiques, mais d’autres ne le sont pas. Pour ce fichier XAML particulier, plusieurs de ces classes jouent un rôle en arrière-plan :

- `LayoutOptionsConverter` pour la  `VerticalOptions` propriété
- `FontSizeConverter` pour la  `FontSize` propriété
- `ColorTypeConverter` pour la  `TextColor` propriété

Ces convertisseurs gouvernent la syntaxe autorisée des paramètres de propriété.

Le `ThicknessTypeConverter` peut gérer un, deux ou quatre nombres séparés par des virgules. Si un nombre est fourni, il s’applique aux quatre côtés. Avec deux nombres, le premier est gauche et droit, et le deuxième est supérieur et inférieur. Quatre nombres sont dans l’ordre à gauche, en haut, à droite et en bas.

`LayoutOptionsConverter`Peut convertir les noms des champs statiques publics de la `LayoutOptions` structure en valeurs de type `LayoutOptions` .

`FontSizeConverter`Peut gérer un `NamedSize` membre ou une taille de police numérique.

Le `ColorTypeConverter` accepte les noms des champs statiques publics de la `Color` structure ou des valeurs RVB hexadécimales, avec ou sans canal alpha, précédé d’un signe dièse (#). Voici la syntaxe sans canal alpha :

 `TextColor="#rrggbb"`

Chacune des petites lettres est un chiffre hexadécimal. Voici comment un canal alpha est inclus :

 `TextColor="#aarrggbb">`

Pour le canal alpha, gardez à l’esprit que FF est entièrement opaque et 00 est entièrement transparent.

Deux autres formats vous permettent de spécifier un seul chiffre hexadécimal pour chaque chaîne :

 `TextColor="#rgb"` `TextColor="#argb"`

Dans ces cas-là, le chiffre est répété pour former la valeur. Par exemple, #CF3 est la couleur RVB CC-FF-33.

## <a name="page-navigation"></a>Navigation entre les pages

Lorsque vous exécutez le programme **XamlSamples** , le `MainPage` s’affiche. Pour voir le nouveau `HelloXamlPage` , vous pouvez soit le définir en tant que nouvelle page de démarrage dans le fichier **app.Xaml.cs** , soit accéder à la nouvelle page à partir de `MainPage` .

Pour implémenter la navigation, commencez par modifier le code dans le constructeur **app.Xaml.cs** afin qu’un `NavigationPage` objet soit créé :

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

Dans le constructeur **MainPage.Xaml.cs** , vous pouvez créer un simple `Button` et utiliser le gestionnaire d’événements pour accéder à `HelloXamlPage` :

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

La définition de la `Content` propriété de la page remplace le paramètre de la `Content` propriété dans le fichier XAML. Quand vous compilez et déployez la nouvelle version de ce programme, un bouton s’affiche à l’écran. Le fait d’appuyer sur cette touche permet d’accéder à `HelloXamlPage` . Voici la page résultante sur iPhone, Android et UWP :

[![Texte de l’étiquette pivoté](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

Vous pouvez revenir à `MainPage` l’utilisation du bouton **< précédent** sur iOS, en utilisant la flèche gauche en haut de la page ou en bas du téléphone sur Android, ou en utilisant la flèche gauche en haut de la page sur Windows 10.

N’hésitez pas à expérimenter le XAML pour différentes façons d’afficher le `Label` . Si vous devez incorporer des caractères Unicode dans le texte, vous pouvez utiliser la syntaxe XML standard. Par exemple, pour placer le message d’accueil dans des guillemets typographiques, utilisez :

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

Voici à quoi elle ressemble :

[![Texte d’étiquette pivoté avec des caractères Unicode](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML et interactions de code

L’exemple **HelloXamlPage** contient un seul `Label` sur la page, ce qui est très rare. La plupart des `ContentPage` dérivées définissent la `Content` propriété sur une disposition d’un certain type, tel qu’un `StackLayout` . La `Children` propriété de `StackLayout` est définie comme étant de type `IList<View>` , mais il s’agit en fait d’un objet de type `ElementCollection<View>` , et cette collection peut être remplie avec plusieurs vues ou d’autres dispositions. En XAML, ces relations parent-enfant sont établies avec une hiérarchie XML normale. Voici un fichier XAML pour une nouvelle page nommée **XamlPlusCodePage** :

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

Ce fichier XAML est syntaxiquement complet et voici à quoi il ressemble :

[![Plusieurs contrôles sur une page](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

Toutefois, il est probable que vous considériez ce programme comme étant déficient. Peut `Slider` -être est-il supposé entraîner l' `Label` affichage de la valeur actuelle par le, et `Button` est probablement prévu pour effectuer une opération dans le programme.

Comme vous le verrez dans la [partie 4. Les principes de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)de la liaison de données, le travail d’affichage d’une `Slider` valeur à l’aide d’un `Label` peut être géré entièrement en XAML avec une liaison de données. Mais il est utile de consulter d’abord la solution de code. Même dans ce cas, le fait de gérer le `Button` Code de clic requiert un code. Cela signifie que le fichier code-behind pour `XamlPlusCodePage` doit contenir des gestionnaires pour l' `ValueChanged` événement de `Slider` et l' `Clicked` événement de `Button` . Nous allons les ajouter :

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

Ces gestionnaires d’événements n’ont pas besoin d’être publics.

De retour dans le fichier XAML, `Slider` les `Button` balises et doivent inclure des attributs pour les `ValueChanged` `Clicked` événements et qui font référence à ces gestionnaires :

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

Notez que l’affectation d’un gestionnaire à un événement a la même syntaxe que l’affectation d’une valeur à une propriété.

Si le gestionnaire de l' `ValueChanged` événement du `Slider` utilise `Label` pour afficher la valeur actuelle, le gestionnaire doit référencer cet objet à partir du code. Le `Label` requiert un nom, qui est spécifié avec l' `x:Name` attribut.

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

Le `x` préfixe de l' `x:Name` attribut indique que cet attribut est intrinsèque au XAML.

Le nom que vous affectez à l' `x:Name` attribut a les mêmes règles que les noms de variables C#. Par exemple, il doit commencer par une lettre ou un trait de soulignement et ne contenir aucun espace incorporé.

À présent `ValueChanged` , le gestionnaire d’événements peut définir le `Label` pour afficher la nouvelle `Slider` valeur. La nouvelle valeur est disponible à partir des arguments de l’événement :

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

Sinon, le gestionnaire peut obtenir l' `Slider` objet qui génère cet événement à partir de l' `sender` argument et obtenir la `Value` propriété de ce qui suit :

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

Lorsque vous exécutez le programme pour la première fois, le `Label` n’affiche pas la `Slider` valeur, car l' `ValueChanged` événement n’a pas encore été déclenché. Toutefois, toute manipulation du `Slider` provoque l’affichage de la valeur :

[![Valeur de curseur affichée](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

Maintenant pour le `Button` . Nous allons simuler une réponse à un `Clicked` événement en affichant une alerte avec le `Text` du bouton. Le gestionnaire d’événements peut effectuer un cast `sender` en toute sécurité de l’argument en `Button` , puis accéder à ses propriétés :

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

La méthode est définie comme `async` parce que la `DisplayAlert` méthode est asynchrone et doit être précédée de l' `await` opérateur, qui retourne à la fin de la méthode. Étant donné que cette méthode obtient le `Button` déclenchement de l’événement à partir de l' `sender` argument, le même gestionnaire peut être utilisé pour plusieurs boutons.

Vous avez vu qu’un objet défini en XAML peut déclencher un événement qui est géré dans le fichier code-behind et que le fichier code-behind peut accéder à un objet défini en XAML à l’aide du nom qui lui est assigné avec l' `x:Name` attribut. Il s’agit des deux méthodes fondamentales d’interaction entre code et XAML.

Vous pouvez obtenir des informations supplémentaires sur le fonctionnement de XAML en examinant le **fichier XamlPlusCode.Xaml.g.cs** qui vient d’être généré, qui comprend désormais tout nom affecté à un `x:Name` attribut en tant que champ privé. Voici une version simplifiée de ce fichier :

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

La déclaration de ce champ permet à la variable d’être utilisée librement n’importe où dans le `XamlPlusCodePage` fichier de classe partielle sous votre juridiction. Au moment de l’exécution, le champ est assigné après l’analyse du code XAML. Cela signifie que le `valueLabel` champ est `null` lorsque le `XamlPlusCodePage` constructeur commence mais valide après l' `InitializeComponent` appel de.

Après le `InitializeComponent` retour du contrôle au constructeur, les visuels de la page ont été construits comme s’ils avaient été instanciés et initialisés dans le code. Le fichier XAML ne joue plus aucun rôle dans la classe. Vous pouvez manipuler ces objets sur la page comme vous le souhaitez, par exemple, en ajoutant des vues au ou en `StackLayout` affectant à la `Content` propriété de la page une autre valeur. Vous pouvez « parcourir l’arborescence » en examinant la `Content` propriété de la page et les éléments dans les `Children` collections de dispositions. Vous pouvez définir des propriétés sur les vues accessibles de cette façon ou leur assigner des gestionnaires d’événements de manière dynamique.

N’hésitez pas. C’est votre page et XAML est un outil qui permet de générer son contenu.

## <a name="summary"></a>Récapitulatif

Avec cette introduction, vous avez vu comment un fichier de code et un fichier XAML contribuent à une définition de classe, et comment les fichiers XAML et de code interagissent. Toutefois, XAML possède également ses propres fonctionnalités syntaxiques uniques qui lui permettent d’être utilisé de manière très flexible. Vous pouvez commencer à les explorer dans la [partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md).

## <a name="related-links"></a>Liens associés

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. De la liaison de données au MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)